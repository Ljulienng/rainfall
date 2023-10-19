In this level we're given a program ./level0

the program takes a string as an argument else it just does a segfault.
it seems like we have to find the right string for it to work

nm and strings doesn't work so let's use gdb instead
after disasembling main (disas main)

[...]
0x08048ed4 <+20>: call 0x8049710 <atoi>
0x08048ed9 <+25>: cmp $0x1a7,%eax
[...]

Those line are interesting:

- the program uses atoi and compares the result with "0x1a7"

We just have to decode it to decimal:

0x1a7 = 423

now that we have access to the shell let's just use the example given in the subject
cat /home/user/level1/.pass
