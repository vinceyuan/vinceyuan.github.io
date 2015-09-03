---
layout:     post
title:      Build SQLCipher on Mac OS X
category:   blog
description: SQLCipher is encrypted SQLite. The following are the steps to build it on Mac OS X.
---

SQLCipher is encrypted SQLite. The following are the steps to build it on Mac OS X.

###Donwload and build OpenSSL code.

```
$ curl -o openssl-1.0.0e.tar.gz http://www.openssl.org/source/openssl-1.0.0e.tar.gz
$ tar xzf openssl-1.0.0e.tar.gz
$ cd openssl-1.0.0e
$ ./Configure darwin64-x86_64-cc
$ make
// 'make install' if you want to install it
```

Now we get `libcrypto.a` in the same folder.

###Download and build SQLCipher code.

In another directory,

```
$ git clone https://github.com/sqlcipher/sqlcipher.git
$ cd sqlcipher
```

Change `/path/to/libcrypto.a` in the following command to your path

```
$ ./configure --enable-tempstore=yes CFLAGS="-DSQLITE_HAS_CODEC" LDFLAGS="/path/to/libcrypto.a"
$ make
```

Now we get sqlcipher executable with static linking. However it does not work well for me. I have to build sqlcipher again with dynamic linking.

```
$ make clean
$ ./configure --enable-tempstore=yes CFLAGS="-DSQLITE_HAS_CODEC" LDFLAGS="-lcrypto"
$ make
```

We get sqlcipher executable again. This one works for me.

###Try sqlcipher

Go to parent folder

```
$ cd ..
```

Create a db without encryption.

```
$ sqlcipher/sqlcipher hello
sqlite> create table test(id integer);
sqlite> .q
$ hexdump -C hello | head -15
00000000  53 51 4c 69 74 65 20 66  6f 72 6d 61 74 20 33 00  |SQLite format 3.|
00000010  04 00 01 01 00 40 20 20  00 00 00 01 00 00 00 02  |.....@  ........|
00000020  00 00 00 00 00 00 00 00  00 00 00 01 00 00 00 04  |................|
00000030  00 00 00 00 00 00 00 00  00 00 00 01 00 00 00 00  |................|
00000040  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000050  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 01  |................|
00000060  00 2d e2 29 0d 00 00 00  01 03 cd 00 03 cd 00 00  |.-.)............|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
000003c0  00 00 00 00 00 00 00 00  00 00 00 00 00 31 01 06  |.............1..|
000003d0  17 15 15 01 47 74 61 62  6c 65 74 65 73 74 74 65  |....Gtabletestte|
000003e0  73 74 02 43 52 45 41 54  45 20 54 41 42 4c 45 20  |st.CREATE TABLE |
000003f0  74 65 73 74 28 69 64 20  69 6e 74 65 67 65 72 29  |test(id integer)|
00000400  0d 00 00 00 00 04 00 00  00 00 00 00 00 00 00 00  |................|
00000410  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
```

create a db with encryption.

```
$ sqlcipher/sqlcipher hello2

sqlite> PRAGMA key='mypass';
sqlite> PRAGMA cipher = 'aes-256-cfb';
sqlite> create table test(id integer);
sqlite> .q
```

Now the db is encrypted.

```
$ hexdump -C hello2 | head -15
00000000  1a b9 66 10 2e 09 ec f6  8a de 82 bc 59 1f 02 c1  |..f.........Y...|
00000010  5f 75 8b 94 f6 ec e9 d6  05 ad 20 57 32 10 42 a6  |_u........ W2.B.|
00000020  8e fe 23 ab cf 55 e5 dd  9d ba 99 f6 a9 16 fa f6  |..#..U..........|
00000030  61 3f 9a 80 68 15 fb 87  e8 ee 34 61 1a 30 ed cc  |a?..h.....4a.0..|
00000040  9e 5f a2 d9 19 8b 52 17  e7 6f 10 80 5b 67 88 b7  |._....R..o..[g..|
00000050  ce 13 2a 9b dc c2 28 b6  85 a3 22 81 45 7e 50 cd  |..*...(...".E~P.|
00000060  54 6a 56 2d af 69 7e 8d  4c 57 0d d2 e7 09 76 22  |TjV-.i~.LW....v"|
00000070  f2 41 79 cc 70 f7 3c d6  03 ac 0c 55 b9 58 fe ca  |.Ay.p.<....U.X..|
00000080  92 e6 50 de 26 26 0f 89  80 97 b1 12 4f ea 46 4d  |..P.&&......O.FM|
00000090  c8 c9 bc 7a 72 4f 16 3c  a4 c8 48 76 5b de f3 67  |...zrO.<..Hv[..g|
000000a0  c6 f9 28 d5 be 66 c3 17  19 e7 0e c6 8c a1 98 77  |..(..f.........w|
000000b0  c8 fe 8f bc 06 ca 73 25  f1 55 45 01 ed 6d 01 41  |......s%.UE..m.A|
000000c0  90 79 28 cb 4d b5 f2 98  0b 55 26 b8 5c 5f 5b 91  |.y(.M....U&.\_[.|
000000d0  15 bd 5a c2 4e 68 2b 47  79 62 0f 8a f5 bf 5d f6  |..Z.Nh+Gyb....].|
000000e0  c3 8c 9d c4 3c 3d 74 5e  1f 60 8c 33 b3 1f d2 e4  |....<=t^.`.3....|
```

Note: I found there are some problems in changing default codec. I failed to read the encrypted db. Do not run this line:

```
PRAGMA cipher = 'aes-256-cfb';
```

