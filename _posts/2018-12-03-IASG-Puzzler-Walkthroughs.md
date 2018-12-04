---
layout: post
title: Information Assurance Student Group Puzzler Walkthroughs - Fall 2018 
---

As a longtime member of the Information Assurance Student Group (IASG) at Iowa
State, I've made it a goal to solve all of the CTF puzzlers created by the
cabinet for club members to solve [(shoutout to Nic Losby for making these
puzzlers for the club)](https://www.twitter.com/blurbdust). After receiving the blessing of the cabinet
to publish these answers, I wrote up my thought processes and solutions to all of
the puzzlers published throughout the fall of 2018. Be warned: while I did
eventually solve all of the puzzles, I definitely am not an authority on CTFs,
as you'll doubtless realize when you see my convoluted and inefficient thought
processes. That being said, I had a lot of fun solving these, and [I definitely
recommend that you try them on your own before reading my solutions.](https://iasg.iac.iastate.edu/puzzlers) Without any further ado:

# 1. Easy Steganography 

![Easy Steganography](/images/EasyStego.png)

With this image and the knowledge that this should be an easy steganography
puzzle, there's a few obvious places to start. Of course, upon downloading the
image, I like to run `file` and make sure that I know what kind of file I'm
dealing with.

```bash
$ file easy.png
easy.png: PNG image data, 600 x 315, 8-bit/color RGB, non-interlaced
```
Okay, so it's a PNG image, looks standard enough. Now, I know that it's a
classic CTF puzzle to smash together a zip and a PNG, so I decide to hit it with
a personal favorite tool, `foremost`, to look for additional file headers. (For
those of you playing along at home, `foremost` should be available in your
package manager's default repository. If not, ~~Google~~ DuckDuckGo is your
friend.) 
```bash
$ foremost easy.png
$ cat output/audit.txt
[REDACTED FOR LENGTH]
1 FILES EXTRACTED

png:= 1
[REDACTED FOR LENGTH]
``` 
Okay, so this tells us that there's no other file headers in there, so there
must be something special about the image data itself. Here's a crazy idea:
let's just search the image data for the flag.
```bash
$ strings easy.png | grep iasg
iasg{strings_is_your_best_friend_but_its_not_sad_because_your_computer_is_too}
```
Wow, I guess it really was that easy. Moral of the story, run `strings` on every
possible stego challenge if you want to save yourself a lot of time. In this
case, the flag was hidden by placing it after the "IEND" tag in the PNG data,
causing image viewers to ignore it, but strings obviously had no trouble finding
it.

# 2. Medium Steganography

![Medium Steganography](/images/MediumStego.png)

Alright, another stego challenge, but this one is supposed to be a bit harder.
While the massive "XOR" edited into this image seems like a useful clue, it
seems prudent to start with the basics.
Having learned my lesson, I started with `strings`, hoping to find the "iasg"
string indicating the flag, but with no luck. However, the output from `strings`
was interesting, and is certainly worth investigating. (I'm going to truncate
the output with `tail` because most of the output is gibberish that isn't
relevant to solving the puzzle.)
```bash
$ strings medium.png | tail -n 15
IEND
BEUAAAIA
AAAIA]A'- &o595
HABk
49JA@E
BAAE%AAA\
2afBH
@C_BUAAAIA
AAAIAYAAAAA@AAA
AAAA'- &o595
DABk
49JA@E
BAAE%AAA
DGAAAA@A@A
AAAAA
```

Interestingly, we see the IEND tag marking the end of the image, and following
it we see a large jumble of random characters with lots of 'A's showing up. Time
to fire up another great tool (that should be available every community
repository), the hex editor known as "Bless". After opening up the image in
bless, here's what we see:

![bless](/images/bless.png)

Alright, so let's take stock of the situation. We have a bunch of random
characters that obviously mean something and a massive "XOR" staring us in the
face. I'm not ashamed to admit that my first thought here was to XOR this entire
image agaisnt the previous image, bit by bit, thinking that because they looked
so similar and came out back-to-back this could be worthwhile. I won't even get
into how I did this, as that ended up being completely incorrect. Let's focus on
those weird characters.

...

I have no idea what to do.

So, let's use yet *another* favorite tool,
[CyberChef](https://gchq.github.io/CyberChef/) (which, I just found out, is
maintained by GCHQ. Not sure how I feel about that.) We'll set it up with the
"XOR Brute Force" tool, which allows us to see a brief preview of the output of
XOR-ing our input text against lots of keys at once.

![CyberChef](/images/CyberChef.png)

While this might look like it's generating a whole lot of useless characters, it
actually, well, yeah. It is. The vast majority of these keys are going to give
us garbage. However, if we start searching through the output for a string such
as "iasg" or "flag", we find something interesting. With the key "41", we see
that the output will contain "flag.txt" along with the "PK" signature of a zip
file. With this knowledge, we can set CyberChef to run a standard XOR operation
against this data with the key "41", resulting in the following output:

![XOR Chef](/images/chef2.png)

Well, that's definitely something. It would appear that the flag is hidden in
"flag.txt" which has been zipped up to prevent us from reading it now. Well,
let's try saving this data and opening it as a zip archive. I simply turned this
text into hex and did a quick copy-paste job into a new file in Bless, naming it
"flag.zip". Afterwards, all we have to do is hit it with a quick `unzip`, and
Bob's your uncle.

```bash
$ unzip flag.txt
Archive:  flag.zip
  inflating: flag.txt
$ cat flag.txt
iasg{xor_is_bad_for_crypto_but_can_be_fun_for_hiding_things_like_bodies}
```
Thanks for that nice little message, Nic.


# 3. Easyish Steganography

[For this challenge, we are given a sound file named
easy.wav.](https://iasg.iac.iastate.edu/puzzlers/easy.wav) In previous CTFs and
puzzlers, I've seen things like Morse code and DTMF (Dual-Tone Multi-Frequency,
think phone button tones) encoded numbers, but upon playing this sound file I'm
met with indecipherable noise. While I don't want to write off the
sound itself as irrelevant, I first want to check the metadata and other
attributes of the file. I open it up in VLC again (but mute the volume) and
check the track information. Nothing is hidden in the artist, album, tags, or
anything like that. I then run `strings`, `file`, and `foremost`, looking for
anything obvious, but nothing is standing out. This is about when I realized
that giving us a file filled with seemingly random, unimportant tones
is a perfect cover for some good ol'
least significant bit stego, since you can hide just about anything in here
and it'll just continue to sound strange. Time to break out some "real"
stego tools, specifically [Deepsound](http://www.jpinsoft.net/DeepSound/) and
[Steghide](http://steghide.sourceforge.net/). Steghide tends to be more popular
in CTFs, so let's use it first. I know Steghide is going to ask for a password,
but I have no idea what to use. Let's go with... no password at all. 

```bash
$ steghide extract -sf easy.wav
Enter passphrase:
wrote extracted data to "flag.txt".
$ cat flag.txt
iasg{im_hoping_i_will_here_a_ton_of_people_playing_this_during_the_meeting}
```

Wow, that was surprisingly straightforward. I guess it's a good reminder that
just mashing enter on the password field sometimes works, just because people
don't like remembering passwords. [And sometimes it works because Apple has an
interesting idea for a password-less
future.](https://twitter.com/lemiorhan/status/935581020774117381)

# 4. Hard Steganography 

[Another sound file filled with seemingly nothing of importance.
Joy.](https://iasg.iac.iastate.edu/puzzlers/hard.wav)

After the standard gauntlet of `file`, `foremost`, `strings`, etc., I hit this
one with Steghide and no password to no avail. After harassing the creator of
these puzzles about not making progress for a while, he mentioned that this
puzzler is very similar to the previous one, but with a password. Great.
Fortunately, I know that most CTFs that include password brute forcing try to
pull from the famous [rockyou.txt](https://wiki.skullsecurity.org/Passwords) to
make things semi-solvable for the contestants. I set up a little script to pull
lines from rockyou.txt and bounce them against hard.wav using steghide, and
eventually we had a hit. I regrettably no longer have this script saved, but you
can rest assured that it was woefully inefficient and took hours to land a hit.
(Recreating this script or a similar script of higher quality has been left as
an exercise to the reader.) Anyway, after hours of running through the wordlist,
a payload was found with the password "taco" and written to flag.zip.

Alright, so we've got a zip file to work with. Naturally, we should unzip it,
right? Well, kind of, but first it wouldn't hurt to run the gauntlet of `file`,
`foremost`, and `strings`. Side note, it would seem that it's worth scripting
the three of these into one quick command, given how often we run them in Stego
challenges. In this case, none of them produced anything useful, so let's unzip
it.
```bash
$ unzip flag.zip
Archive: flag.zip
[flag.zip] flag.txt password: 
```
Wonderful, we need a password. Just for fun, let's try "taco" again.
```bash
password incorrect--reenter:
```
Well, it was worth a try. Unfortunately, waiting on my earlier script has used
up my patience, so I'm going to escalate straight to the big guns - time to
break out [John The Ripper](https://www.openwall.com/john/).

After compiling and installing John, you encounter a beautiful directory full of
various password cracking tools, tailored to extracting and cracking hashes from
just about anything you can imagine, including zip files.
```bash
$ ./zip2john /PATH/TO/flag.zip > /PATH/TO/flag.hashes
ver 2.0 efh 5455 efh 7875 flag.zip->flag.txt PKZIP Encr: 2b chk, TS_chk, cmplen=64, decmplen=53, crc=85569B17
```
With that one command, we take in a zip file and write the hashes to a new file. Now all we have to do is call run John against that hash file.
```bash
./john --show ~/PATH/TO/flag.hashes 
flag.zip:taco1:::::/PATH/TO/flag.zip

1 password hash cracked, 0 left
``` 
So, continuing with the "taco" theme, the password to our zip file is "taco1".
Side note, this password is short enough that John cracks it nearly instantly.
The first time I ran this, I thought John had failed since it ended so quickly.
When I glanced at the output, I actually completely looked over the "taco1"
because it was pretty short and at this point I was pretty tired. However, after
looking back over it a few times, I realized my mistake. Anyway, after unzipping
the zip file with the password "taco1", we're given the file "flag.txt". Sounds
promising to me.
```bash
$ cat flag.txt
iasg{im_hungry_for_tacos_can_you_tell_also_good_job}
```
Victory!

# 5. Medium-Hard Crypto
Well, this is a nice change of pace. I mean, I really love Stego, but I'll enjoy
doing something a little different. This challenge starts with us downloading
[pubkey.pem](https://iasg.iac.iastate.edu/puzzlers/pubkey.pem) and
[flag.enc](https://iasg.iac.iastate.edu/puzzlers/flag.enc). Let's start by just
checking them out by hand.
```bash
$ cat pubkey.pem
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA168ysJPW4iS7ljqae8hz
sud8as52Pn1sbdfR0RGChS9oS1A91y52lP3sRmivv2+QxK9GYqvuHDozxQ7FxSqM
CpqvTQ8xYeu07nYioi/x+2e5MCPUlDEwb/wBQBkbEHexFMyohNtMzdjR97sTHvMh
tJBwA1SBYZIdKTpCYxiXCLoHVUUqZqbckWGIsmMBBTb9I56+2o2HembdhMtDH1pi
q5CLZrTmTT5Xv7ozjhOwN3wDA9Y4YHVYZHhaHI9LX7R8b8Tyqf2FMHMjBzPGi3VL
K0gBBwJzMwObsdhJExN+bfpHPpLUskr1fnMKSjT24BpBb5SoNVzPRpVM0m0lA7zq
IJA+zXegHyaEzx18cSS0Xe+r1sK0fybcFCgSyDVzxBKBPwFoBOLBLzHKQz3t1HY0
u+Pkk112KrblnnLqCTL/dfGIB6ixei9ogXY88wKLnhWlEUwGgX+wduBfTUH6Urnb
eVmf9AehhWtOtHOPHO00CkQHBynCpWrXO2EkO2yplqINUFSN/LEddQaJrpNHEzH+
aMIpImh/FsSDCNOtdOjJv/jBdEwGdNIDpWwJnXvujvlNktm07D0ekZegrEkldUX8
CeHtc51qEK3aCC5bGPfqqD54bumthYYHDqZdtPm1q7k5TaMvt01oyNSmTvXEoSzC
nWEiZOc9JZo/1ojFVnVYvqkCAwEAAQ==
-----END PUBLIC KEY-----

$ cat flag.enc
EpIoenxrDcSzBQ/zxTuk6D99pwXJ2cSO6jlx7dQhzaq43uotsX7LyeM9Dkx+ocJOebromW/69k/xp6OyVE9W/6o+D4vcQmmOObnzT41G15zsPx8O1DayIfO9Nrd5n5xoM9acaHCHBK8pZhoY/qLSW4AoVFrPk3woS5MBz2VTVimjG9jM0iyG+aBf145KTojXDm9J2B+u23bMB2yb9B/scYy8mqED98ZFp7iZ7Ev2XXs0n8FzvEeNtiJJJJ1FUQbckIv5a/ndISRD/HVfxXVjZeFG6JUaEQvPy3n1mf9xgtQt/S0SjBahl8iKCKfYVhxg9y9nzMLayZKnHMhzRhuR8XAZHq0or+amFEnm1molYgXMNTiyIzYsWDOz8s0MyVpzEbaP3u46tKftwM3YsEMMriE4o1nsipSk01NGR5aErIDazULPehXwhIPP5fu+mmGb0QmCStCAP9EG1/yupfN3Iobx2491SBvS2LVmgclftuJb2IwEZGg2qhJLkXzi46gaUM+XmPisXCZZSIBjLyKGVsTkNGUzt0IiXdk+xx2B7fTkssgJEJTv6yhJkClslI/bbwnegYoicyNWGBFXMjJiPJUA0Xz01QYkt75wTG5+siWUFTp9iul/iMiRTlcGbUKeQ5hW+0IPq7s106kFZyu32yEKv7gqe9/FlR1izeYCrEs=
```
Alright, so we've seemingly got a standard RSA public key and an encrypted file.
Logical conclusion: the private key corresponding to this public key was used to
encrypt this file. Time for an important lesson: be lazy. Someone out there who
understands $CONCEPT better than I ever intend to has already released $TOOL to
deal with this kind of problem. Turns out, if you search for "RSA CTF tool",
you'll find yourself on an github repo aptly named
[RsaCtfTool](https://github.com/Ganapati/RsaCtfTool). This is a great collection
of attacks against RSA in all forms. Let's see if these keys were generated with
weak enough primes to reverse engineer the private key from the public key,
shall we?
```bash
$ python RsaCtfTool.py --publickey /PATH/TO/pubkey.pem --private >
/PATH/TO/privkey.pem
$ file privkey.pem
privkey.pem: PEM RSA private key
``` 
Awesome, someone used weak crypto and now we have their private key. Now, how
should we go about decrypting flag.enc? I elected to use openssl's built-in RSA
utility. However, I encountered an issue.
```bash
openssl rsautl -decrypt -inkey privkey.pem -in flag.enc -out flag
RSA operation error
140400544260608:error:0406506C:rsa routines:rsa_ossl_private_decrypt:data greater than mod len:crypto/rsa/rsa_ossl.c:399:
``` 
Hmmm. That's annoying, it would seem that there's too much here for RSA to take
in. Now, I don't pretend to be a cryptographer, but I understand that block
ciphers tend to be very particular about the size of the data they work on. So,
what do we do now?

In my case, StackOverflow. Turns out other people had a similar question, and a
particularly helpful user pointed out that dencoding the data from base 64 tends to
help this process out. Frankly, I hadn't noticed that base64 was even in play,
but why not try it? Since my method of problem solving is mostly "throw
tools at the wall until something sticks", I figured I'd give it a shot.
```bash
$ cat flag.enc | base64 --decode > RawFlagData.enc
$ openssl rsautl -decrypt -inkey privkey.pem -in  RawFlagData.enc -out flag 
$ cat flag
iasg{rsa_is_cool_but_use_large_primes}
```
Well, what do you know? Looks like it worked!

That's the extent of the public puzzlers put out by IASG this semester, so this
is a good place to wrap things up. Of course, if you have any questions or
comments, feel free to shoot me a message via email or Twitter. 


If I could give any advice to someone who
wants to play around with (primarily stego) CTF-style puzzles, I'd tell them
that there's a lot of value in maintaining a personal directory of various tools
used in previous challenges, because there is nothing more frustrating than
realizing "I've seen a similar puzzle before, but I can't remember how or what I
used to solve it." Also, don't forget to run `strings`.
-JP
