---
title: Github authenticator登录问题
date: 2017-07-21 18:45:07
categories: 工具使用
tags:
    - Github
    - Git
    - Github authenticator
---

# Github authenticator登录问题

---

最近换了手机，刚刚写完代码，需要提交到Github，发现以前设置过二次校验，是通过Google authenticator设置的。

发现以前备份的code，也丢失了，重新安装了Google authenticator也不行，最好只要找官方客服，发邮件求帮助。

下面是帮助地址，[https://github.com/contact](https://github.com/contact)，然后过了几个小时，就有人回复了。

```text
Hello there,

The recovery codes would have had the default filename github-recovery-codes.txt or github_2fa_recovery_codes.txt - it may still be worth searching your computer, data backup, and/or email account for this document, just in case!

If you don't have valid recovery codes, you may be able to verify account ownership using an SSH key you have added to your account. To do this, please run the following command on the computer where your SSH key exists, and send us the full output:

ssh -T git@github.com verify

If you can verify account ownership, we can disable 2FA on your account so you can sign in again.

All the best,
Andrew
```

然后就把`ssh -T git@github.com verify`命令执行了一般，最后把结果发过去，还在等结果。

悲剧的一天，下班，代码不提交了。