# Leak report
## Where the leaks are happening

```bash
==31181== 46 bytes in 6 blocks are definitely lost in loss record 1 of 1
==31181==    at 0x4C2FA50: calloc (vg_replace_malloc.c:711)
==31181==    by 0x400678: strip (check_whitespace.c:41)
==31181==    by 0x4006E3: is_clean (check_whitespace.c:62)
==31181==    by 0x40076B: main (check_whitespace.c:87)
```

This means that there are 46 bytes were lost and were allocated in calloc
  1. The first lost on line 41 by the ***strip*** function
  2. The second lost is on line 62 by the function ***is_clean***
  3. The third is ts lost by the ***main*** function on line 87

### Why memory errors happen and How to fix it
```bash
result = calloc(size-num_spaces+1, sizeof(char));
```
The memory errors are happening because if the character pointer arrays is empty then no memory is allocated. To fix this we need to free ***cleaned*** using the free command. We must add a conditional and since it is declared that the null terminator _'\0'_ indicates the end of the string. And so we will check that if it is not the end of the string then we must free it.

```c
if (cleaned[strlen(cleaned) - 1] == '\0'){
   free(cleaned);
}
```
And then we get the result being

```bash
==27706== Memcheck, a memory error detector
==27706== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.
==27706== Using Valgrind-3.12.0 and LibVEX; rerun with -h for copyright info
==27706== Command: ./check_whitespace
==27706==
The string 'Morris' is clean.
The string '  stuff' is NOT clean.
The string 'Minnesota' is clean.
The string 'nonsense  ' is NOT clean.
The string 'USA' is clean.
The string '   ' is NOT clean.
The string '     silliness    ' is NOT clean.
==27706==
==27706== HEAP SUMMARY:
==27706==     in use at exit: 0 bytes in 0 blocks
==27706==   total heap usage: 7 allocs, 7 frees, 1,070 bytes allocated
==27706==
==27706== All heap blocks were freed -- no leaks are possible
==27706==
==27706== For counts of detected and suppressed errors, rerun with: -v
==27706== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```
