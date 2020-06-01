# AX25-2020
Some utilities related to running AX.25 services

This repository contains some utilities I wrote to handle AX.25 services on my Linux system.  Some things I found missing, or deficient, in the usual sets of utilites, which honestly haven't been updated since the mid-90s for the most part anyway.

   * **axwrap** - A work-alike for Tomi Manninen's (OH2BNS) axwrapper.  It's written in Python3, uses a pseudo-terminal instead of pipes, which means that things like *login* can be wrapped, and should handle command-line arguments a bit better than Tomi's wrapper.  If you haven't seen his software before, it runs a program and translates inbound *CR* characters to *NL* for the program it's running, and outbound *NL* to *CR* for a standard AX.25 client.  You run it out of *a25d.conf* in this way:
   '''
   default	* * * * * *	-	root	/usr/local/bin/axwrap axwrap /bin/echo echo This is a test.
   '''
   
   * **axlogin** - I decided I didn't want to axwrap *login* after all.  Part of the reason for this is that I wanted to use my own PAM service definition.  This is rather important, because you should choose a service definition that won't have your passwords flying around in the clear on the AX.25 network.  Personally, I use the *pam_google_authenticator* and insist on TOTP authentication for over-the-air logins.  This utility pretty much does what the name implies.  You run it out of ax25d by adding a line like the following to the configuration:
```
   default	* * * * * *	-	root	/usr/local/bin/axlogin axlogin %S
```
   
   Syntax looks like this:
```
   usage: axlogin [-h] [-n] Callsign

Accept remote logins over AX.25

positional arguments:
  Callsign       Callsign of remote system.

optional arguments:
  -h, --help     show this help message and exit
  -n, --newline  Leave the newline characters rather than trying to translate
                 EOL into carriage-returns for the remote host.
```
 
 If you want it to provide service for a standard AX.25 terminal, run it without *-n*.  If you want to use *ax25_call* on Linux, for example, run it with the *-n* flag.
 
   * **ax25.pam** - This is the PAM service definition I've been using with **axlogin**; it lives in */etc/pam.d/ax25*, in general, and can at least be used as an example of how to configure your system for logins which use **only** TOTP with the *pam_google_authenticator* module.
    
    
