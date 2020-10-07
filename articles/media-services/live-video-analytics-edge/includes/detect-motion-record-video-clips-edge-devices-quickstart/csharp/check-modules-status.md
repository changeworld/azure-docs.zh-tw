---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682096"
---
在[產生和部署 IoT Edge 部署資訊清單](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)步驟中，於 Visual Studio Code 中展開 [AZURE IOT 中樞] 底下的 [lva-sample-device] 節點 (位於左下方區段)。 您應該會看到下列部署的模組：

* 名為 `lvaEdge` 的 Live Video Analytics 模組
* 名為 `rtspsim` 的模組，其會模擬作為即時影片摘要來源的 RTSP 伺服器

  ![模組](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 如果您使用自己的邊緣裝置，而不是我們的設定指令碼所佈建的裝置，請移至您的邊緣裝置，並以**管理員權限**執行下列命令，以提取並儲存用於本快速入門的範例影片檔案：  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
