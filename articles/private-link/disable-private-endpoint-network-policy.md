---
title: 禁用 Azure 中專用終結點的網路原則
description: 瞭解如何禁用專用終結點的網路原則。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453026"
---
# <a name="disable-network-policies-for-private-endpoints"></a>禁用專用終結點的網路原則

專用終結點不支援網路原則（如網路安全性群組 （NSG）。 為了在給定子網上部署專用終結點，該子網上需要顯式禁用設置。 此設置僅適用于專用終結點。 對於子網中的其他資源，根據網路安全性群組 （NSG） 安全規則定義控制訪問。 
 
當使用門戶創建專用終結點時，此設置將自動禁用，作為創建過程的一部分。 使用其他用戶端進行部署需要執行其他步驟來更改此設置。 您可以使用 Azure 門戶中的雲外殼禁用設置，或者 Azure PowerShell、Azure CLI 的本地安裝或使用 Azure 資源管理器範本。  
 
以下示例`PrivateEndpointNetworkPolicies`描述如何禁用名為*myVirtualNetwork 的虛擬網路*，該虛擬網路在名為*myResourceGroup*的資源組中託管了*預設*子網。

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本節介紹如何使用 Azure PowerShell 禁用子網專用終結點策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本節介紹如何使用 Azure CLI 禁用子網專用終結點策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>使用範本
本節介紹如何使用 Azure 資源管理器範本禁用子網專用終結點策略。
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>後續步驟
- 瞭解有關[Azure 專用終結點](private-endpoint-overview.md)的更多
 
