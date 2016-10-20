---
layout: post
title: Better BIOS password encryption
subtitle: Securing master EFI passwords better
---

There are many great EFI engineers working hard in creating the best and the greatest, who don't have the time to 
pause and understand proper crypto for the critical parts of the solutions they are working on. I thought I'd over some
basic do's and don'ts. Dogbert did this [before](https://dogber1.blogspot.com/2010/07/how-to-protect-better-secure-bios.html),
but some of them them rather impractical, so here is my list:

### Do
* Create a one time key from the hardware tag that is used to derive the master password. Don't store it anywhere, but RAM.
* Use public-private key cryptography:
** Create a public/private key pair for the master password. Store the private key securely, on a server with strong access oversight
** Store the public key in BIOS. Create a mechanism to change it in case the private key is compromised
** When the master password is entered, decrypt it with a stored public key
* Allow a limited number of free master password generations for customers by the support group. This way all the honest uses are covered and the 
shadowy third party password selling is discouraged.
* Pay attention to the security research in the field

### Do not
* Use weak public/private key algorithms, or keys (hi sony!). RSA with 2048 would work fine for the next 5 years.
* Use hashing, secure or not, or symmetric crypto. At some point you will have to store the IV or the key, and that can be reversed.
* Use random number generators initialized with just timestamps. Make sure to create good quality entropy.
* Use well known machine identifiers (serial/Mac etc) for generating the OTP without salting heavily. You don't want someone to generate OTPs offline
* Use TPM. Too many problems with it to get into details here. Just don't.

Of course there are many, many other topics about EFI security in general, but that's for another post.
