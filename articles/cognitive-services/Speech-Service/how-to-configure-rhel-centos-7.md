---
title: 如何設定 RHEL/CentOS 7 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何配置 RHEL/CentOS 7 以便可以使用語音 SDK。
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639180"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>為語音 SDK 設定 RHEL/CentOS 7

紅帽企業 Linux (RHEL) 8 x64 和 CentOS 8 x64 得到語音 SDK 版本 1.10.0 和更高版本的正式支援。 也可以在 RHEL/CentOS 7 x64 上使用語音 SDK,但這需要更新C++編譯器(用於C++開發)和系統上的共用C++運行時庫。

要檢查C++編譯器版本,請運行:

```bash
g++ --version
```

如果安裝了編譯器,則輸出應如下所示:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

此消息讓您知道已安裝 GCC 主要版本 4。 此版本沒有完全支援語音 SDK 使用的 C++ 11 標準。 嘗試使用此 GCC 版本和語音 SDK 標頭編譯C++程式將導致編譯錯誤。

檢查共用C++運行時庫 (libstdc#) 的版本也很重要。 大多數語音 SDK 都是作為本機C++庫實現的,這意味著它依賴於 libstdc®,而不管您使用何種語言來開發應用程式。

要查找系統上 libstdc® 的位置,請執行:

```bash
ldconfig -p | grep libstdc++
```

香草 RHEL/CentOS 7 (x64) 上的輸出是:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

根據此訊息,您需要使用此指令檢查版本定義:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

輸出應該是：

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

語音 SDK 需要**CXXABI_1.3.9**和**GLIBCXX_3.4.21**。 您可以透過在 Linux`ldd libMicrosoft.CognitiveServices.Speech.core.so`套件的語音 SDK 庫上執行來尋找此資訊。

> [!NOTE]
> 建議安裝在系統上的 GCC 版本至少為**5.4.0,** 具有匹配的運行時庫。

## <a name="example"></a>範例

這是一個範例命令,說明如何使用語音 SDK 1.10.0 或更高版本配置 RHEL/CentOS 7 x64 用於開發(C++、C#、JAVA、Python):

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)
