---
title: 還原已刪除的應用程式
description: 了解如何在 Azure App Service 中還原已刪除的應用程式。 避免意外刪除應用程式帶來的麻煩。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 20c220bcb44a1a47e308f57d1466aee2773111a4
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985677"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 還原已刪除的 App Service 應用程式

如果您意外刪除了 Azure App Service 中的應用程式，您可以使用 [Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令進行還原。

> [!NOTE]
> - 已刪除的應用程式會在初始刪除之後的 30 天內從系統中清除。 應用程式清除之後，就無法復原。
> - 取用量方案不支援取消刪除功能。
> - 在 App Service 環境中執行的應用程式服務應用程式不支援快照集。 因此，在 App Service 環境中執行的 App Service 應用程式不支援取消刪除功能和複製功能。
>

## <a name="re-register-app-service-resource-provider"></a>重新註冊 App Service 資源提供者

某些客戶可能會遇到無法擷取已刪除應用程式清單的問題。 若要解決此問題，請執行下列命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已刪除的應用程式

若要取得已刪除應用程式的集合，您可以使用 `Get-AzDeletedWebApp`。

如需您可以使用之特定已刪除應用程式的詳細資料：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

詳細資訊包括：

- **DeletedSiteId**：應用程式的唯一識別碼，用於已刪除多個同名應用程式的案例
- **SubscriptionID**：包含已刪除資源的訂用帳戶
- **位置**：原始應用程式的位置
- **ResourceGroupName**：原始資源群組的名稱
- **Name**：原始應用程式的名稱。
- **Slot**：位置的名稱。
- **Deletion Time**：刪除應用程式的時間  

## <a name="restore-deleted-app"></a>還原已刪除的應用程式

>[!NOTE]
> 函數應用程式不支援 `Restore-AzDeletedWebApp`。

一旦識別出您想要還原的應用程式之後，您就可以使用 `Restore-AzDeletedWebApp` 進行還原。

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 部署位置不會還原為應用程式的一部分。 如果您需要還原預備位置，請使用旗標 `-Slot <slot-name>` 。
>

命令的輸入如下：

- **目標資源群組**：將會還原應用程式的目標資源群組
- **Name**：應用程式的名稱，必須是全域唯一的。
- **TargetAppServicePlanName**：連結至應用程式的 App Service 方案

根據預設， `Restore-AzDeletedWebApp` 會同時還原您的應用程式設定和任何內容。 如果您只想還原內容，請使用 `-RestoreContentOnly` 旗標搭配這個 commandlet。

> [!NOTE]
> 如果應用程式裝載在上，然後從 App Service 環境中刪除，則只有在對應的 App Service 環境仍然存在時，才可以還原。
>

您可以在這裡找到完整的 commandlet 參考：[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp) \(英文\)。
