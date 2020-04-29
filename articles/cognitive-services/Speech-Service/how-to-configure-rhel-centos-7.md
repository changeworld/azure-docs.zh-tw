---
title: 如何設定 RHEL/CentOS 7-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定 RHEL/CentOS 7，讓語音 SDK 可以使用。
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639180"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>設定 RHEL/CentOS 7 for Speech SDK

Red Hat Enterprise Linux （RHEL） 8 x64 和 CentOS 8 x64 已由語音 SDK 版本1.10.0 和更新版本正式支援。 您也可以在 RHEL/CentOS 7 x64 上使用語音 SDK，但這需要更新您系統上的 c + + 編譯器（適用于 c + + 開發）和共用 c + + 執行時間程式庫。

若要檢查 c + + 編譯器版本，請執行：

```bash
g++ --version
```

如果已安裝編譯器，輸出看起來應該像這樣：

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

此訊息可讓您知道已安裝 GCC 主要第4版。 此版本沒有適用于語音 SDK 所使用之 c + + 11 標準的完整支援。 嘗試使用此 GCC 版本和語音 SDK 標頭編譯 c + + 程式將會導致編譯錯誤。

檢查共用 c + + 執行時間程式庫（libstdc + +）的版本也很重要。 大部分的語音 SDK 會實作為原生 c + + 程式庫，這表示它相依于 libstdc + +，而不論您用來開發應用程式的語言為何。

若要在您的系統上尋找 libstdc + + 的位置，請執行：

```bash
ldconfig -p | grep libstdc++
```

Vanilla RHEL/CentOS 7 （x64）上的輸出為：

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

根據此訊息，您會想要使用下列命令來檢查版本定義：

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

語音 SDK 需要**CXXABI_1 3.9**和**GLIBCXX_3. 4.21**。 您可以從 Linux 套件的語音`ldd libMicrosoft.CognitiveServices.Speech.core.so` SDK 程式庫執行來尋找這項資訊。

> [!NOTE]
> 建議您在系統上安裝的 GCC 版本至少為**5.4.0**，且具有相符的執行時間程式庫。

## <a name="example"></a>範例

這是一個範例命令，說明如何使用語音 SDK 1.10.0 或更新版本來設定 RHEL/CentOS 7 x64 for 開發（c + +、c #、JAVA、Python）：

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
