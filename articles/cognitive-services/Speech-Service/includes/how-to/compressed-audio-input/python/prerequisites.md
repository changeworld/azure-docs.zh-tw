---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282720"
---
處理壓縮的音訊是使用來執行 [`GStreamer`](https://gstreamer.freedesktop.org) 。 基於授權原因， `GStreamer` 二進位檔不會與語音 SDK 進行編譯和連結。 開發人員必須安裝數個相依性和外掛程式，請參閱 [在 Windows 上安裝](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 `GStreamer` 二進位檔必須位於系統路徑中，才能讓語音 SDK 在執行時間載入二進位檔。 如果語音 SDK 在執行時間可以找到 `libgstreamer-1.0-0.dll` ，這表示二進位檔位於系統路徑中。

