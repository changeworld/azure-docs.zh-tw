---
title: 刪除和恢復 Azure 日誌分析工作區 |微軟文檔
description: 了解如何刪除您的 Log Analytics 工作區 (如果您已在個人訂用帳戶中建立工作區) 或重組您的工作區模型。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: ead0ac04fbd2244fce97dd043ebd44f24fb0f67f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054927"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>刪除和恢復 Azure 日誌分析工作區

本文介紹了 Azure 日誌分析工作區虛刪除的概念以及如何恢復已刪除的工作區。 

## <a name="considerations-when-deleting-a-workspace"></a>刪除工作區時的注意事項

刪除 Log Analytics 工作區時，將執行虛刪除操作，以便在 14 天內恢復工作區（包括其資料和連接的代理），無論刪除是意外的還是有意的。 在虛刪除期之後，工作區資源及其資料不可恢復 - 其資料將排隊等待永久刪除，並在 30 天內完全清除。 工作區名稱是"釋放"的，您可以使用它創建新工作區。

> [!NOTE]
> 如果要重寫虛刪除行為並永久刪除工作區，請按照[永久工作區刪除](#permanent-workspace-delete)中的步驟操作。

刪除工作區時，需要謹慎，因為可能存在可能對服務操作產生負面影響的重要資料和配置。 查看哪些代理、解決方案和其他 Azure 服務和源在日誌分析中存儲其資料，例如：

* 管理解決方案
* Azure 自動化
* 在 Windows 與 Linux 虛擬機器上執行的代理程式
* 在您環境中的 Windows 與 Linux 電腦上執行的代理程式
* System Center Operations Manager

虛刪除操作將刪除工作區資源，並且任何關聯的使用者的許可權都將斷開。 如果使用者與其他工作區關聯，則他們可以繼續將這些工作區使用日誌分析。

## <a name="soft-delete-behavior"></a>虛刪除行為

工作區刪除操作將刪除工作區資源管理器資源，但其配置和資料將保留 14 天，同時顯示工作區已被刪除。 配置為向工作區報告的任何代理和系統中心操作管理器管理組在虛刪除期間都處於孤立狀態。 該服務進一步提供了一種機制，用於恢復已刪除的工作區，包括其資料和連接的資源，基本上撤銷刪除。

> [!NOTE] 
> 已安裝的解決方案和連結服務（如 Azure 自動化帳戶）在刪除時將從工作區永久刪除，無法恢復。 應在恢復操作後重新配置這些工作區，以使工作區達到其以前配置的狀態。

可以使用[PowerShell、REST](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)或[Azure 門戶](https://portal.azure.com)中刪除工作區。

### <a name="azure-portal"></a>Azure 入口網站

1. 要登錄，請轉到[Azure 門戶](https://portal.azure.com)。 
2. 在 Azure 門戶中，選擇 **"所有服務**"。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]****。
3. 在日誌分析工作區清單中，選擇工作區，然後按一下中間窗格頂部的 **"刪除**"。
   ![來自工作區屬性窗格的刪除選項](media/delete-workspace/log-analytics-delete-workspace.png)
4. 當要求您確認刪除工作區的確認訊息窗格出現時，按一下 [是]****。
   ![確認刪除工作區](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>永久工作區刪除
虛刪除方法可能不適合某些方案，如開發和測試，其中需要使用相同的設置和工作區名稱重複部署。 在這種情況下，您可以永久刪除工作區並"覆蓋"虛刪除週期。 永久工作區刪除操作釋放工作區名稱，您可以使用同一名稱創建新工作區。


> [!IMPORTANT]
> 使用永久工作區刪除操作時要小心，因為它不可逆，並且您將無法恢復工作區及其資料。

永久工作區刪除當前可以通過 REST API 執行。

> [!NOTE]
> 任何 API 請求都必須在請求標頭中包含承載授權權杖。
>
> 您可以使用以下功能獲取權杖：
> - [應用程式註冊](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - 使用瀏覽器中的開發人員主控台 （F12） 導航到 Azure 門戶。 在請求標題 下查找身份驗證字串的**批次處理實例****之一**。 這將在模式*授權中：承載 。 <token> * 複製此調用並將其添加到 API 呼叫中，如示例所示。
> - 導航到 Azure REST 文檔網站。 按在任何 API 上**試用它**，複製承載權杖，並將其添加到 API 呼叫中。
要永久刪除工作區，請使用[工作區 - 使用]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)強制標記刪除 REST API 呼叫：
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
哪裡'eyJ0eXIIIJKV1Qi...' 表示完整的授權權杖。

## <a name="recover-workspace"></a>恢復工作區

如果對在虛刪除操作之前關聯的工作區的訂閱和資源組具有"參與者"許可權，則可以在其虛刪除期間恢復該資料夾，包括其資料、配置和連接的代理。 在虛刪除期之後，工作區不可恢復並分配進行永久刪除。 已刪除工作區的名稱在虛刪除期間保留，在嘗試創建新工作區時不能使用。  

您可以通過使用以下工作區創建方法重新創建工作區來恢復工作區：只要使用已刪除的工作區詳細資訊填充以下屬性[，PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)即可恢復它：

* 訂用帳戶識別碼
* 資源群組名稱
* 工作區名稱
* 區域

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

恢復操作後，工作區及其所有資料將返回。 刪除解決方案和連結服務時，將從工作區中永久刪除，應重新配置這些服務以使工作區達到以前配置的狀態。 在重新安裝關聯的解決方案並將其架構添加到工作區之前，某些資料可能無法在工作區恢復後進行查詢。

> [!NOTE]
> * [Azure 門戶](https://portal.azure.com)不支援工作區恢復。 
> * 在虛刪除期間重新創建工作區表示此工作區名稱已在使用中。 
> 
