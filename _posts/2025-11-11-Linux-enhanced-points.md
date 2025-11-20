---
title: "Linux2"
date: 2025-11-11 10:00:00 +0800
categories: [CS basics, Linux] # [Top_category, sub_category]
tags: [linux]      # TAG names should always be lowercase
# author:gaoshan
# add image: ![img-description](/assets/img/os.jpg)
---

Linux
Command line summary: https://bootlin.com/doc/legacy/command-line/command_memento.pdf 

Vi summary https://bootlin.com/doc/legacy/command-line/vi_memento.pdf 

### 深度解析: 软连接和硬连接 symbolic link and hard link

核心概念：inode / i节点

要理解它们，首先必须了解Unix文件系统的核心——inode。

inode 是文件系统中的一个数据结构，它存储了文件的元数据，例如：

文件权限、文件所有者、文件大小、文件创建/修改时间

指向文件数据块的指针

重要：inode不存储文件名。文件名只是人类可读的标签。

目录的本质是一个特殊文件，它里面存储着一张表，将文件名映射到inode号。
可以把inode想象成房子的地址，而文件名就是门牌号。多个门牌号可以指向同一个地址（硬链接），而一个指向门牌号的路标就是软链接。

软连接指向文件路径,而硬连接指向文件的inode(删除原文件后仍可访问)
```bash
# EXAMPLES: 
#Create a symbolic link named /home/src and point it to /usr/src:软连接类似于windows的快捷键.
ln -s /usr/src /home/src  # 类似快捷方式，可跨文件系统；目标删除后，链接失效。
ln -s /etc/sysconfig/network-scripts/ifcfg-ens33 ip # 
# Hard link /usr/local/bin/fooprog to file /usr/local/bin/fooprog-1.0:
ln /usr/local/bin/fooprog-1.0 /usr/local/bin/fooprog #硬连接类似于动态备份(修改一个会导致另个file跟着变)
cp a.txt b.txt #静态备份
ln a.txt b.txt #动态备份
```
drwxrwxr-x 2 dev_lead developers 27 Sep 3 16:00 docs

这里的2指的是这个文件夹有 2个 硬连接. 硬连接创建后，你无法分辨哪个是“原始”文件，哪个是“链接”。它们是完全平等的，都指向同一个i节点。系统只是记录这个i节点有多少个“链接计数”。

无法跨文件系统：因为i节点号是相对于当前文件系统的，你不能创建一个硬链接指向另一个硬盘分区或设备上的文件。

删除操作的本质：删除一个文件（包括硬链接）实际上做的是：

在目录中删除该文件名到i节点的记录。
将i节点的“链接计数”减1。
只有当链接计数减为0时，系统才会真正释放这个i节点和其对应的数据块，文件内容才会被删除。
所以，只要你还有一个硬链接存在，文件数据就还在。


软链接是一个独立的文件，它有自己独立的i节点。

这个文件的内容很特殊——它存储的是目标文件的路径。当你访问软链接时，系统会读取这个路径，然后去找到那个路径下的文件。

## 实际应用场景
**硬链接的应用**

1.文件备份与防误删：

如果你有一个非常重要的文件，可以为其创建一个硬链接。这样，即使你“删除”了其中一个（链接数减1），只要链接数不为0，数据依然安全。这比复制一份更节省空间，因为数据只有一份。

2.文件系统的内部机制：

每个目录至少有两个硬链接：自身的条目（.）和其父目录的条目（..）。这就是为什么新创建的空目录链接数通常是2。

**软链接的应用**

1.软件版本管理：

非常经典的应用。例如，系统中安装了 Python 3.9 和 Python 3.11。你可以创建一个软链接 /usr/bin/python3 -> /usr/bin/python3.11。当你想切换到 Python 3.9 时，只需删除并重新创建这个软链接即可，所有调用 python3 的程序都会自动使用新版本。
gcc, java 等开发工具链也常用这种方式。

2.快捷方式与路径简化：

将一个深层次目录链接到你的主目录下，方便快速访问。例如 ln -s /var/www/html/my_complex_project ~/myproject。
动态库版本控制：

在 /usr/lib 目录下，你会看到 libc.so.6 这样的软链接，指向实际的库文件 libc-2.31.so。这样程序只需要链接 libc.so.6，而无需关心具体的版本号。
