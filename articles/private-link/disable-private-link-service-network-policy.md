---
title: '禁用 Azure 專用連結服務源 IP 位址的網路原則 '
description: 瞭解如何禁用 Azure 專用連結的網路原則
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452999"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>禁用專用鏈路服務源 IP 的網路原則

為了選擇專用連結服務的源 IP 位址，子網上需要顯式禁用設置`privateLinkServiceNetworkPolicies`。 此設置僅適用于您選擇的特定私人 IP 位址，作為專用連結服務的源 IP。 對於子網中的其他資源，根據網路安全性群組 （NSG） 安全規則定義控制訪問。 
 
使用任何 Azure 用戶端（PowerShell、CLI 或範本）時，需要執行其他步驟來更改此屬性。 可以使用 Azure 門戶中的雲外殼禁用策略，或者 Azure PowerShell、Azure CLI 的本地安裝或使用 Azure 資源管理器範本。  
 
按照以下步驟禁用名為*myVirtualNetwork*的虛擬網路的專用連結服務網路原則，該虛擬網路*預設子網*託管在名為*myResourceGroup*的資源組中。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本節介紹如何使用 Azure PowerShell 禁用子網專用終結點策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本節介紹如何使用 Azure CLI 禁用子網專用終結點策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>後續步驟
- 瞭解有關[Azure 專用終結點](private-endpoint-overview.md)的更多
 
