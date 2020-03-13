---
title: Azure IoT DPS IP 連線篩選器 |Microsoft Docs
description: 如何使用 IP 篩選來封鎖從特定 IP 位址到 Azure IoT DPS 實例的連線。 您可以封鎖來自個別 IP 位址或 IP 位址範圍的連接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284911"
---
# <a name="use-ip-filters"></a>使用 IP 篩選器

安全性是任何 IoT 解決方案的重要層面。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 Azure IoT 中樞裝置布建服務（DPS）的*IP 篩選器*功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

有兩個特定的使用案例，從特定 IP 位址封鎖對 DPS 端點的連線很有用：

* 您的 DPS 只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有專案。 例如，您使用 DPS 搭配[Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) ，在 dps 與您的裝置之間建立私人連線。

* 您必須從已被 DPS 系統管理員識別為可疑的 IP 位址拒絕流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選規則會套用在 DPS 實例層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。

從與您 DPS 實例中拒絕 IP 規則相符的 IP 位址進行的任何連線嘗試都會收到未獲授權401狀態碼和描述。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

根據預設，DPS 的入口網站中的**IP 篩選器**方格是空的。 此預設設定表示您的 DPS 會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![IoT DPS 預設 IP 篩選器設定](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

若要新增 IP 篩選規則，請選取 [ **+ 新增 Ip 篩選規則**]。

![將 IP 篩選規則新增至 IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

選取 [**新增 IP 篩選規則**] 之後，請填寫欄位。

![選取 [新增 IP 篩選規則] 之後](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 篩選規則的**名稱**。 這必須是唯一、不區分大小寫的英數位元字串，最長可達128個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

* 選取 [**允許**] 或 [**封鎖**] 做為 IP 篩選規則的**動作**。

填寫欄位之後，請選取 [**儲存**] 以儲存規則。 您會看到警示，通知您正在進行更新。

![有關儲存 IP 篩選器規則的通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

當您達到 10 個 IP 篩選器規則的上限後，[新增] 選項便會停用。

若要編輯現有的規則，請選取您要變更的資料，進行變更，然後選取 [**儲存**] 以儲存編輯。

> [!NOTE]
> 拒絕 IP 位址可防止其他 Azure 服務與 DPS 實例進行互動。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選規則，請選取該資料列上的垃圾桶圖示，然後選取 [**儲存**]。 此規則會被移除，並儲存變更。

![刪除 IoT DPS IP 篩選規則](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>更新程式碼中的 IP 篩選規則

您可以使用 Azure 資源提供者的 REST 端點來抓取及修改 DPS IP 篩選器。 請參閱 `properties.ipFilterRules`createorupdate 方法[中的 ](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate)。

Azure CLI 或 Azure PowerShell 目前不支援更新 DPS IP 篩選器規則，但可透過 Azure Resource Manager 範本來完成。 如需使用 Resource Manager 範本的指引，請參閱[Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)。 接下來的範本範例會示範如何建立、編輯和刪除 DPS IP 篩選規則。

### <a name="add-an-ip-filter-rule"></a>新增 IP 篩選規則

下列範本範例會建立名為 "AllowAll" 的新 IP 篩選規則，以接受所有流量。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

根據您的需求更新範本的 IP 篩選規則屬性。

| 屬性                | 描述 |
| ------------------------ | ----------- |
| **FilterName**           | 提供 IP 篩選規則的名稱。 這必須是唯一、不區分大小寫的英數位元字串，最長可達128個字元。 僅限 ASCII 7 位英數位元加上 {'-'、'： '、'/'、'\'、'. '、' + '、'% '、' _ '、' # '、' * '、'？ '，'！ ' 已接受 ' （'、'） '、'、'、' = '、' @ '、'; '、' ' '}。 |
| **動作**               | 接受的值為 [**接受**] 或 [ **拒絕**] 作為 IP 篩選規則的動作。 |
| **ipMask**               | 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。 |


### <a name="update-an-ip-filter-rule"></a>更新 IP 篩選規則

下列範本範例會更新名為 "AllowAll" 的 IP 篩選規則，如先前所示，以拒絕所有流量。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

下列範本範例會刪除 DPS 實例的所有 IP 篩選規則。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，第一個符合 IP 位址的規則會決定接受或拒絕動作。

例如，如果您想要接受 192.168.100.0/22 範圍中的位址，並拒絕其他所有位址，則方格中的第一個規則應接受位址範圍 192.168.100.0/22。 下一個規則應使用 0.0.0.0/0 範圍拒絕所有位址。

按一下資料列前端呈垂直方向的三個點並使用拖放功能，即可變更方格中的 IP 篩選器規則順序。

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]。

![變更 DPS IP 篩選規則的順序](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>後續步驟

若要進一步探索管理 DPS，請參閱：

* [瞭解 IoT DPS IP 位址](iot-dps-understand-ip-address.md)
* [使用 Azure CLI 設定 DPS](how-to-manage-dps-with-cli.md)
* [控制 DPS 的存取權](how-to-control-access.md)
