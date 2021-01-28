---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956238"
---
在[產生和部署 IoT Edge 部署資訊清單](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)步驟中，於 Visual Studio Code 中展開 [AZURE IOT 中樞] 底下的 [lva-sample-device] 節點 (位於左下方區段)。 您應該會看到下列部署的模組：

* 名為 `lvaEdge` 的 Live Video Analytics 模組
* 名為 `rtspsim` 的模組，其會模擬作為即時影片摘要來源的 RTSP 伺服器

  ![模組](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 上述步驟假設您使用的是安裝腳本所建立的虛擬機器。 如果您改為使用您自己的 edge 裝置，請移至您的 edge 裝置，並以系統 **管理員許可權** 執行下列命令，以提取和儲存本快速入門所使用的範例影片檔案：  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
