---
title: 管理 Azure 自動化資料
description: 本文章包含用於管理 Azure 自動化環境的多個主題。  目前將資料保留和備份 Azure 自動化災害復原併入 Azure 自動化中。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984652"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自動化資料

本文包含多個主題,用於在 Azure 自動化環境中管理數據。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="data-retention"></a>資料保留

刪除 Azure 自動化中的資源時,在永久刪除之前,資源將保留數天以進行審核。 在此期間,您無法查看或使用資源。 此策略也適用於屬於已刪除的自動化帳戶的資源。

下表摘要說明不同的資源的保留原則。

| 資料 | 原則 |
|:--- |:--- |
| 帳戶 |帳戶在使用者刪除帳戶 30 天後被永久刪除。 |
| Assets |在使用者刪除資產 30 天后或使用者刪除持有該資產的帳戶 30 天后,將永久刪除資產。 |
| DSC 節點 |使用 Azure 門戶或 Windows PowerShell 中的[「取消註冊-AzAutomationDnode」cmdlet](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0)從自動化帳戶註銷 30 天後,將永久刪除 DSC 節點。 在使用者刪除保存該節點的帳戶 30 天後,節點也會被永久刪除。 |
| 工作 |修改後 30 天內刪除和永久刪除作業,例如,作業完成後,該作業將停止或掛起。 |
| 模組 |模組在使用者刪除該模組 30 天后或使用者刪除持有該模組的帳戶 30 天後被永久刪除。 |
| 節點組態/MOF 檔案 |生成新節點配置 30 天後,將永久刪除舊節點配置。 |
| 節點報告 |節點報告在生成該節點的新報告 90 天后被永久刪除。 |
| Runbook |Runbook 在使用者刪除資源 30 天后或使用者刪除保存資源的帳戶 30 天後被永久刪除。 |

保留策略適用於所有使用者,當前無法自定義。 但是,如果需要將資料保留更長時間,可以將[Azure 自動化作業資料轉寄到 Azure 監視器日誌](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>資料備份

刪除 Azure 中的自動化帳戶時,該帳戶中的所有物件都將被刪除。 這些物件包括 Runbook、模組、配置、設置、作業和資產。 刪除帳戶後無法恢復它們。 在刪除自動化帳戶之前,可以使用以下信息備份其內容。

### <a name="runbooks"></a>Runbook

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)Cmdlet，將您的 Runbook 匯出為指令碼檔案。 您可以將這些文本檔匯入到另一個自動化帳戶中,如[Azure 自動化中的管理 Runbook 中](manage-runbooks.md)所述。

### <a name="integration-modules"></a>整合模組

無法從 Azure 自動化匯出集成模組。 您必須在自動化帳戶之外提供它們。

### <a name="assets"></a>Assets

無法匯出 Azure 自動化資產:證書、連接、憑據、計畫和變數。 相反,您可以使用 Azure 門戶和 Azure cmdlet 來記錄這些資產的詳細資訊。 然後使用這些詳細資訊創建您導入到另一個自動化帳戶的 Runbook 使用的任何資產。

無法使用 cmdlet 檢索加密變數或憑據的密碼欄位的值。 如果您不知道這些值,可以在 Runbook 中檢索它們。 有關檢索變數值,請參閱[Azure 自動化 中的變數資產](shared-resources/variables.md)。 要瞭解有關檢索認證的更多資訊,請參閱[Azure 自動化 中的認證資產](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 組態

您可以使用 Azure 門戶或 Windows PowerShell 中的[匯出-AzAutomationDsc配置](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
)cmdlet 將 DSC 設定匯出到文本檔。 您可以在另一個自動化帳戶中導入和使用這些配置。

## <a name="geo-replication-in-azure-automation"></a>Azure 自動化中的異地複寫

異地複製是 Azure 自動化帳戶中的標準配置。 設置帳戶時,您可以選擇主要區域。 內部自動化異地複製服務自動將輔助區域分配給帳戶。 然後,該服務會持續將帳戶數據從主區域備份到輔助區域。 主要區域與輔助區域的完整清單可在[業務連續性和災難復原 (BCDR) 中找不到:Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

由自動化異地複製服務創建的備份是自動化資產、配置等的完整副本。 如果主區域出現故障並丟失數據,可以使用此備份。 如果主區域的數據丟失,Microsoft 會嘗試恢復它。 如果公司無法恢復主數據,則它使用自動故障轉移,並通過 Azure 訂閱通知您情況。 

如果出現區域故障,則外部客戶無法直接訪問自動化異地複製服務。 如果要在區域故障期間維護自動化設定與執行簿:

1. 選擇要與主自動化帳戶的地理區域配對的輔助區域。

2. 在輔助區域中創建自動化帳戶。

3. 在主帳戶中,將 Runbook 匯出為腳本檔。

4. 將 Runbook 導入輔助區域中的自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關 Azure 自動化中安全資產的更多資訊,請參閱[在 Azure 自動化 中加密安全資產](automation-secure-asset-encryption.md)。

* 要瞭解有關異地複製的更多,請參閱[建立和使用活動異地複製](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)。