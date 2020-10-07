---
ms.openlocfilehash: bc24d6670cbf0c2ac72c9a1d100467b8724779b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682102"
---
在本快速入門的必要條件中，您已將範例程式碼下載至資料夾。 請遵循下列步驟來檢查和編輯範例程式碼。

1. 在 Visual Studio Code 中，移至 *src/edge*。 您會看到 *.env* 檔案和一些部署範本檔案。

    部署範本會參照邊緣裝置的部署資訊清單，其中會使用變數來代表某些屬性。 *.env* 檔案包含這些變數的值。
1. 移至 *src/cloud-to-device-console-app* 資料夾。 在這裡，您會看到 *appsettings.json* 檔案和其他幾個檔案：
    * ***c2d-console-app.csproj*** - Visual Studio Code 的專案檔。
    * ***operations.json*** - 您想要讓程式執行的作業清單。
    * ***Program.cs*** - 範例程式碼。 此程式碼：

        * 載入應用程式設定。
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](../../../direct-methods.md)來分析即時影片串流。 
        * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
        * 叫用直接方法來清除資源。

1. 編輯 *operations.json* 檔案：
    * 變更圖表拓撲的連結：

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * 在 `GraphInstanceSet` 底下，編輯圖形拓撲的名稱，使其符合前一個連結中的值：
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * 編輯 RTSP URL，以指向影片檔案：

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * 在 `GraphTopologyDelete` 底下，編輯名稱：

        `"name": "EVRToFilesOnMotionDetection"`
