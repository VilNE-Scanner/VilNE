# VilNEPriv
VilNE Scanner Private

## Background - While recently reading about 

## What is VilNE?

VilNE - *Victim Initiated Locale Network Enumerator/Exploiter (Villainy)* is a tool/proof of concept designed to highlight the potential attack surface present within an organisation's internal network. This attack surface will often be under-estimated with vulnerality management perhaps not giving it the attention that it requires. 

## What does it do and how?

VilNE currently:

* Identifies "Up" Web interfaces by timing the responses. - Due to CORS restrictions, it is not possible to read a page, or receive any information such as HTTP response codes. However, by timing the responses the tool can determine what hosts are likely to be up - a host with a web interface that is not allowed will fail much quicker than one which is not there and timesout.

* Using the above method, it scans for web-interfaces on the first address of class c(/24) subnets - The logic being that often this address will often have a device with a web interface of some description. Doing this allows the tool to quickly determine which subnets to scan in more depth. This is not foolproof, and future versions may also scan several other common address. 

* It then scans these subnets for web-interfaces on a variety of ports - The tool will attempt multiple ports, such as 443, 80, 8080 where web-interfaces are commonly hosted.

* "Attacks" these interfaces - Again due to CORS restrictions, it is not possible to see the contents returned by requests - but this does not stop the tool sending them. Therefore, any RCEs (Either un-authenticated or authenticated with weak CSRF protections), could still be successful. 


## Why is it a problem?

* This tool shows the concepts, which are that:
  * It is possible for an external page to enumerate internal web-interfaces. 
  * A web-page can be used to cause the browser to be a proxy for exploitation.
  
 * The concepts could be:
  * Embedded in a malicious website - Much like old EK redirect chains, I can envisage compromised websites being used to redirect users and/or have scripts embedded within that could be then used to proxy attacks on internal or external resources. 
  Used by someone within a network to quickly conduct reconnaissance/exploit web interfaces 
