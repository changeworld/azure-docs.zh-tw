---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821548"
---
處理壓縮的音訊是使用 [GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權理由，GStreamer 二進位檔不會與語音 SDK 進行編譯和連結。 開發人員必須安裝數個相依性和外掛程式，請參閱 [在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 或 [在 Linux 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)。 GStreamer 二進位檔必須位於系統路徑中，才能讓語音 SDK 在執行時間載入二進位檔。 例如，在 Windows 上，如果語音 SDK 可在 `libgstreamer-1.0-0.dll` 執行時間期間找到，則表示 gstreamer 二進位檔位於系統路徑中。

