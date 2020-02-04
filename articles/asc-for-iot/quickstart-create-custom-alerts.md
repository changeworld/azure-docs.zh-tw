---
title: 快速入門：為適用於 IoT 的 Azure 資訊安全中心建立自訂警示
description: 了解、建立及指派適用於 IoT 的 Azure 資訊安全中心安全性服務的自訂裝置警示。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 510a2c3f04cc4c307acad18ee3012d9407155f9f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766453"
---
# <a name="quickstart-create-custom-alerts"></a>快速入門：建立自訂警示


使用自訂的安全性群組和警示，利用端對端安全性資訊和類別裝置知識，來確保您的 IoT 解決方案有更健全的安全性。 

## <a name="why-use-custom-alerts"></a>為何要使用自訂警示？ 

您最了解您自己的 IoT 裝置。

對於徹底了解其裝置預期行為的客戶，適用於 IoT 的 Azure 資訊安全中心可讓您將這方面的了解轉化為裝置行為原則，並對不在所預期正常行為內的任何偏差舉動發出警示。

## <a name="security-groups"></a>安全性群組

安全性群組可讓您定義裝置的邏輯群組，並集中管理其安全性狀態。

這些群組可以代表配備了特定硬體的裝置、部署在特定位置的裝置，或任何其他適用於特定需求的群組。

安全性群組可透過名為 **SecurityGroup** 的裝置對應項標籤屬性加以定義。 根據預設，IoT 中樞上的每個 IoT 解決方案都會有一個名為 **default** 的安全性群組。 變更 **SecurityGroup**屬性的值，就能變更裝置的安全性群組。
 
例如：

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

請使用安全性群組來將裝置分組到不同的邏輯類別。 在建立群組後，請將其指派至您所選擇的自訂警示，以獲得最有效的端對端 IoT 安全性解決方案。 

## <a name="customize-an-alert"></a>自訂警示

1. 開啟 IoT 中樞。 
2. 按一下 [安全性]  區段中的 [自訂警示]  。 
3. 選擇要作為自訂內容套用對象的安全性群組。 
4. 按一下 [新增自訂警示]  。
5. 從下拉式清單中選取自訂警示。 
6. 編輯所需屬性，按一下 [確定]  。
7. 請務必按一下 [儲存]  。 若未儲存新的警示，該警示會在您下一次關閉 IoT 中樞時遭到刪除。

 
## <a name="alerts-available-for-customization"></a>可供自訂的警示

適用於 IoT 的 Azure 資訊安全中心提供了大量警示，您可以根據本身的特定需求加以自訂。 請檢閱[可自訂的警示資料表](concept-customizable-security-alerts.md)，以了解警示嚴重性、資料來源、描述，以及您在收到每個警示時可參考的建議補救步驟。 
|


## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何部署安全性代理程式...

> [!div class="nextstepaction"]
> [部署安全性代理程式](how-to-deploy-agent.md)
