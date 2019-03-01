---
title: "Mac迁移导致Golang无法debug的问题修复"
date: 2019-03-01T07:13:54+08:00
draft: false
---

最近，使用Mac的迁移助手之后，突然出现一个奇怪的问题。我的golang 用goland 或者vscode都会出现间歇性的无法debug，表现为第一次debug，断点可以停下来。再调试一遍就会直接走过

而如果你直接在项目中执行

```
dlv debug 
```
会看到如下的报错

```
Unexpected header files xxxx uint64_t 之类的
```

网上搜了一下都没有解决。。

并给出了具体的文件目录。这个看着是c库的一些文件，当时我记得迁移完成后，brew提示有一些库有问题。

执行
```
brew doctor
```

会看到

```
Warning: Unbrewed header files were found in /usr/local/include.
If you didn't put them there on purpose they could cause problems when
building Homebrew formulae, and may need to be deleted.

Unexpected header files:
  /usr/local/include/node/openssl/archs/BSD-x86_64/asm/crypto/include/internal/bn_conf.h
  /usr/local/include/node/openssl/archs/BSD-x86_64/asm/crypto/include/internal/dso_conf.h
  /usr/local/include/node/openssl/archs/BSD-x86_64/asm/include/openssl/opensslconf.h
  /usr/local/include/node/openssl/archs/BSD-x86_64/no-asm/crypto/buildinf.h
  /usr/local/include/node/openssl/archs/BSD-x86_64/no-asm/crypto/include/internal/bn_conf
```

于是，拷贝下来，一个for循环，将这些文件全部删除。之后再执行dlv debug 就好了。。