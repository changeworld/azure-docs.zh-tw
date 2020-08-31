---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690925"
---
1. 在 Visual Studio Code 中，移至 *src/edge*。 您會看到 *.env* 檔案和一些部署範本檔案。

    部署範本會參照邊緣裝置的部署資訊清單，其中會使用變數來代表某些屬性。 *.env* 檔案包含這些變數的值。
1. 移至 *src/cloud-to-device-console-app* 資料夾。 在這裡，您會看到 *appsettings.json* 檔案和其他幾個檔案：

    * ***operations.json*** - 您想要讓程式執行的作業清單。
    * **main.py** - 範例程式碼。 此程式碼：
    
      * 載入應用程式設定。
      * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](../../../direct-methods.md)來分析即時影片串流。
      * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
      * 叫用直接方法來清除資源。   

