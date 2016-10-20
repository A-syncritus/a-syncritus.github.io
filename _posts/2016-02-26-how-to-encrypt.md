---
layout: post
title: The deadman's treasure
subtitle: How to encrypt with multiple keys
# bigimg: /images/path.jpg
---

What if you wanted to make your sensitive data recoverable in case of your untimely demise or, less grimly, your incapacitation, whatever form it may come in. But you don't want any one person to recover it by themselves.

The obvious solution is to encrypt your data with several keys. Destribute that data to several people, along with their keys and to keep one keep to yourself, but make it location known to others.

Sounds good, but how do you do that? Well you can not use PGP to encrypt your data, because PGP uses *parallel* keys, meaning that anyone with a key can open the data, so multiplying the keys only makes the data more accessible, not less.
Instead you need to use chained symmetric encryption. Here is an example with three keys:

Turn swap off. Remember the device it printed, you'll need it later
`sudo swapoff -va`
Create a memory disk
`sudo mount -t tmpfs -o size=128m tmpfs /media/Temp/`
Save all your data unencrypted on the ramdisk, into a folder called "data". Zip it.
`zip data.zip data/`
Generate 3 keys and do successive encryption - store on the ramdisk

```sh
cp data.zip data.enc0
for i in {1..3}
do
	openssl rand 8 > salt-$i
	openssl rand 32 > key-$i
	openssl rand 16 > iv-$i
	openssl enc -aes-256-cbc -K $(hexdump -v -e '/1 "%02X"' < key-$i) -S $(hexdump -v -e '/1 "%02X"' < salt-$i) -iv $(hexdump -v -e '/1 "%02X"' < iv-$i) -in data.enc$((i-1)) -out data.enc$i
done
```

Test by decrypting. The last two sha sums should match.

```sh
rm data.enc0 data.enc1 data.enc2
for i in {3..1}
do
	openssl enc -d -aes-256-cbc -K $(hexdump -v -e '/1 "%02X"' < key-$i) -S $(hexdump -v -e '/1 "%02X"' < salt-$i) -iv $(hexdump -v -e '/1 "%02X"' < iv-$i) -in data.enc$i -out data.enc$((i-1))
done
sha256sum data.enc0 data.zip
```

Create instructions

```sh
for i in {3..1}
do
	echo -e "You need 3 keys to decrypt the contents, starting from key 3, going to 2 and 1.\nThis is key #$i\nOnce it is your turn to use your key, run the following command to decrypt:\n\n" > instructions-$i
	echo openssl enc -d -aes-256-cbc -K $(hexdump -v -e '/1 "%02X"' < key-$i) -S $(hexdump -v -e '/1 "%02X"' < salt-$i) -iv $(hexdump -v -e '/1 "%02X"' < iv-$i) -in data.enc$i -out data.enc$((i-1)) >> instructions-$i
	echo -e "\nThen give the result to the next key holder.\ndata.enc0 file will be the fully unencrypted zip file.\nThe SHA256 sum for the fully encrypted data file data.enc3 is $(sha256sum data.enc3 | awk '{print $1}' )"  >> instructions-$i
done
```

Cleanup

```sh
rm data.enc0 data.zip
rm salt-1 key-1 iv-1 data.enc1
rm salt-2 key-2 iv-2 data.enc2
rm salt-3 key-3 iv-3
```

Print instructions. Please note that doing remote printing or using the lpr command may send your keys over the network and may save it in the printer's spool. Instead, copy instructions onto a ramdisk on the print server and run the following from the print server:

```sh
for i in {1..3}
do
	cat instructions-$i | fold -w 80 | unix2dos | sudo tee /dev/usb/lp1
done
```

Copy the encrypted file to various places. Blow away the ramdisk
`sudo umount /media/Temp`
Turn swap on
`swapon <devicename>`
Check that it's on by running
`free`
For best results reboot your machine so all the latent memory contents is wiped as well.
