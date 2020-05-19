---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: b38857cd5ea12767f872690dfcdcb84dcbbb3f7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585008"
---
處理壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，GStreamer 二進位檔不會使用語音 SDK 進行編譯和連結。 開發人員必須安裝數個相依性和外掛程式。

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu、16.04、18.04 或 Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos-78"></a>[RHEL/CentOS 7/8](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，遵循[如何設定 RHEL/CentOS 7 For SPEECH SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)的指示。
> - 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

> [!IMPORTANT]
> RHEL/CentOS 7 上的語音 SDK 不支援 Opus 音訊編碼格式。

---