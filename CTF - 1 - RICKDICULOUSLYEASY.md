---
title: MY FIRST BOOT2ROOT LEARNING EXPERIENCE
---

Documentation of thought process and learning notes

15 April 2021

A. Introduction
===============

My first attempt at a boot2root machine based on recommendations on
reddit, and more importantly, a Rick and Morty themed one at that.

This is the documentation of my thought process and hopefully the start
of an incremental learning journey into the world of CTFs, and the
cybersecurity domain.

B. Additional Information
=========================

Vulnerable machine used: [RICKDICULOUSLYEASY:
1](https://www.vulnhub.com/entry/rickdiculouslyeasy-1,207/)

> > This is a fedora server vm, created with virtualbox.
>
> > It is a very simple Rick and Morty themed boot to root.
>
> > There are 130 points worth of flags available (each flag has its
> > points recorded with it), you should also get root.
>
> > It's designed to be a beginner ctf, if you're new to pen testing,
> > check it out!

**Walkthroughs used for learning process:**

<https://medium.com/pentestsec/rickdiculouslyeasy-vulnhub-16a54ac2a8e1>

<https://www.hackingarticles.in/hack-rickdiculouslyeasy-vm-ctf-challenge/>

**VirtualBox Network**

Internal Network with 10.10.10.0/24 for network address, and DHCP range
of 10.10.10.100 -150.

C. Initial Host Discovery
=========================

Already know VM has IP address of 10.10.10.101 from VirtualBox settings

Will still run the **nmap** command for learning the basics of how to
use it.

-sn is the option to scan entire network of a network address based on
the subnet mask. This is the quickest and easiest way to learn what is
on the network in a more reliable way than a simple ping sweep

-sn will perform a no port scan

![](media/image1.png){width="5.813311461067366in"
height="1.614808617672791in"}

To find out what software, and its version listening on a port, I use
sudo nmap 10.10.10.101 -sV -O

-sV is to determine what software and version is listening on the host
ports (--version)

-O is to identify the OS of the host operating system

![](media/image2.png){width="6.268055555555556in"
height="1.9951388888888888in"}

Nmap 10.10.10.101 -p- sC for deeper probe into the host

![](media/image3.png){width="5.726415135608049in"
height="5.318472222222222in"}

Learning Notes
--------------

-   -p- is used to specify scanning all TCP ports (port range 1-65535)

-   -sC (use all scripts) is used to identify specific applications
    listening on ports, scan for known exploits agains those
    applications, and scan for common misconfigurations of services

-   -T4 (timing templates) could be used to speed up probe. T0 being
    slowest and T5 being fastest in terms of scan delay before
    giving up. Since latency is extremely low on VMs, could have
    used -T4. (note that seldom used on real-world networks with
    actual latency)

-   -sS (stealth scan / TCP SYN). Relatively unobtrusive and stealthy
    since it never compltetes

-   TCP connections. A clear, reliable differentiation between open,
    closed and filtered states

-   -A (all) runs all scripts and -O

-   For even deeper scan, could have used options -p \[opened ports
    discovered\] -sV -O (or -A)

-   For purpose of home lab, could use options: -p- -T4 -sS first before
    doing -sC?

D. Scan Results
===============

> Nmap scan report for 10.10.10.101
>
> PORT STATE SERVICE
>
> 21/tcp open ftp -&gt; allows anonymous FTP login, has FLAG inside
>
> 22/tcp open ssh -&gt; SSH, so probably need some sort of credentials?
>
> 80/tcp open http -&gt; web server
>
> 9090/tcp open zeus-admin -&gt; not sure what this means but SSL, so
> web server?
>
> 13337/tcp open unknown
>
> 22222/tcp open easyengine -&gt; not sure what this means, but SSH
>
> 60000/tcp open unknown

E. Starting the Process
=======================

Thought process for sequence of ports to test:

60000 &gt; 21 &gt; 13337 &gt; 9090 &gt;&gt; 80 &gt; SSH ports (22 and
22222)

Based on what I think would be the easiest way to navigate around the
scan results.

F. Port 60000
=============

Since it’s “unknown”, first instinct is to **netcat** the port, which
gives welcome message of “Welcome to Ricks half baked reverse shell…”
and a shell? (no idea what this really means at the moment)

![](media/image4.png){width="3.875541338582677in"
height="3.2087806211723535in"}

No idea what I’m supposed to do after getting the flag or if in a
real-world situation there’ll be something more I should be exploring,
but felt learning objectives were met and moved on first.

FLAG{Flip the pickle Morty!} – 10/130Points

Learning Notes:
---------------

-   -nc (netcat) is a network utility that uses TCP and UDP connections
    to read / write in a network. It can do port scanning, banner
    grabbing, transferring a file, and generating a reverse connection
    to name a few

-   It acts as a simple TCP/UDP/SCTP/SSL client for interacting with
    web servers. My current understanding is that it’s useful for banner
    grabbing for CTF VMs.

-   Banner Grabbing is useful for intel-reconnaissance. It gets software
    banner information and often exposes critical information, and can
    reveal insecure and vulnerable applications which could lead to
    service exploitation.

G. Port 21 (Anonymous login FTP)
================================

Remembered reading something previously about using Metasploit for
vsFTPd versions but did not return a result for the version that the VM
is running (3.0.3)

![](media/image5.png){width="5.396586832895888in"
height="2.2190594925634297in"}

Realized after that again, that the service allowed for anonymous
logins, and there was already a file called “FLAG” inside.

![](media/image6.png){width="5.771639326334208in"
height="5.250732720909887in"}

FLAG{Whoa this is unexpected} – 20/130 Points

Learning notes:
---------------

-   You can use ls in an FTP session but can’t use cat. Use **get** to
    download the file locally first before utilizing the cat command.

H. Port 13337
=============

Seeing as it’s another port being utilized by an unknown service, first
instinct is to **netcad** it, similar to port 60000.

![](media/image7.png){width="3.854704724409449in"
height="0.7084317585301837in"}

FLAG:{TheyFoundMyBackDoorMorty} - 30/130 Points

Learning notes:
---------------

-   **Netcad** will be my friend. Will Google more on its actual
    real-world / practical usages.

I. Port 9090 (SSL = Web Server?)
================================

Easy and straight forward enough. Tried looking at the source code to
find more additional information, but my current limited knowledge on
web development tells me that there is nothing of interest left on this
service.

![](media/image8.png){width="6.268055555555556in"
height="1.1458333333333333in"}

Tried using **dirb** to do some additional recon for this service but
didn’t get any promising hits.

\~/ping returns a JSON application called cockpit that didn’t seem like
I could exploit in anyway at the moment.

![](media/image9.png){width="5.219478346456693in"
height="3.5838331146106737in"}

![](media/image10.png){width="6.115436351706037in"
height="4.615227471566055in"}

Script kiddie in me decided that trying SSLye was worth a shot, but
didn’t return any promising hits as well. From some preliminary reading
/ YouTubing, vaguely recalled it was to scan for the Heartbleed bug that
made headlines in 2014.

FLAG {There is no Zeus, in your face!} – 40/130 Points

Learning notes:
---------------

-   Secure Sockets Layer (SSL) is a standard security technology for
    establishing an encrypted link between a server and a
    client—typically a web server (website) and a browser, or a mail
    server and a mail client (e.g., Outlook).

-   Cockpit is an easy-to-use, lightweight and simple yet powerful
    remote manager for GNU/Linux servers, it’s an interactive server
    administration user interface that offers a live Linux session via a
    web browser.

-   Heartbleed vulnerability link:
    <https://www.vox.com/2014/6/19/18076318/heartbleed>

J. Port 80 (HTTP) – Part 1
==========================

Initially the website and source code did not provide any useful
information.

![](media/image11.png){width="6.268055555555556in"
height="2.9243055555555557in"}

![](media/image12.png){width="6.268055555555556in"
height="1.9118055555555555in"}

Additional reading and INE’s basic penetration testing course prior
taught me that I should always attempt to find for hidden directories
(spider), as those almost always yield additional information,
especially in purposefully vulnerable machines. (Port 80 = HTTP
service).

First, I went looking into \~/**robots.txt**, which yielded some
directories.

![](media/image13.png){width="5.1361329833770775in"
height="2.0523698600174978in"}

Noted down kept the cgi-bin directory in mind first while performing
**dirb** to search for additional hidden directories of interests.

![](media/image14.png){width="5.198641732283464in"
height="4.542300962379702in"}

Noted the existence of \~/passwords directory now as well. Exploring it
further since it is a dead giveaway.

![](media/image15.png){width="5.198641732283464in"
height="3.448397856517935in"}

FLAG.txt yielded another flag, while passwords.html didn’t return
anything interesting initially until looking at the source code.

I now have a password called “winter”, but no other credentials to use
it with. Noted it down for later.

![](media/image16.png){width="6.268055555555556in"
height="0.9222222222222223in"}

![](media/image17.png){width="6.268055555555556in"
height="0.7854166666666667in"}

FLAG{Yeah d- just don't do it.} – 50/130 Points

Learning notes:
---------------

-   Always look at source code for crucial information that may be
    purposefully hidden by the creators of vulnerable machines, or
    accidentally leftover by developers in real-world scenarios.

K. Port 80 (HTTP) – Part 2
==========================

Attempting to figure out what functions the 2 cgi applications could do.

Root\_shell.cgi didn’t yield any promising results.

![](media/image18.png){width="3.55257874015748in"
height="1.2293383639545057in"}

Tracertool.cgi on the other hand looked like it’s running a traceroute
command from the server.

A little bit stumped here as to what else could be done using this app,
so took the time to digest the walkthroughs to get a better
understanding of what was happening, what additional things I could make
happen, and the rationale behind it.

![](media/image19.png){width="6.268055555555556in"
height="2.428472222222222in"}

After reading additional explanation extracted from pentestsec’s medium
post:

> *What happened here? instead of using an IP as input I used a
> semicolon. Due to the fact that the site is not applying restrictions
> to the input we managed to run arbitrary commands on the machine.*
>
> *Instead of the expected input: traceroute 192.168.1.5*
>
> *We used ‘; whoami’ to execute the following command: traceroute ;
> whoami*
>
> *The semicolon makes the second command to be executed after the first
> one finishes. Command concatenation can also be applied ussing ‘&&’
> and ‘||’*

Able to vaguely understand that inputting the semicolon allowed for
command injection or RCE, which makes the server output information as
though I was on its terminal.

Tried by first using ; whoami ; pwd and ; ls -la to get my bearings of
what can / could be done with this.

![](media/image20.png){width="4.5006277340332455in"
height="2.812892607174103in"}

Remembered that users on a Linux system as stored in the /etc/passwd
directory. I have a password (winter) from previously, now I need other
credentials to attempt to move forward.

**Cat** command seems to have been replaced with an output of a literal
cat ascii art, had to use **more** command to get the output I was
seeking.

Now have 3 users: RickSanchez, Morty, and Summer.

![](media/image21.png){width="4.583973097112861in"
height="5.031952099737532in"}

![](media/image22.png){width="5.615367454068242in"
height="6.990558836395451in"}

Learning notes:
---------------

-   CGI (Common Gateway Interface) is a standard way of running programs
    from a Web server. Often, CGI programs are used to generate pages
    dynamically or to perform some other action when someone fills out
    an HTML form and clicks the submit button.

-   Remote code execution (RCE) is a class of software
    security flaws/vulnerabilities. RCE vulnerabilities will allow a
    malicious actor to execute any code of their choice on a remote
    machine over LAN, WAN, or internet.

-   Every user on a Linux system, whether created as an account for a
    real human being or associated with a particular service or system
    function, is stored in a file called "/etc/passwd".

-   The "/etc/passwd" file contains information about the users on
    the system. Each line describes a distinct user.

-   **more** command lets you view text files or other output in a
    scrollable manner. It displays the text one screenful at a time, and
    lets you scroll backwards and forwards through the text, and even
    lets you search the text.

L. Ports 22 and 22222 (SSH) – Part 1
====================================

I have a list of users and 1 password. I create a user.txt file as a
list for **hydra** to match with password “winter”.

![](media/image23.png){width="4.292265966754155in"
height="3.958885608048994in"}

Used **hydra-wizard**. The step-by-step input feels beneficial to my
current level of understanding when using tools.

Port 22 doesn’t seem to work, while 22222 matched user “Summer” with
password “winter”. (which was to be expected)

![](media/image24.png){width="6.268055555555556in"
height="0.5951388888888889in"}

![](media/image25.png){width="6.268055555555556in"
height="0.8166666666666667in"}

With matched credentials and successful login, begin to start exploring
the server’s internals and found another FLAG.txt file.

![](media/image26.png){width="6.268055555555556in"
height="4.284027777777778in"}

FLAG{Get off the high road Summer!} – 60/130 Points

Learning notes:
---------------

-   Blocks of multiple lines of text can be written in a single command
    when using the &lt;&lt;EOL command (heredoc).

-   There’s a somewhat obscure feature in Linux and Unix shells that
    allows you to open a sort of do-while loop for the cat command. It’s
    called the heredoc, and it enables you to have, more or less, a text
    editor no matter what shell you’re using.

-   Using cut -d : -f 1 /etc/passwd allows to only get the users instead
    of the other information presented. -d represent the delimiter,
    while -f represents the position of the delimiter to stop at.

-   Metasploit module “auxiliary/scanner/ssh/ssh\_login” can be used to
    brute force and open a shell as well. (More info at
    [here](https://www.offensive-security.com/metasploit-unleashed/scanner-ssh-auxiliary-modules/)
    and
    [here](https://null-byte.wonderhowto.com/how-to/get-root-with-metasploits-local-exploit-suggester-0199463/))

M. Ports 22 and 22222 (SSH) – Part 2
====================================

Further digging showed that Summer has access to Rick and Morty’s
respective home directories.

![](media/image27.png){width="5.698711723534558in"
height="2.062787620297463in"}

Within Morty directory, there’s a password protected .zip file and what
appears to be a .jpg file with the needed password.

![](media/image28.png){width="4.386543088363955in"
height="4.78301946631671in"}

Downloaded the 2 files using **scp** to Kali machine to further
investigate them.

![](media/image29.png){width="6.268055555555556in"
height="1.1833333333333333in"}

.jpg file appears to be just a normal picture file. Further reading of
the walkthroughs tells me that there are methods of inspecting the
files, which yield the password required for the zip file “Meeseek”
(using **head** or **strings** command).

![](media/image30.png){width="6.268055555555556in" height="0.96875in"}

Output of the file also states that the flag is a possible password for
another objective. Have noted “131333” as something relevant for later
use.

![](media/image31.png){width="6.268055555555556in"
height="0.47152777777777777in"}

FLAG: {131333} – 80/130 Points

Learning notes:
---------------

-   SSH or Secure Shell is a protocol that allows a secure way to access
    remote computer. SSH implementation comes with scp utility for
    remote file transfer that utilises SSH protocol.

-   The easiest of these are scp or secure copy. While cp is for copying
    local files, scp is for remote file transfer where both uses almost
    the same syntax. The main difference is that with scp you'll have to
    specify the remote host's DNS name or IP address and provide login
    credential for the command to work.

-   Linux strings command is used to return the string characters
    into files. It primarily focuses on determining the contents of and
    extracting text from the binary files (non-text file).

N. Ports 22 and 22222 (SSH) – Part 3
====================================

The directory that says it doesn’t contain any flags, surprisingly did
not contain any flags.

![](media/image32.png){width="6.268055555555556in"
height="1.6513888888888888in"}

![](media/image33.png){width="5.677876202974629in"
height="1.5939720034995626in"}

Dir RICKS\_SAFE on the other hand, seems to have a file and looks like
an executable that we do not have permissions to run (No permission to
e***x***ecute other than the owner).

Tried downloading it to the Kali machine using the scp command but
resulted in an error instead. I chalk it up as the Kali machine just
missing something.

![](media/image34.png){width="5.1883617672790905in"
height="3.7555555555555555in"}

![](media/image35.png){width="6.268055555555556in" height="0.975in"}

Consulted the walkthroughs to figure out what I was doing wrong instead.

From pentestsec’s medium write-up:

> *Here we can see the file ‘safe’ is an executable but the user that we
> are currently using has only read privileges for it. The sticky bit is
> not set for that file so we are able to make a copy and execute it
> (because we are the owner of the copy). We use the password found in
> Morty’s journal as a parameter for the executable.*

Followed the steps and got another flag, followed by a clue for another
password. Gut feeling for the line “sudo is wheely good” tells me that
it is something I should note down at least.

![](media/image36.png){width="6.268055555555556in"
height="2.3069444444444445in"}

FLAG{And Awwwaaaaayyyy we Go!} – 100/130 Points

Learning notes:
---------------

-   Example: The permission in the command line is displayed as:
    \_rwxrwxrwx 1 owner:group

<!-- -->

-   The first character that I marked with an underscore is the special
    permission flag that can vary.

-   The following set of three characters (rwx) is for the
    owner permissions.

-   The second set of three characters (rwx) is for the
    Group permissions.

-   The third set of three characters (rwx) is for the All
    Users permissions.

-   Following that grouping since the integer/number displays the number
    of hardlinks to the file.

-   The last piece is the Owner and Group assignment formatted
    as Owner:Group.

O. Ports 22 and 22222 (SSH) – Part 4
====================================

The previous clues indicated the password would be (Upper
Case)(Digit)The/Flesh/Curtains.

Using hackingarticles’ walkthrough as a guideline for understanding,
used **crunch** command to generate the password list to brute force for
user RickSanchez with **hydra** again.

![](media/image37.png){width="5.521604330708661in"
height="1.4897911198600176in"}![](media/image38.png){width="5.636202974628172in"
height="3.0420909886264216in"}

![](media/image39.png){width="3.958885608048994in"
height="0.635505249343832in"}

Have identified a valid login credential.

![](media/image40.png){width="6.268055555555556in"
height="1.3659722222222221in"}

Started digging for what privileges the account had by using the id
command, and was reminded of the note about “sudo is wheely good”.

Used sudo -l to list the user’s privileges and saw “ALL”, indicating
that I could escalate to root privileges.

![](media/image41.png){width="6.268055555555556in" height="1.95in"}

And with that, finally obtained the last flag for a full 130 points, and
some PTSD about cat ascii art.

![](media/image42.png){width="4.979861111111111in"
height="5.886237970253719in"}

FLAG: {Ionic Defibrillator} – 130/130 points

Learning notes:
---------------

-   Crunch is a wordlist generator where you can specify a standard
    character set or a character set you specify. crunch can generate
    all possible combinations and permutations.

> ![](media/image43.png){width="5.386167979002624in"
> height="0.531324365704287in"}
>
> ![](media/image44.png){width="6.063346456692913in"
> height="0.6042508748906387in"}
>
> ![](media/image45.png){width="6.268055555555556in"
> height="0.9548611111111112in"}

-   The most easiest way to count the number of lines, words, and
    characters in text file is to use the Linux command “wc”
    in terminal.

-   In Unix operating systems, the term wheel refers to a user account
    with a wheel bit, a system setting that provides additional special
    system privileges that empower a user to execute restricted commands
    that ordinary user accounts cannot access.

-   The sudo -l list user's privileges or check a specific command.
    Using -l twice outputs a longer format.

P. Conclusion
=============

As my first CTF style machine, I found the pace to be just nice in
grasping the basics for boot2root. There are still many commands and
logics that I would like to commit to the common-sense portion of my
brain. The entire process and documentation took about 6 hours in total.

I had read both the walkthroughs about 2 – 3 times each prior to
attempting this machine to get a basic understanding of what to expect.
The walkthroughs have been immensely helpful in explaining some basic
concepts and I’d like to thank the authors for taking the time and
effort in publishing them.

Moving forward, I think I should reenforce my Linux fundamentals, as
well as read up more on how to use nmap effectively according to my
needs.
