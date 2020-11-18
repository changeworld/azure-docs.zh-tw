---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358196"
---
1. 在 Visual Studio Code 中，瀏覽至 src/edge。 您會看到您所建立的 .env 檔案，以及一些部署範本檔案。

    部署範本會使用一些預留位置值來參考邊緣裝置的部署資訊清單。 .env 檔案具有這些變數的值。
1. 接下來，瀏覽至 src/cloud-to-device-console-app 資料夾。 您在這裡會看到您所建立的 appsettings.json 檔案，以及一些其他檔案：

   * operations.json - 此檔案會列出您需要程式執行的不同作業。
   * main.py - 這是執行下列動作的範例程式碼：
    
        * 載入應用程式設定。
        * 在 IoT Edge 模組的直接方法上叫用即時影片分析，以建立拓撲、具現化圖表並啟動圖表。
        * 暫停一會，讓您在 [終端] 視窗中檢查圖表輸出，以及在 [輸出] 視窗中檢查傳送至 IoT 中樞的事件。
        * 停用圖表執行個體、刪除圖表執行個體，以及刪除圖表拓撲。
