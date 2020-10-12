---
title: Azure HPC Cache 資料內嵌-平行複製腳本
description: 如何使用平行複製腳本，將資料移至 Azure HPC Cache 中的 Blob 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: ff7b15a36c5ef19a1fa6ffdca7697dd6ba97c29f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092349"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC Cache 資料內嵌-平行複製腳本方法

本文提供的指示說明如何建立 ``parallelcp`` 腳本，並使用它將資料移至 Blob 儲存體容器，以搭配 Azure HPC Cache 使用。

若要深入瞭解如何將資料移至 Azure HPC Cache 的 Blob 儲存體，請參閱 [將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)。

## <a name="create-the-parallelcp-script"></a>建立 parallelcp 腳本

下方指令碼會新增 `parallelcp` 可執行檔。 (此指令碼是專為 Ubuntu 而設計的；如果使用另一個散發套件，則必須個別安裝 ``parallel``)。

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>平行複製範例

這個範例會使用平行複製腳本， ``glibc`` 在 Azure HPC Cache 中使用來源檔案進行編譯。

來源檔案會在 Azure HPC Cache 掛接點中快取，而物件檔案會儲存在本機硬碟上。

此範例使用平行複製腳本搭配選項 ``-j`` ， ``make`` 以進行平行處理。

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
