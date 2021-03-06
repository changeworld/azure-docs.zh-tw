---
title: '停用 Azure Private Link 服務來源 IP 位址的網路原則 '
description: 瞭解如何停用 Azure private Link 的網路原則
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1062f126da8be6b37f6b52eee520425b3edcde16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744335"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>停用 Private Link 服務來源 IP 的網路原則

為了選擇 Private Link 服務的來源 IP 位址，子網上需要明確停用設定 `privateLinkServiceNetworkPolicies` 。 這項設定僅適用于您選擇做為 Private Link 服務來源 IP 的特定私人 IP 位址。 如果是子網中的其他資源，則會根據 (NSG) 安全性規則定義的網路安全性群組來控制存取權。 
 
使用任何 Azure 用戶端 (PowerShell、CLI 或範本) 時，需要額外的步驟才能變更此屬性。 您可以使用 Azure 入口網站的 cloud shell 或 Azure PowerShell、Azure CLI 的本機安裝，或使用 Azure Resource Manager 範本來停用原則。  
 
依照下列步驟，針對名為*myVirtualNetwork*的虛擬網路，停用在名為*myResourceGroup*的資源群組中裝載的*預設*子網的私人連結服務網路原則。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本節說明如何使用 Azure PowerShell 停用子網私人端點原則。
在程式碼中，以虛擬子網的名稱取代 "default"。

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本節說明如何使用 Azure CLI 停用子網私人端點原則。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>使用範本
本節說明如何使用 Azure Resource Manager 範本停用子網私人端點原則。
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
## <a name="next-steps"></a>接下來的步驟
- 深入瞭解 [Azure 私人端點](private-endpoint-overview.md)
 
