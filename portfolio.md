---
layout: page
title: Senior Portfolio
permalink: /portfolio/
---
### Welcome to Jack's Senior Portfolio
This portfolio page was built for my Computer Engineering 494 Class at Iowa 
State University. It contains a retrospective on my time at Iowa State as well
as summaries of some of the projects I've worked on during my time here at ISU.

### Career Objective
After graduation, my primary goal is to begin working in the security field,
specifically focusing on network penetration testing and physical security
assessment. Throughout my career, I hope to keep advancing my security 
knowledge, skills, and abilities, and I also hope to give back to the security
community through contributions to open source projects, presentations at
conferences such as DEF CON and BSides, and posting informational articles
online. 

### Senior Design Project
For my senior design project, myself and five others developed an encrypted
messaging phone application. Our primary goal was to develop a platform that
required encryption key exchanges to take place completely off of TCP/IP, i.e.
the key exchange cannot take place over any network, including the internet or
local networks. This requires users to meet in person to initially generate and
exchange keys together to ensure that there is no possible way for a malicious
actor to eavesdrop on the exchange. 

My role on this project was split between working on the client application (iOS
and Android applications written in Dart using the Flutter framework) and
performing tests on our application to ensure correct functionality. My
development role was focused on implementing the in person key exchange by
translating keys into QR codes which flash across the screens of the phones,
allowing the conversational partner to read in the QR codes and translate them
back into the AES keys used to encrypt that conversation. 

Throughout this project, I learned a lot about mobile application development,
the Dart programming language, and various cryptography concepts. 

This project is targeted to a niche group of individuals who have a need for
extreme paranoia in protecting their communications. This group includes
embedded journalists, whistleblowers, dissidents, and freedom fighters
worldwide. By taking the TCP/IP stack out of the initial key exchange and then
only allowing messages to travel over a network after having been encrypted with
a strong AES key and a strong RSA keypair, we are able to achieve a high degree
of confidence in the security of our application.

[If you would like to read more about our project, click here to visit our
webpage.](http://sdmay20-52.sd.ece.iastate.edu/)

### Other School Projects

#### Computer Engineering 331x
As my final project for Computer Engineering 331x, I researched and attempted to
develop PoC code for CVE-2019-11477, also commonly known as SACK Panic. This
vulnerability was discovered by security engineers at Netflix over the summer of
2019, and as I was not able to find functional exploit code online, I wanted to
develop it myself. This CVE was considered highly important by all of the
organizations tracking CVEs, as it allows remote users to trigger kernel panic
on Linux and BSD webservers by sending maliciously crafted TCP packets. 

While working on this project, I learned a lot about networking technology,
specifically how TCP packets are formed and the details of the initial TCP
handshake at the start of a connection. I also learned a fair bit about writing
networking code in both C and python. Resources utilized for this project
included a number of CVE entries across various sources (Mitre, NIST, Red Hat,
etc.) as well as the blog posts published by Netflix about their discovery of
the vulnerability. 

#### Computer Engineering 231
As my final project for Computer Engineering 231, I was tasked with breaking in
to a simulated corporate network and moving laterally through the "company's"
resources, escalating my privileges whenever possible. Following this, I had to
document how I gained access to the networked machines and then fix/secure all
of the vulnerabilities I had found. Finally, I was tasked with attacking the
networks of two other students in the class while they did the same to me. This
project gave me a good idea what it is like to work as a network penetration
tester, and it allowed me to apply concepts I learned throughout the class in
vulnerability discovery and exploitation. I utilized resources including the
Linux manual pages as well as programs such as nmap, Wireshark, MetaSploit, and 
other industry standard tools.

#### Computer Science 309
For my semester-long project in Computer Science 309, I worked on a team of four
students developing an Android application that allows college roommates to 
easily coordinate chores, communal shopping lists, and to-do lists. Our project
was geared towards Iowa State University students, and it included access to
additional student resources such as their dorm floor's Community Advisor. 

My role in this project was front-end development of our Android application. I
worked on both functions/features of the application as well as the UI/UX. One 
of my main tasks was to implement a barcode scanner for adding and removing 
items to/from a shopping list. 

Throughout the course of working on this project, I learned a lot about Android
development, specifically coding in Java with Android Studio. I also learned a
lot about the value of finding preexisting libraries that contain the
functionality that I'm looking for rather that writing the necessary functions
myself, as implementing existing barcode scanning libraries made the project
significantly more straightforward to complete. 

### Internship Experience

#### John Deere
I worked as an intern across two security teams at John Deere, initially as a
summer intern and then as a part-time student employee during my junior year. I
worked on both the Cloud Security team as well as an Embedded Security team,
across which I was exposed to many aspects of security engineering. On the cloud
security team, I primarily worked to secure AWS resources, developing scrips to
alert the team in real time if any non compliant resources were found. On the
embedded security team, I worked to secure the low level embedded devices that
are vital to the function of John Deere devices. This internship allowed me to
gain skills in AWS management, static code analysis, binary fuzzing, and agile
development practices. I received positive evaluations during my time with the
company and had the opportunity to present on my work multiple times to various
leaders in the organization.

#### RSM
I worked as an intern with RSM over the span of one summer, during which I was
able to work on many interesting penetration tests of a variety of clients. I
performed external and internal penetration tests, phone and email based social
engineering engagements, and multiple physical security assessments that
required me to break into offices of corporations and financial institutions. I
learned a lot about utilizing security skills and tools in the field, how to
effectively discover vulnerabilities in clients, and how best to present
security flaws and discoveries to the clients, which I did a number of times. I 
received positive evaluations from several associates at the firm and will be 
working there full time starting in July 2020. 

### Resume
[Click here to view or download a copy of my resume.](/resume.pdf)

### Cumulative Reflection
[Click here to view or download a copy of my 494 cumulative course
reflection.](/CumulativeReflection.pdf)

### General Education Reflection
[Click here to view or download a copy of my 494 general education
reflection.](/GeneralEducation.pdf)

### Ethics Essay
[Click here to view or download a copy of my term paper for Computer Engineering
234x: Legal and Ethical Issues in Cyber Security](/TermPaper-Potter.pdf)

### Contact me

[jack@hackpotter.com](mailto:jack@hackpotter.com)







