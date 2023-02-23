# Kerberos Authentication for IIS

#### Build

* In Site Configuration -&gt; Authentication, Enable Anonymous, ASP.NET Impersonation \(if you need that\) & Windows Authentication
* Make sure AppPool is using integrated and not classic
* On the same menu, click providers and make sure it only says Negoiate & NTLM
* Create a service account which the apppool will be running as \(e.g. web\_service\)
  * Give a SPN to that service account `setspn -a HTTP/servername.test.com web_service`
* Change Identity of Appool to use the new user \(Advanced Options\)
* Go to Configuration Editor -&gt; system.webServer-&gt; security -&gt; authentication -&gt; windowsAuthentication and check usingPoolCredentials & useKernelMode

#### References

* [https://techcommunity.microsoft.com/t5/iis-support-blog/setting-up-kerberos-authentication-for-a-website-in-iis/ba-p/347882](https://techcommunity.microsoft.com/t5/iis-support-blog/setting-up-kerberos-authentication-for-a-website-in-iis/ba-p/347882)
* [http://woshub.com/configuring-kerberos-authentication-on-iis-website/](http://woshub.com/configuring-kerberos-authentication-on-iis-website/)



