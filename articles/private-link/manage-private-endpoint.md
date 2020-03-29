---
title: 在 Azure 中管理專用終結點連接
description: 瞭解如何在 Azure 中管理專用終結點連接
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452970"
---
# <a name="manage-a-private-endpoint-connection"></a>管理私人端點連線
Azure 專用連結適用于審批調用流模型，其中專用連結服務消費者可以請求連接到服務提供者以使用該服務。 然後，服務提供者可以決定是否允許消費者連接。 Azure 專用連結使服務提供者能夠管理其資源上的專用終結點連接。 本文提供有關如何管理專用終結點連接的說明。

![管理專用終結點](media/manage-private-endpoint/manage-private-endpoint.png)

專用連結服務消費者可以選擇兩種連接審批方法：
- **自動**：如果服務消費者對服務提供者資源具有 RBAC 許可權，則消費者可以選擇自動核准方法。 在這種情況下，當請求到達服務提供者資源時，不需要服務提供者執行任何操作，並且連接將自動獲得批准。 
- **手冊**：相反，如果服務消費者對服務提供者資源沒有 RBAC 許可權，消費者可以選擇手動審批方法。 在這種情況下，連接請求在服務資源上顯示為**掛起**。 在建立連接之前，服務提供者必須手動批准請求。 在手動情況下，服務消費者還可以指定一條消息，請求向服務提供者提供更多上下文。 服務提供者有以下選項可供選擇的所有專用終結點連接：**已批准**、**拒絕**、**刪除**。

下表顯示了專用終結點的各種服務提供者操作和生成的連接狀態。  服務提供者還可以在以後更改專用終結點連接的連接狀態，而無需消費者干預。 該操作將更新消費者端端的狀態。 


|服務提供者操作   |服務消費者專用終結點狀態   |描述   |
|---------|---------|---------|
|None    |    Pending     |    連接是手動創建的，正在等待專用連結資源擁有者的批准。       |
|核准    |  已核准       |  已自動或手動核准連線並可供使用。     |
|拒絕     | 已拒絕        | 私人連結資源擁有者已拒絕連線。        |
|移除    |  已中斷連接       | 連接被專用連結資源擁有者刪除，專用終結點變得資訊豐富，應刪除以進行清理。        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>管理 Azure PaaS 資源上的私人端點連線
門戶是管理 Azure PaaS 資源上的專用終結點連接的首選方法。 目前，我們沒有 PowerShell/CLI 支援管理 Azure PaaS 資源上的連接。
1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 導航到專用連結中心。
3. 在 **"資源**"下，選擇要管理專用終結點連接的資源類型。
4. 對於每種資源類型，您可以查看與其關聯的專用終結點連接數。 您可以根據需要篩選資源。
5. 選擇專用終結點連接。  在列出的連接下，選擇要管理的連接。 
6. 您可以通過從頂部的選項中進行選擇來更改連接的狀態。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>在客戶/合作夥伴擁有的專用連結服務上管理專用終結點連接

Azure PowerShell 和 Azure CLI 是管理 Microsoft 合作夥伴服務或客戶擁有的服務上的專用終結點連接的首選方法。 目前，我們沒有任何門戶支援來管理專用連結服務上的連接。  
 
### <a name="powershell"></a>PowerShell 
  
使用以下 PowerShell 命令管理專用終結點連接。  
#### <a name="get-private-link-connection-states"></a>獲取專用鏈路連接狀態 
使用`Get-AzPrivateLinkService`Cmdlet 獲取專用終結點連接及其狀態。  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>批准專用終結點連接 
 
使用`Approve-AzPrivateEndpointConnection`Cmdlet 批准專用終結點連接。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>拒絕專用終結點連接 
 
使用`Deny-AzPrivateEndpointConnection`Cmdlet 拒絕專用終結點連接。 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>刪除專用終結點連接 
 
使用`Remove-AzPrivateEndpointConnection`Cmdlet 刪除專用終結點連接。 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
用於`az network private-link-service update`管理專用終結點連接。 連接狀態在參數中```azurecli connection-status```指定。 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>後續步驟
- [瞭解專用終結點](private-endpoint-overview.md)
 
