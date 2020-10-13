---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a93c478f0621bf62b710f58f3e6f06298bad9954
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673045"
---
:::row:::
    :::column span="3":::
        語音 SDK 在搭配 Linux 使用時，僅支援下列目標架構上的 **Ubuntu 16.04/18.04**、 **Debian 9**、 **Red Hat Enterprise Linux (RHEL) 7/8**和 **CentOS 7/8** ：
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- 適用于 c + + 開發的 x86 (Debian/Ubuntu) 、x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu) 
- x64、ARM32 (Debian/Ubuntu) 和 ARM64 (適用于 JAVA 的 Debian/Ubuntu) 
- 適用于 .NET Core 的 x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu) 
- 適用于 Python 的 x64

> [!IMPORTANT]
> 針對 Linux ARM64 上的 c #，需要 .NET Core 3.x (dotnet-6.x 套件) 。

### <a name="system-requirements"></a>系統需求

若為原生應用程式，語音 SDK 會依賴 `libMicrosoft.CognitiveServices.Speech.core.so` 。 請確定 (x86、x64) 的目標架構與應用程式相符。 視 Linux 版本而定，可能需要額外的相依性。

- GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
- OpenSSL 程式庫 (`libssl.so.1.0.0` 或 `libssl.so.1.0.2`) 
- ALSA 應用程式的共用程式庫 (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 和 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - 在 RHEL/CentOS 7 上，依照[如何設定適用於 Speech SDK 的 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的指示進行。
> - 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
