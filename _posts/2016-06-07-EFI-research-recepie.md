---
layout: post
title: EFI investigation recepie
subtitle: How to get to the inside of you EFI BIOS
---

No, I am not Phineas Fisher, but I do think that good security education benefits everyone. Security through obscurity is only good
against the most lazy attacker, making it now, in the age of dedicated, well funded and even state sponsored adversaries, an increasingly dangerous attitude.

So, to get to the insides of your BIOS crypto, do this:
1. Download the EFI BIOS distribution for your motherboard from the vendor site. Most of them have BIOS's dating back several years.

2. The package you downloaded has the testin/flashing etc built in. You need to extract the actual EFI code from the distribution.
Most of the time you could just use a standard archive extractor to get it out. Sometimes you need to search for archive identifier inside the package. Other times you might need a specialized extractor ("extract-hdr-from-exe")

3. Now unpack your EFI code with the UEFIExtract that is a part of the UEFITool

4. You get a big bunch of stuff. Do a global search 

5. These are standard ELF files. Get your favorite disassembler/decompiler out and have fun.

6. If you get stuck, use UEFIReverse's EFI environment emulation to actually RUN the code on your platform. You can then debug the code in your favorite debugger, set watches and view states. The reversing galore!

Hopefully this helps vendors understand the issues they are facing (encrypt your binaries!), and gives security expects a couple of hints (EFI reversing is simple).
