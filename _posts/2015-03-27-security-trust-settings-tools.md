---
title: Keychain Trust Settings
description: Blacklist Any Untrusted Certificate
tags: [security, certificate, blacklist, revoke, OS X, Keychain.app]
image: /assets/img/2015/03/27/security-trust-settings-tools/cnnic-root@2x.png
---


![CNNIC ROOT]({{ page.image }}){: srcset="{{ page.image }} 2x" }

Only a few days after the [DDoS attack on Greatfire.org](https://greatfire.org/blog/2015/mar/we-are-under-attack) began, [Google](https://googleonlinesecurity.blogspot.com/2015/03/maintaining-digital-certificate-security.html) and [Mozilla](https://blog.mozilla.org/security/2015/03/23/revoking-trust-in-one-cnnic-intermediate-certificate/) posted about China Internet Network Information Center (CNNIC) issued a certificate for man-in-the-middle attack.  Then, yesterday, the [Large Scale DDoS Attack on GitHub](https://github.com/blog/1981-large-scale-ddos-attack-on-github-com) began, attempting to take down [Greatfire](https://greatfire.org)'s accounts on GitHub.

All these attacks just gave me more consern over cybersecurity, especially against man-in-the-middle attack from China.  Thus, I created [security-trust-settings-tools](https://github.com/ntkme/security-trust-settings-tools), a tool set to make it really easy to blacklist all user untrusted certificates on OS X.

# Keep Away from Chinese SSL Certificates

To blacklist all common Chinese SSL Certificates with my tools, simply try the OS X version of [RevokeChinaCerts](https://github.com/chengr28/RevokeChinaCerts).
