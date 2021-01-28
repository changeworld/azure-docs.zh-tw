---
title: Azure IoT DPS IP 連線篩選器 | Microsoft Docs
description: 如何使用 IP 篩選來封鎖從特定 IP 位址至 Azure IoT DPS 執行個體的連線。 您可以封鎖來自個別 IP 位址或 IP 位址範圍的連接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934507"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>使用 Azure IoT DPS IP 連線篩選器

安全性是任何 IoT 解決方案的一個重要課題。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 Azure IoT 中樞裝置佈建服務 (DPS) 的「IP 篩選器」功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

有兩個特定使用案例適合封鎖來自特定 IP 位址的 DPS 端點連線︰

* 您的 DPS 只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量。 例如，您使用 DPS 搭配 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) 來建立 DPS 與您裝置之間的私人連線。

* 您需要拒絕 DPS 系統管理員認為可疑的 IP 位址所傳來的流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 DPS 執行個體層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。

嘗試建立連線的 IP 位址若符合 DPS 執行個體內的拒絕 IP 規則，將會收到未授權 401 狀態碼和描述。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

根據預設，DPS 入口網站中的 **IP 篩選器** 方格是空的。 這個預設設定表示您的 DPS 會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![IoT DPS 預設 IP 篩選器設定](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

若要新增 IP 篩選器規則，請選取 [+ 新增 IP 篩選器規則]。

![新增 IP 篩選器規則到 IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

選取 [新增 IP 篩選器規則] 之後，請填寫欄位。

![選取 [新增 IP 篩選器規則] 之後](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 篩選器規則的 **名稱**。 名稱必須是唯一的、不區分大小寫的英數字元字串，長度上限為 128 個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

* 選取 [允許] 或 [封鎖] 作為 IP 篩選器規則的 **動作**。

填寫欄位之後，請選取 [儲存] 以儲存規則。 您會看到通知您正在進行更新的警示。

![有關儲存 IP 篩選器規則的通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

當您達到 100 IP 篩選規則的最大值時，就會停用 [ **新增** ] 選項。

若要編輯現有的規則，請選取您要變更的資料，進行變更，然後選取 [儲存] 以儲存您的編輯。

> [!NOTE]
> 拒絕 IP 位址可防止其他 Azure 服務與 DPS 執行個體互動。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選器規則，請選取該資料列上的垃圾桶圖示，然後選取 [儲存]。 此規則會被移除，並儲存變更。

![刪除 IoT DPS IP 篩選器規則](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>更新程式碼中的 IP 篩選器規則

您可以使用 Azure 資源提供者的 REST 端點，擷取和修改 DPS IP 篩選條件。 請參閱 [createorupdate 方法](/rest/api/iot-dps/iotdpsresource/createorupdate)中的 `properties.ipFilterRules`。

Azure CLI 或 Azure PowerShell 目前不支援更新 DPS IP 篩選器規則，但可透過 Azure Resource Manager 範本來完成。 如需使用 Resource Manager 範本的指引，請參閱 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)。 接下來的範本範例會示範如何建立、編輯和刪除 DPS IP 篩選器規則。

### <a name="add-an-ip-filter-rule"></a>新增 IP 篩選器規則

下列範本範例會建立名為 "AllowAll" 的新 IP 篩選器規則，以接受所有流量。

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

根據您的需求更新範本的 IP 篩選器規則屬性。

| 屬性                | 描述 |
| ------------------------ | ----------- |
| **FilterName**           | 提供 IP 篩選器規則的名稱。 名稱必須是唯一的、不區分大小寫的英數字元字串，長度上限為 128 個字元。 只接受 ASCII 7 位英數位元和  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   。 |
| **動作**               | 接受的值為 [接受]  或 [拒絕] ****   以作為 IP 篩選器規則的動作。 |
| **ipMask**               | 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。 |


### <a name="update-an-ip-filter-rule"></a>更新 IP 篩選器規則

下列範本範例會更新名為 "AllowAll" 的 IP 篩選器規則 (如先前所示)，以拒絕所有流量。

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

下列範本範例會刪除 DPS 執行個體的所有 IP 篩選器規則。

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

![變更 DPS IP 篩選器規則的順序](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>後續步驟

若要進一步探索管理 DPS，請參閱：

* [了解 IoT DPS IP 位址](iot-dps-understand-ip-address.md)
* [使用 Azure CLI 設定 DPS](quick-setup-auto-provision-cli.md)
* [DPS 存取控制](how-to-control-access.md)
