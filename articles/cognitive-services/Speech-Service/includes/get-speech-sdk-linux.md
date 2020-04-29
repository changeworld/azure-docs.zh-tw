---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400018"
---
:::row:::
    :::column span="3":::
        在搭配 Linux 使用時，語音 SDK 僅支援下列目標架構上的**Ubuntu 16.04/18.04**、 **Debian 9**、 **Red Hat Enterprise Linux （RHEL） 7/8**和**CentOS 7/8** ：
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
> 以 Linux ARM64 為目標並使用 c # 時，需要 .NET Core 3.x （dotnet-3.x 套件）。 如果您的目標是 ARM32 或 ARM64，則不支援 Python。

> [!NOTE]
> Ubuntu 16.04、Ubuntu 18.04 和 Debian 9 的 x86 架構僅支援使用語音 SDK 進行 c + + 開發。

### <a name="system-requirements"></a>系統需求

針對原生應用程式，語音 SDK 會依賴`libMicrosoft.CognitiveServices.Speech.core.so`。 請確定目標架構（x86、x64）符合應用程式。 視 Linux 版本而定，可能需要額外的相依性。

- GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
- OpenSSL 程式庫（`libssl.so.1.0.0`或`libssl.so.1.0.2`）
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
> 請遵循[如何設定 RHEL/CentOS 7 For SPEECH SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)的指示。

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
