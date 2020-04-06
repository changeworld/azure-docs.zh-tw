---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 14f9d0936a4d1949cf8d7fc69bbb782ee447bdba
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668770"
---
:::row:::
    :::column span="3":::
        語音 SDK 僅支援**Ubuntu 16.04/18.04、Debian** **9、****紅帽企業 Linux (RHEL) 7/8**和**CentOS 7/8**與 Linux 一起使用時在以下目標體系結構上:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 當瞄準Linux ARM64時,並使用 C# - .NET Core 3.x(dotnet-sdk-3.x 包)是必需的。 如果您的目標是 ARM32 或 ARM64,則不支援 Python。

> [!NOTE]
> Ubuntu 16.04、Ubuntu 18.04 和 Debian 9 的 x86 體系結構僅支援使用語音 SDK C++開發。

### <a name="system-requirements"></a>系統需求

對於本機應用程式,語音 SDK`libMicrosoft.CognitiveServices.Speech.core.so`依賴於 。 確保目標體系結構 (x86, x64) 與應用程式匹配。 根據 Linux 版本,可能需要其他依賴項。

- GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
- OpenSSL`libssl.so.1.0.0`函`libssl.so.1.0.2`式庫( 或 )
- ALSA 應用程式的共用程式庫 (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[烏本圖 16.04/18.04](#tab/ubuntu)

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
> 按照有關如何[為語音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)的說明。

> [!TIP]
> 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](../how-to-configure-openssl-linux.md) 上的指示進行。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
