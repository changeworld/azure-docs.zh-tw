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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737490"
---
# <a name="manage-a-private-endpoint-connection"></a>管理私人端點連線
Azure 私用連結適用于核准呼叫流程模型，其中私用連結服務取用者可以要求與服務提供者的連線，以取用服務。 服務提供者接著可以決定是否允許取用者連接。 Azure 私用連結可讓服務提供者管理其資源上的私用端點連線。 本文提供如何管理私人端點連接的指示。

![管理私人端點](media/manage-private-endpoint/manage-private-endpoint.png)

私用連結服務取用者有兩種連接核准方法可供選擇：
- **自動**：如果服務取用者具有服務提供者資源的 RBAC 許可權，取用者就可以選擇自動核准方法。 在此情況下，當要求到達服務提供者資源時，服務提供者不需要採取任何動作，且會自動核准連線。 
- **手動**：相反地，如果服務取用者沒有服務提供者資源的 RBAC 許可權，取用者可以選擇手動核准方法。 在此情況下，連線要求會在服務資源上顯示為 [**擱置**中]。 服務提供者必須手動核准要求，才能建立連接。 在手動案例中，服務取用者也可以指定包含要求的訊息，以提供更多的內容給服務提供者。 服務提供者具有下列選項，可供選擇所有私人端點連線的： [**已核准**]、[**拒絕**]、[**移除**]。

下表顯示各種服務提供者動作和私人端點的產生連接狀態。  服務提供者也可以在稍後變更私人端點連線的線上狀態，而不需要取用者介入。 動作會更新取用者端上端點的狀態。 


|服務提供者動作   |服務取用者私人端點狀態   |描述   |
|---------|---------|---------|
|None    |    Pending     |    手動建立連線，並擱置私人連結資源擁有者核准。       |
|核准    |  已核准       |  已自動或手動核准連線並可供使用。     |
|拒絕     | 已拒絕        | 私人連結資源擁有者已拒絕連線。        |
|移除    |  已中斷連接       | 私人連結資源擁有者已移除連線，私用端點會變成資訊性，而且應該刪除以進行清除。        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>管理 Azure PaaS 資源上的私人端點連線
入口網站是管理 Azure PaaS 資源上的私用端點連線慣用的方法。 目前，我們沒有 PowerShell/CLI 支援來管理 Azure PaaS 資源上的連線。
1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 流覽至 [私人連結中心]。
3. 在 [**資源**] 下，選取您想要管理私人端點連線的資源類型。
4. 針對您的每個資源類型，您可以查看與它相關聯的私人端點連接數目。 您可以視需要篩選資源。
5. 選取 [私人端點連接]。  在列出的連線底下，選取您要管理的連線。 
6. 您可以從頂端的選項中選取，以變更連接的狀態。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>在客戶/合作夥伴擁有的私人連結服務上管理私人端點連線

Azure PowerShell 和 Azure CLI 是在 Microsoft 合作夥伴服務或客戶擁有的服務上管理私人端點連線的慣用方法。 目前，我們沒有任何入口網站支援，無法在私人連結服務上管理連接。  
 
### <a name="powershell"></a>PowerShell 
  
使用下列 PowerShell 命令來管理私人端點連線。  
#### <a name="get-private-link-connection-states"></a>取得私人連結連接狀態 
使用 `Get-AzPrivateLinkService` Cmdlet 來取得私人端點連線及其狀態。  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連接 
 
使用 `Approve-AzPrivateEndpointConnection` Cmdlet 來核准私用端點連接。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>拒絕私人端點連接 
 
使用 `Deny-AzPrivateEndpointConnection` Cmdlet 來拒絕私人端點連接。 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>移除私人端點連接 
 
使用 `Remove-AzPrivateEndpointConnection` Cmdlet 來移除私人端點連接。 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
用於 `az network private-link-service update` 管理您的私用端點連接。 連接狀態會在參數中指定 ```azurecli connection-status``` 。 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>後續步驟
- [深入了解私人端點](private-endpoint-overview.md)
 
