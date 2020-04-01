---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 590e5494a8c8f9d4e06b69af0708e83d53be72b5
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409640"
---
使用[GStreamer](https://gstreamer.freedesktop.org)實現處理壓縮音訊。 出於許可原因,GStreamer 二進位檔未編譯並與語音 SDK 連結。 開發人員需要安裝多個依賴項和外掛程式。

# <a name="ubuntu-1604-1804-or-debian-9"></a>[烏本圖, 16.04, 18.04 或 Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL / CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> 在 RHEL / CentOS 上,請按照有關如何[為 Linux 配置 OpenSSL](../../../../how-to-configure-openssl-linux.md)的說明進行操作。

---