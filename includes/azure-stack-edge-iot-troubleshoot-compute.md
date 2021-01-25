---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761536"
---
使用 IoT Edge 代理程式執行時間回應，針對計算相關的錯誤進行疑難排解。 以下是可能回應的清單：

* 200 - 確定
* 400 - 部署設定不正確或無效。
* 417-裝置沒有部署設定集。
* 412 - 部署設定中的結構描述版本無效。
* 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
* 500 - IoT Edge 執行階段發生錯誤。

如需詳細資訊，請參閱 [IoT Edge 代理程式](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)。