---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421721"
---
處理壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，GStreamer 二進位檔不會使用語音 SDK 進行編譯和連結。 開發人員必須安裝數個相依性和外掛程式，請參閱[在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 Gstreamer 二進位檔必須位於系統路徑中，讓語音 SDK 可以在執行時間載入 Gstreamer 二進位檔。 如果語音 SDK 在執行時間期間能夠找到 libgstreamer-1.0 4.9.0-，則表示 gstreamer 二進位檔在系統路徑中。

