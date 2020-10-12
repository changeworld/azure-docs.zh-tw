---
title: 在 Azure 中管理私人端點連線
description: 瞭解如何在 Azure 中管理私人端點連線
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 20695d183ea8184f7ee2948b3897fa1f3a741411
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737490"
---
# <a name="manage-a-private-endpoint-connection"></a>管理私人端點連線
Azure Private Link 適用于 Private Link 服務取用者可要求服務提供者連接以取用服務的核准呼叫流程模型。 接著，服務提供者可以決定是否允許取用者連接。 Azure Private Link 可讓服務提供者管理其資源上的私人端點連線。 本文提供如何管理私人端點連接的相關指示。

![管理私人端點](media/manage-private-endpoint/manage-private-endpoint.png)

Private Link 服務取用者可以選擇兩種連線核准方法：
- **自動**：如果服務取用者具有服務提供者資源的 RBAC 許可權，取用者可以選擇自動核准方法。 在此情況下，當要求到達服務提供者資源時，服務提供者不需要採取任何動作，而且會自動核准連接。 
- **手動**：相反地，如果服務取用者沒有服務提供者資源的 RBAC 許可權，取用者可以選擇手動核准方法。 在此情況下，連接要求會在服務資源上顯示為 **擱置**中。 服務提供者必須手動核准要求，才能建立連接。 在手動案例中，服務取用者也可以指定具有要求的訊息，以提供更多內容給服務提供者。 服務提供者有下列選項可供選擇所有私人端點連接： **已核准**、 **拒絕**、 **移除**。

下表顯示私人端點的各種服務提供者動作和產生的連接狀態。  服務提供者也可以在稍後變更私人端點連線的線上狀態，而不需取用消費者介入。 動作將會更新取用者端端點的狀態。 


|服務提供者動作   |服務取用者私人端點狀態   |描述   |
|---------|---------|---------|
|None    |    Pending     |    連接是以手動方式建立的，且會等待 Private Link 資源擁有者進行核准。       |
|核准    |  已核准       |  已自動或手動核准連線並可供使用。     |
|拒絕     | 已拒絕        | 私人連結資源擁有者已拒絕連線。        |
|移除    |  已中斷連接       | 私人連結資源擁有者已移除連線，私用端點會變成資訊，而且應該刪除以進行清除。        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>管理 Azure PaaS 資源上的私人端點連線
入口網站是在 Azure PaaS 資源上管理私人端點連線的慣用方法。 目前，我們沒有 PowerShell/CLI 支援來管理 Azure PaaS 資源上的連接。
1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 流覽至 Private Link Center。
3. 在 [ **資源**] 底下，選取您要管理私人端點連線的資源類型。
4. 您可以針對每個資源類型，查看與其相關聯的私人端點連接數目。 您可以視需要篩選資源。
5. 選取私人端點連接。  在列出的 [連線] 底下，選取您要管理的連接。 
6. 您可以從頂端的選項中選取，以變更連接的狀態。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>管理客戶/合作夥伴所擁有 Private Link 服務的私人端點連線

Azure PowerShell 和 Azure CLI 是在 Microsoft 合作夥伴服務或客戶擁有的服務上管理私人端點連接的慣用方法。 目前，我們沒有任何入口網站支援，無法管理 Private Link 服務上的連接。  
 
### <a name="powershell"></a>PowerShell 
  
使用下列 PowerShell 命令來管理私人端點連接。  
#### <a name="get-private-link-connection-states"></a>取得 Private Link 連接狀態 
使用 `Get-AzPrivateLinkService` Cmdlet 來取得私人端點連線和其狀態。  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連線 
 
使用 `Approve-AzPrivateEndpointConnection` Cmdlet 來核准私人端點連接。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>拒絕私人端點連線 
 
使用 `Deny-AzPrivateEndpointConnection` Cmdlet 來拒絕私人端點連接。 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>移除私人端點連線 
 
使用 `Remove-AzPrivateEndpointConnection` Cmdlet 來移除私人端點連接。 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
用於 `az network private-link-service update` 管理私人端點連接。 連接狀態是在參數中指定 ```azurecli connection-status``` 。 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>後續步驟
- [深入了解私人端點](private-endpoint-overview.md)
 
