---
title: 還原已刪除的應用程式
description: 瞭解如何在 Azure App Service 中還原已刪除的應用程式。 避免不小心刪除應用程式的麻煩。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272486"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 還原已刪除的 App Service 應用程式

如果您不小心在 Azure App Service 中刪除您的應用程式，您可以使用[Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令來還原它。

> [!NOTE]
> 已刪除的應用程式會在初始刪除之後的30天內從系統中清除。 一旦應用程式已清除，就無法復原。
>

## <a name="re-register-app-service-resource-provider"></a>重新註冊 App Service 資源提供者
某些客戶可能會遇到無法執行已刪除之應用程式清單的問題。 若要解決此問題，請執行下列命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已刪除的應用程式

若要取得已刪除之應用程式的集合， `Get-AzDeletedWebApp`您可以使用。

如需您可以使用之特定已刪除應用程式的詳細資訊：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

詳細資訊包括：

- **DeletedSiteId**：應用程式的唯一識別碼，用於已刪除多個同名應用程式的案例
- **SubscriptionID**：包含已刪除資源的訂用帳戶
- **位置**：原始應用程式的位置
- **ResourceGroupName**：原始資源群組的名稱
- **名稱**：原始應用程式的名稱。
- 位置 **：位置**的名稱。
- **刪除時間**：應用程式何時刪除  

## <a name="restore-deleted-app"></a>還原已刪除的應用程式

一旦識別出您想要還原的應用程式之後，您就可以使用`Restore-AzDeletedWebApp`來還原它。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 部署位置不會還原為應用程式的一部分。 如果您需要還原預備位置， `-Slot <slot-name>`請使用旗標。
>

命令的輸入如下：

- **資源群組**：將用來還原應用程式的目標資源群組
- **名稱**：應用程式的名稱，必須是全域唯一的。
- **TargetAppServicePlanName**：連結至應用程式的 App Service 方案

根據預設`Restore-AzDeletedWebApp` ，會同時還原您的應用程式設定和內容。 如果您只想要還原內容，您可以使用`-RestoreContentOnly`旗標搭配此 commandlet。

> [!NOTE]
> 如果應用程式裝載在上，然後從 App Service 環境中刪除，則只有在對應的 App Service 環境仍然存在時，才可以還原。
>

您可以在這裡找到完整的 commandlet 參考： [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
