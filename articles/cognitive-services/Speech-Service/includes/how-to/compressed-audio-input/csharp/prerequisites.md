---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637305"
---
使用[GStreamer](https://gstreamer.freedesktop.org)實現處理壓縮音訊。 出於許可原因,GStreamer 二進位檔未編譯並與語音 SDK 連結。 開發人員需要安裝多個依賴項和外掛程式,請參閱[在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 Gstreamer 二進位檔案需要位於系統路徑中,以便語音 SDK 可以在執行時載入 gstreamer 二進制檔。 如果語音 SDK 能夠在執行時找到 libgstreamer-1.0-0.dll,則意味著 gstreamer 二進位檔案位於系統路徑中。

