# ps4-controller-weak-auth
WEAK_AUTH algorithm of Pla\*\*\*\*tion4 controller. Please **don't use it**, or $\*\*￥ may kill you, even though they are busy working on Pla\*\*\*\*tion5 now.

This is for educational only, all the related data can be found in public internet.

## You are **not expected to understand this** unless you have deeply reverse engineered the Pla****tion4 controller. 😈

All publicly available researches on the ps4 controller authentication protocol on the Internet are aimed at the main authentication (256 Bytes challenge, 1040 Bytes response) in the case of USB, except this one. This research reveals the WEAK_AUTH algorithm when the PS4 controller is connected using the Bluetooth protocol, which will be once every 4 seconds. Failing them seems to cause the PS4 to immediately ignore the controller.

```
input:
    [NOUSE, 1B] + [tid, 1B] + [input challenge, 32B], total_len = 34B

dword_20023B68:
    [v2 ^ sub_14836(), 4B]+[HIDWORD(v1) ^ v5[1], 4B] + [byte_1FFE0290, 64B] + [dword_1FFE0114, 4B] + [dword_1FFE00C8,4B], total_len = 80B

dword_20023AEA:
    [table[tid],32B] + [input[2:34],32B] + [sha1(dword_20023B68), 20B], total_len = 84B

output:
    [input[0:2], 2B] + [sha1(dword_20023AEA), 20B] + [sha1(dword_20023B68), 20B], total_len = 42B
```

As you can see, the secret here only depends on the lookup table, which is the same in all controllers. You should know how to get a copy of the controller's firmware.

You can test it with the following input&output example:

```
input example (I2C):
0x60 0x01 0x04 0x22 0x11 0x00 0x78 0x06 0x4A 0xC4 0x11 0xF6 0xC4 0x3B 0x97 0x96 0xB2 0xDC 0x93 0x4F  
0x60 0x01 0x9A 0x32 0x04 0x52 0x60 0x05 0x27 0x0E 0xC7 0x1E 0xD0 0x82 0x2C 0xF4 0x44 0x9D 
0x60 0x00 0x16 0xD1  
0x60 0x83 

output example (I2C):
0x61 0x11 0x00 0xC0 0x11 0x47 0x0C 0x76 0x08 0xEB 0x92 0x33 0xD9 0xF2 0x12 0xB5 0x7C  
0x60 0x83 
0x61 0x0F 0xAE 0x00 0x91 0xC3 0x2A 0xCD 0x53 0xE3 0xE2 0xD8 0xA8 0x3F 0x1A 0x45 0x5A  
0x60 0x82 
0x61 0x47 0x70 0xCE 0x16 0x6F 0x5E 0x74 0x8C 0x41 0x65

```

Thanks for the help from [@dogtopus](https://github.com/dogtopus)
