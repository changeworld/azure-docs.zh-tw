---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421721"
---
使用[GStreamer](https://gstreamer.freedesktop.org)實現處理壓縮音訊。 出於許可原因,GStreamer 二進位檔未編譯並與語音 SDK 連結。 開發人員需要安裝多個依賴項和外掛程式,請參閱[在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 Gstreamer 二進位檔案需要位於系統路徑中,以便語音 SDK 可以在執行時載入 gstreamer 二進制檔。 如果語音 SDK 能夠在執行時找到 libgstreamer-1.0-0.dll,則意味著 gstreamer 二進位檔案位於系統路徑中。

