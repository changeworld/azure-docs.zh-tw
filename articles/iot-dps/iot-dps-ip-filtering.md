---
title: Azure IoT DPS IP 連接篩選器 |微軟文檔
description: 如何使用 IP 篩選來阻止從特定 IP 位址連接到 Azure IoT DPS 實例。 您可以封鎖來自個別 IP 位址或 IP 位址範圍的連接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284911"
---
# <a name="use-ip-filters"></a>使用 IP 篩選器

安全性是任何 IoT 解決方案的一個重要方面。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 Azure IoT 中心設備佈建服務 （DPS） 的*IP 篩選器*功能使您能夠配置用於拒絕或接受來自特定 IPv4 位址的流量的規則。

## <a name="when-to-use"></a>使用時機

有兩種特定的用例，其中從某些 IP 位址阻止與 DPS 終結點的連接非常有用：

* 您的 DPS 應僅從指定的 IP 位址範圍接收流量，並拒絕其他所有內容。 例如，將 DPS 與[Azure 快速路由](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services)一起使用，以在 DPS 和設備之間創建專用連線。

* 您需要拒絕來自由 DPS 管理員標識為可疑的 IP 位址的流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則在 DPS 實例級別應用。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。

與 DPS 實例中的拒絕 IP 規則匹配的 IP 位址的任何連接嘗試都會收到未經授權的 401 狀態碼和說明。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

預設情況下，DPS 門戶中的**IP 篩選器**網格為空。 此設置表示 DPS 接受來自任何 IP 位址的連接。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![IoT DPS 預設 IP 篩選器設置](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

要添加 IP 篩選器規則，請選擇 **" 添加 IP 篩選器規則**"。

![將 IP 篩選器規則添加到 IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

選擇 **"添加 IP 篩選器規則**"後，請填寫欄位。

![選擇添加 IP 篩選器規則後](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 篩選器規則**的名稱**。 這必須是一個唯一的、不區分大小寫的字母數位字串，長達 128 個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

* 選擇 **"允許**"或 **"阻止**"作為 IP 篩選器規則**的操作**。

填寫欄位後，選擇 **"保存**"以保存規則。 您將看到一個警報，通知您更新正在進行。

![有關儲存 IP 篩選器規則的通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

當您達到 10 個 IP 篩選器規則的上限後，[新增]**** 選項便會停用。

要編輯現有規則，請選擇要更改的資料，進行更改，然後選擇 **"保存"** 以保存編輯。

> [!NOTE]
> 拒絕 IP 位址可能會阻止其他 Azure 服務與 DPS 實例交互。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

要刪除 IP 篩選器規則，請選擇該行上的垃圾罐圖示，然後選擇"**保存**"。 將刪除規則並保存更改。

![刪除 IoT DPS IP 篩選器規則](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>更新代碼中的 IP 篩選器規則

您可以使用 Azure 資來源提供者的 REST 終結點檢索和修改 DPS IP 篩選器。 請參閱 [createorupdate 方法](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate)中的 `properties.ipFilterRules`。

Azure CLI 或 Azure PowerShell 目前不支援更新 DPS IP 篩選器規則，但可以通過 Azure 資源管理器範本完成。 有關使用資源管理器範本的指導，請參閱[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)。 以下範本示例演示如何創建、編輯和刪除 DPS IP 篩選器規則。

### <a name="add-an-ip-filter-rule"></a>添加 IP 篩選器規則

下面的範本示例創建一個名為"AllowAll"的新 IP 篩選器規則，該規則接受所有流量。

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

根據您的要求更新範本的 IP 篩選器規則屬性。

| 屬性                | 描述 |
| ------------------------ | ----------- |
| **篩檢程式名稱**           | 提供 IP 篩選器規則的名稱。 這必須是一個唯一的、不區分大小寫的字母數位字串，長達 128 個字元。 只有 ASCII 7 位字母數位字元加上\'{'"，"""/"，"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" |
| **動作**               | 接受的值是 **"接受** "或 **"拒絕** "作為 IP 篩選器規則的操作。 |
| **ipMask**               | 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。 |


### <a name="update-an-ip-filter-rule"></a>更新 IP 篩選器規則

下面的範本示例更新前面顯示的名為"AllowAll"的 IP 篩選器規則，以拒絕所有流量。

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

以下範本示例刪除 DPS 實例的所有 IP 篩選器規則。

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

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]****。

![更改 DPS IP 篩選器規則的順序](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>後續步驟

要進一步探索管理 DPS，請參閱：

* [瞭解 IoT DPS IP 位址](iot-dps-understand-ip-address.md)
* [使用 Azure CLI 配置 DPS](how-to-manage-dps-with-cli.md)
* [控制對 DPS 的訪問](how-to-control-access.md)
