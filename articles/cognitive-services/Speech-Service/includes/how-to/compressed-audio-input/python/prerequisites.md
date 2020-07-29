---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282720"
---
處理壓縮的音訊會使用來執行 [`GStreamer`](https://gstreamer.freedesktop.org) 。 基於授權原因， `GStreamer` 二進位檔不會使用語音 SDK 進行編譯和連結。 開發人員必須安裝數個相依性和外掛程式，請參閱[在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 `GStreamer`二進位檔必須位於系統路徑中，讓語音 SDK 可以在執行時間載入二進位檔。 如果語音 SDK 能夠在執行時間中找到 `libgstreamer-1.0-0.dll` ，則表示二進位檔在系統路徑中。

