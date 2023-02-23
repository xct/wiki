# Active Directory

Best resource for Active Directory: [adsecurity](https://adsecurity.org/).

### Kerberos overview

1. _AS-REQ_: Local timestamp, encrypted/signed with users hash is send to DC
2. _AS-REP_: DC answers with Ticket Granting Ticket \(TGT\) \(encrypted with krbtgt hash, so client cant decrypt it\)
3. _TGS-REQ_: Client sends TGT back & a Ticket Granting Service \(TGS\) request to DC in order to get a TGS-Ticket
4. _TGS-REP_: DC sends TGS Ticket \(encrypted/signed with target service NTLM hash\)
5. _AP-REQ_: Client presents TGS to Application Server \(which knows its correct since it is encrypted with its service account or machine account hash\)

### Kerberos Attacks

* _Golden Ticket_ attacks TGS-REQ, validation is done on the DC only based on the TGT encryption, if we have a valid krbtgt hash it will accept everything inside it. \(does not need the password, just the hash\)
* _Silver Ticket_ forges a TGS-Ticket and presents it to the application server \(needs the ntlm hash of a service account, so it can decrypt it\), usually limited to a single target box
  * many services use the machine account as a service account
  * can be created for HOST, RPCSS, CIFS, WSMAN, .... 
* _Kerberoast_ extracts a service hash from a TGS-Ticket \(usually a machine account, but there are some services running as 

  user accounts, which will have a SP Names associated - we want to prioritize these\)

* _ASREP-Roast_ captures the AS-REQ in order to crack it \(its encrypted with the users hash\). This needs kerberos preauthentication disabled. In some cases we can disable it with the right acl conditions.

### Kerberos Double Hop Problem

* Can't log into application server and server then impersonates the same user to log into another box e.g. a db \(only one hop allowed\)

### Resources

* [https://rmusser.net/docs/Active\_Directory.html](https://rmusser.net/docs/Active_Directory.html)
* [https://github.com/balaasif6789/AD-Pentesting](https://github.com/balaasif6789/AD-Pentesting)
* [https://github.com/3gstudent/Pentest-and-Development-Tips/blob/master/README-en.md](https://github.com/3gstudent/Pentest-and-Development-Tips/blob/master/README-en.md)
* [https://cas.vancooten.com/posts/2020/11/windows-active-directory-exploitation-cheat-sheet-and-command-reference/](https://cas.vancooten.com/posts/2020/11/windows-active-directory-exploitation-cheat-sheet-and-command-reference/)
* [https://posts.specterops.io/kerberosity-killed-the-domain-an-offensive-kerberos-overview-eb04b1402c61](https://posts.specterops.io/kerberosity-killed-the-domain-an-offensive-kerberos-overview-eb04b1402c61)
* [https://m0chan.github.io/2019/07/31/How-To-Attack-Kerberos-101.html](https://m0chan.github.io/2019/07/31/How-To-Attack-Kerberos-101.html)

### Tools

* [https://github.com/HarmJ0y/DAMP/](https://github.com/HarmJ0y/DAMP/)



