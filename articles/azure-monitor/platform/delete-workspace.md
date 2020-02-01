---
title: 刪除並復原 Azure Log Analytics 工作區 |Microsoft Docs
description: 了解如何刪除您的 Log Analytics 工作區 (如果您已在個人訂用帳戶中建立工作區) 或重組您的工作區模型。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 038cfe04193b734bd26ed0ffd4dec5ae9b267c22
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901267"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>刪除和還原 Azure Log Analytics 工作區

本文說明 Azure Log Analytics 工作區虛刪除的概念，以及如何復原已刪除的工作區。 

## <a name="considerations-when-deleting-a-workspace"></a>刪除工作區時的考慮

當您刪除 Log Analytics 工作區時，會執行虛刪除作業，以允許在14天內復原工作區（包括其資料和連線的代理程式），不論是意外或刻意刪除。 在虛刪除期間之後，工作區資源和其資料無法復原–其資料會排入佇列以供永久刪除，並在30天內完全清除。 工作區名稱是「已發行」，您可以用它來建立新的工作區。

> [!NOTE]
> 如果您想要覆寫虛刪除行為並永久刪除工作區，請遵循[永久工作區刪除](#permanent-workspace-delete)中的步驟。

當您刪除工作區時，想要特別小心，因為可能有重要的資料和設定可能會對您的服務作業造成負面影響。 審查哪些代理程式、解決方案和其他 Azure 服務，以及將其資料儲存在 Log Analytics 中的來源，例如：

* 管理解決方案
* Azure 自動化
* 在 Windows 與 Linux 虛擬機器上執行的代理程式
* 在您環境中的 Windows 與 Linux 電腦上執行的代理程式
* System Center Operations Manager

虛刪除作業會刪除工作區資源，且任何相關聯的使用者許可權都會中斷。 如果使用者與其他工作區相關聯，則他們可以繼續搭配其他工作區使用 Log Analytics。

## <a name="soft-delete-behavior"></a>虛刪除行為

工作區刪除作業會移除工作區 Resource Manager 資源，但其設定和資料會保留14天，同時提供刪除工作區的外觀。 設定為向工作區報告的任何代理程式和 System Center Operations Manager 管理群組，在虛刪除期間都會維持在孤立狀態。 服務會進一步提供一種機制來復原已刪除的工作區，包括其資料和連線的資源，基本上就是將刪除作業還原。

> [!NOTE] 
> 已安裝的解決方案和連結的服務（例如您的 Azure 自動化帳戶）會在刪除時從工作區中永久移除，而且無法復原。 這些應該在復原作業之後重新設定，讓工作區進入先前設定的狀態。

您可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、 [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)或[Azure 入口網站](https://portal.azure.com)來刪除工作區。

### <a name="azure-portal"></a>Azure Portal

1. 若要登入，請移至[Azure 入口網站](https://portal.azure.com)。 
2. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。
3. 在 Log Analytics 工作區清單中，選取工作區，然後按一下中間窗格頂端的 [**刪除**]。
   ![來自工作區屬性窗格的刪除選項](media/delete-workspace/log-analytics-delete-workspace.png)
4. 當要求您確認刪除工作區的確認訊息窗格出現時，按一下 [是]。
   ![確認刪除工作區](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>永久刪除工作區
在某些情況下，虛刪除方法可能無法納入，例如開發和測試，您需要使用相同的設定和工作區名稱來重複部署。 在這種情況下，您可以永久刪除您的工作區，並「覆寫」虛刪除期間。 永久的工作區刪除作業會釋放工作場所名稱，您可以使用相同的名稱建立新的工作區。


> [!IMPORTANT]
> 請小心使用永久的工作區刪除作業，因為它無法復原，而且您將無法復原您的工作區和其資料。

您目前可以透過 REST API 來執行永久的工作區刪除。

> [!NOTE]
> 任何 API 要求都必須在要求標頭中包含持有人授權權杖。
>
> 您可以使用下列內容取得權杖：
> - [應用程式註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - 使用瀏覽器中的開發人員主控台（F12），流覽至 Azure 入口網站。 在 [**要求標頭**] 底下的驗證字串中，查看其中一個**batch？** 實例。 這會在模式授權中 *：持有人 <token>* 。 複製此程式，並將其新增至您的 API 呼叫，如範例中所示。
> - 流覽至 Azure REST 檔網站。 在任何 API 上按下 [**試用**]，複製持有人權杖，並將它新增至您的 api 呼叫。
若要永久刪除您的工作區，請使用[工作區-刪除 REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) API 呼叫搭配 force 標記：
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
其中 ' eyJ0eXAiOiJKV1Qi ... ' 表示完整授權 token。

## <a name="recover-workspace"></a>復原工作區

如果您對訂用帳戶和資源群組具有在虛刪除作業之前相關聯的參與者許可權，您可以在其虛刪除期間進行復原，包括其資料、設定和連接的代理程式。 在虛刪除期間之後，工作區無法復原，而且會被指派永久刪除。 已刪除的工作區名稱會在虛刪除期間保留，而且在嘗試建立新的工作區時無法使用。  

您可以使用下列工作區建立方法來重新建立工作區： [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)只要以已刪除的工作區詳細資料填入下列屬性即可：

* 訂用帳戶 ID
* 資源群組名稱
* 工作區名稱
* 地區

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

工作區及其所有資料都會在復原作業之後恢復運作。 解決方案和已連結的服務在刪除時已從工作區中永久移除，而且應該重新設定，以將工作區帶入其先前設定的狀態。 在工作區復原後，某些資料可能無法供查詢，直到重新安裝相關聯的解決方案，並將其架構新增至工作區為止。

> [!NOTE]
> * [Azure 入口網站](https://portal.azure.com)不支援工作區復原。 
> * 在虛刪除期間重新建立工作區，表示此工作區名稱已在使用中。 
> 
