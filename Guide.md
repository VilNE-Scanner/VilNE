# Manual

![Overview](/images/V-1.0-Overview.png)

##Setting up for yourself

This can be downloaded and launched in your browser, or hosted - however for it to work successfully it needs to be hosted on a HTTP server. This is due to browser restrictions on mixed content.


##Configuration of tool
Many of the below can be adjusted to suit your environment, although I've had most success with the defaults:

* Class C Discovery Timeout
    * During the inital stage, the tool will scan the 255 Class C networks for devices on a .1 address. The presence of one suggests that it is a live host and therefore a live subnet. The quicker this timeout - the speedier this stage may run but also the chances it may miss a subnet increase.

* Host Scan Timeout
  * Timeout used during the scanning phase where the tool scans the networks it identified for live hosts. Too low and you may miss hosts, but too high and you may get a lot of false positives.

* Host Scan Group Size
  *  When scanning for live hosts, it will do these in groups for reliability reasons. A large set may speed this up but reduces the reliability.

* Class B Networks to Scan
  * If the defaults (192.168.0.0/16 & 10.0.0.0/16) are not suitable, you can add another here or remove these.

* Ports to scan
  * By default this is set to a series of ports that web applications are commonly hosted on. You can add other ports here. 

* Attack hosts
  * When doing the host scan, attempt to "exploit" them with dummy GET/POSTs

* Scan Now
  * Initialises the scan

* Reset
  * Stops the scan 
