---
title: 分析網路安全性-安全性群組視圖-Azure REST API
titleSuffix: Azure Network Watcher
description: 本文將說明 Azure REST API 如何使用安全性群組視圖來分析虛擬機器的安全性。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 156da7504a1a5e2a704e52a783fcd3e437a59867
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986262"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>使用 REST API，利用安全性群組檢視分析虛擬機器的安全性

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> 安全性群組 View API 已不再維護，即將淘汰。 請使用可提供相同功能的 [有效安全性規則功能](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) 。 

安全性群組檢視會傳回套用至虛擬機器之已設定且有效的網路安全性規則。 這項功能可用來稽核及診斷 VM 所設定的網路安全性群組和規則，以確保會正確允許或拒絕流量。 在本文中，我們會說明如何使用 REST API 來擷取有效且已套用至虛擬機器的安全性規則


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始之前

在此案例中，您可以呼叫網路監看員 REST API 來取得的虛擬機器安全性群組檢視。 使用 ARMclient 透過 PowerShell 呼叫 REST API。 ARMClient 可在 chocolatey 的 [ARMClient 上找到 chocolatey](https://chocolatey.org/packages/ARMClient)

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>狀況

本文涵蓋的案例會擷取有效且已套用指定虛擬機器的安全性規則。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登入

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>擷取虛擬機器

執行下列程式碼以傳回虛擬機器。下列程式碼需要變數︰

- **subscriptionId** -訂用帳戶識別碼也可以使用 >select-azsubscription Cmdlet 來 **取得** 。
- **resourceGroupName** - 包含虛擬機器的資源群組名稱。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

所需的資訊是回應中 `Microsoft.Compute/virtualMachines`類型下方的**識別碼**，如下列範例所示︰

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>取得虛擬機器的安全性群組檢視

下列範例會要求目標虛擬機器的安全性群組檢視。 本範例的結果可用於比較依原始所定義的規則及安全性來尋找設定漂移。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>檢視回應

下列範例是前述命令中所傳回的回應。 結果顯示虛擬機器上所有有效且套用的安全性規則，並細分為 **NetworkInterfaceSecurityRules**、**DefaultSecurityRules**和 **EffectiveSecurityRules** 群組。

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

請瀏覽[使用網路監看員稽核網路安全性群組 (NSG)](network-watcher-security-group-view-powershell.md) 以了解如何自動驗證網路安全性群組。


