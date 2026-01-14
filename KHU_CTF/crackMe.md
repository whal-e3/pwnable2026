---
From: KHU CTF (PWNLAB wargame website)
Type: reversing
Files: "`CrackMe`"
Dare date: 2026-01-14
Solve date:
Idea:
---

```bash
CrackMe: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=4cd9ce0735014def2e6060388e756ae03242b836, stripped
```

# Solution
---
```c
...
char user_input[264]; // [rsp+0h] [rbp-118h] BYREF
...
memset(user_input, 0, 256uLL);
```


# 🚩flag
---