---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67173787"
---
透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 Parameters 區段，內含所有參數值。
您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。 請不要為永遠保持不變的值定義參數。 每個參數值都可在範本中用來定義所部署的資源。 

在定義參數時，請使用 [ **allowedValues** ] 欄位來指定使用者可以在部署期間提供的值。 使用 [ **defaultValue** ] 欄位來指派部署期間未提供任何值時的參數值。

我們將說明範本中的每個參數。

### <a name="sitename"></a>siteName
想要建立之 Web 應用程式的名稱。

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
用來主控 Web 應用程式之 App Service 方案的名稱。

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
主控方案的定價層。

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

此範本定義這個參數允許的值，並指派未指定任何值時的預設值 (S1)。

### <a name="workersize"></a>workerSize
主控方案的執行個體大小 (小型、中型或大型)。

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

範本會定義此參數允許使用的值 (0、1 或 2)，並指派未指定任何值時的預設值 (0)。 這些值分別對應到小型、中型和大型。

