# VilNE
VilNE Scanner 



## What is VilNE?

VilNE - *Victim Initiated Locale Network Enumerator/Exploiter (Villainy)* is a tool to show proof of concepts which may highlight the potential attack surface present within an organisation's internal network. This attack surface will often be under-estimated with vulnerality management perhaps not giving it the attention that it requires. 

## Background

I recently came upon a blog post about CORS bypasses using DNS Rebinding which interested me, I cannot remember exactly which one although it may have been https://book.hacktricks.xyz/pentesting-web/cors-bypass .  I have spent my career as a network defender and I have not had much experience with developing my own tools, but reading this got me interested to see what I could do in general. 

Quite honestly, I'm not sure that VilNE is doing anything new, and if this is already old news, well I've enjoyed learning a lot about any area which I had (and still have) not a huge amount of knowledge in. I personally feel that this may be an area which hasn't had much consideration, certainly as a defender this is going to give me a different view, and I think it bolsters the argument for zero trust and treating everything as hostile.

I would like to also thank ** whose javascript development skills are insane in comparison to my year 2000 geocities levels of web coding! Also ** for providing well needed infrastructure for the testing of this.



## What does it do and how?

VilNE currently:

* Identifies "Up" Web interfaces by timing the responses. - Due to CORS restrictions, it is not possible to read a page or receive any information such as HTTP response codes. However, by timing the responses the tool can determine what hosts are likely to be up - a host with a web interface that is not allowed will fail much quicker than one which is not there and timesout.

* Using the above method, it scans for web-interfaces on the first address of class c(/24) subnets - The logic being that often this address will often have a device with a web interface of some description. Doing this allows the tool to quickly determine which subnets to scan in more depth. This is not foolproof, and future versions may also scan several other common address. 

* It then scans these subnets for web-interfaces on a variety of ports - The tool will attempt multiple ports, such as 443, 80, 8080 where web-interfaces are commonly hosted.

* "Attacks" these interfaces - Again due to CORS restrictions, it is not possible to see the contents returned by requests - but this does not stop the tool sending them. Therefore, any RCEs (Either un-authenticated or authenticated with weak CSRF protections), could still be successful. 
  * Note - this uses 2 dummy attacks, a GET and a POST,  but in principal this could be a large file externally hosted with a bank of attacks. (Maybe something like the Mirai botnet uses)


## Why is it a problem?

* This tool shows the concepts, which are that:
  * It is possible for an external page to enumerate internal web-interfaces. 
  * A web-page can be used to cause the browser to be a proxy for exploitation.
  
 * The concepts could be:
   * Embedded in a malicious website - Much like old EK redirect chains, I can envisage compromised websites being used to redirect users and/or have scripts embedded within that could be then used to proxy attacks on internal or external resources. 
   * Used by an adversary/pentester by social engineering an internal user to conduct prior reconnaissance. 
   * Other exploits (e.g. CVE-2021-40444) use other internal browsers which will have less strict policies. In principal this could mean that the potential for attack techniques is greatly increased.
   * Used by someone within a network to quickly conduct reconnaissance/exploit web interfaces - although I'm sure other tools will be used for this.


## Challenges and Limitations - and possible bypasses

* CORS 
  *  https://book.hacktricks.xyz/pentesting-web/cors-bypass does highlight some bypasses to many of the CORS restrictions. However this tool is designed to not use things like CORS misconfigurations. 
  * Inability to modify HTTP Headers - Pre-flight checks mean that CORS restrictions kick in if you attempt to modify HTTP headers - this means certain vulnerabilities will not work.
  * Inability to view the response - Attacks such as SQL injection generally will not work, although I'd be interested to guage timeout speeds based on blind SQL injection attempts.

* Mixed-Content
  * Modern browsers are restrictive on the content that can be retrieved. E.g You cannot request HTTP content from an HTTPs page - This means that VilNE must be hosted on a HTTP page (or locally hosted - e.g. A phishing email with an HTML attachment). However, a compromised web page could simply redirect to the script which is hosted on HTTP on the malicious actors infrastructure.

## Detection and Mitigation

* Mitigation thoughts
  * Patch - An obvious one - while not preventing the above scanning, it would at least prevent the botnet-like drive by exploitation.
  * Browsers - Bulk scanning such as what this tool does could be restricted by browsers, by perhaps rate limiting or placing restrictions on unique IP/Port attempts, or failure codes across a series of hosts.
  * Browsers - Browsers could maybe prevent parameters being sent until CORS is resolved, such as when doing pre-flight checks.  
  * EDR - I haven't tested this on any EDR tools, however given the noise this would generate I would imagine it may come under existing rules for blocking of port scans.
  * Web applications - As well as patch, ensure all applicable CORS + CSRF protections are in place and tested.
  * Proxy/Enforcing WAF/IDS - Enforce ALL traffic connecting to a Web application to utilise security platforms such as a WAF with up-to-date rules. Implement least-privilege where possible so that only authorised users/IPs can connect.


* Detection thoughts
  * Port Scan detection rules - While within a subnet, port scanning detections using firewall logs is more of a challenge, however a rule which looks for a single internal host attempting the same port on many internal destinations should be considered.
  * IDS/WAF/Proxy logs - By enforcing users into a path via IDS/WAF/Proxy in order to connect to a web application, you can ensure that detection opportunities are maximised. If you see an internal IP trigger a rule, this warrants attention!
