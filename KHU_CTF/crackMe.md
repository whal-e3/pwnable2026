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
ida pseudo code
```c
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  char user_input[264]; // [rsp+0h] [rbp-118h] BYREF
  unsigned __int64 v5; // [rsp+108h] [rbp-10h]

  v5 = __readfsqword(0x28u);
  memset(user_input, 0, 256uLL);
  __printf_chk(1LL, "input : ", a3);
  fflush(stdout);
  user_input[(int)(read(0, user_input, 256uLL) - 1)] = 0;
  if ( (unsigned int)func_in_interest(user_input) )
    puts("correct");
  else
    puts("wrong");
  return 0LL;
}
```



# 🚩flag
---