---
title: 管理 Azure 自動化資料
description: 本文提供 Azure 自動化的資料管理概念，包括資料保留和備份。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835201"
---
# <a name="management-of-azure-automation-data"></a>管理 Azure 自動化資料

本文包含在 Azure 自動化環境中管理資料的數個主題。

## <a name="data-retention"></a>資料保留

當刪除 Azure 自動化中的資源時，會在永久移除該資源之前，將其保留數天作為稽核用途。 在這段期間將無法看到或使用該資源。 此原則也適用於屬於已刪除自動化帳戶的資源。

下表摘要說明不同的資源的保留原則。

| 資料 | 原則 |
|:--- |:--- |
| 帳戶 |帳戶會在使用者刪除的 30 天後永久移除。 |
| Assets |資產會在使用者刪除該資產，或刪除持有該資產的帳戶 30 天後永久移除。 |
| DSC 節點 |當使用 Azure 入口網站或在 Windows PowerShell 中使用 [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) Cmdlet 從自動化帳戶取消註冊 DSC 節點後，該節點會在此動作的 30 天後永久移除。 節點也會在使用者刪除持有該節點的帳戶 30 天後永久移除。 |
| 工作 |工作會在經過修改 (例如工作完成、停止或暫停) 的 30 天後刪除並永久移除。 |
| 模組 |模組會在使用者刪除該模組，或刪除持有該模組的帳戶 30 天後永久移除。 |
| 節點組態/MOF 檔案 |舊節點設定會在新節點設定產生的 30 天後永久移除。 |
| 節點報告 |節點產生新的報告之後，舊節點報告會於 90 天後永久移除。 |
| Runbook |Runbook 會在使用者刪除資源，或刪除持有該資源的帳戶 30 天後永久移除。 |

保留原則適用於所有使用者，且目前無法自訂。 不過，如果需要將資料保留較長的時間，則可[將 Azure 自動化工作資料轉送至 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>資料備份

當在 Azure 中刪除自動化帳戶時，也會刪除帳戶中的所有物件。 這些物件包括 Runbook、模組、組態、設定、工作和資產。 刪除帳戶之後即無法復原物件。 您可使用下列資訊，在刪除之前備份自動化帳戶的內容。

### <a name="runbooks"></a>Runbook

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)Cmdlet，將您的 Runbook 匯出為指令碼檔案。 您可將這些指令碼檔案匯入至另一個自動化帳戶，如[在 Azure 自動化中管理 Runbook](manage-runbooks.md) 所述。

### <a name="integration-modules"></a>整合模組

您無法從 Azure 自動化匯出整合模組。 您必須在自動化帳戶之外提供這些模組。

### <a name="assets"></a>Assets

您無法匯出 Azure 自動化資產：憑證、連線、認證、排程和變數。 相反地，您可使用 Azure 入口網站和 Azure Cmdlet 來記錄這些資產的詳細資料， 然後使用這些詳細資料，為所匯入至另一個自動化帳戶的 Runbook，建立要供其使用的任何資產。

您無法使用 Cmdlet 擷取加密變數或認證密碼欄位的值。 如果不知道這些值，則可在 Runbook 中加以擷取。 如需擷取變數值，請參閱 [Azure 自動化中的變數資產](shared-resources/variables.md)。 若要深入了解如何擷取認證值的詳細資訊，請參閱 [Azure 自動化中的認證資產](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 組態

您可使用 Azure 入口網站或 Windows PowerShell 中的 [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) Cmdlet，將 DSC 設定匯出為指令碼檔案。 您可匯入至另一個自動化帳戶中來使用這些設定。

## <a name="geo-replication-in-azure-automation"></a>Azure 自動化中的異地複寫

異地複寫在 Azure 自動化帳戶中是標準功能。 在設定帳戶時，選擇主要區域。 內部自動化地理複寫服務會自動將次要區域指派給該帳戶。 然後，服務會持續從主要區域將帳戶資料備份到次要區域。 如需主要和次要區域的完整清單，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

自動化異地複寫服務所建立備份是自動化資產、設定與其他類似項目等的完整複本。 如果主要區域停止服務且遺失資料，即可使用此備份。 萬一主要區域的資料遺失，Microsoft 即會嘗試將其復原。 如果公司無法復原主要資料，則會使用自動容錯移轉，並透過 Azure 訂用帳戶來通知狀況。 

如果發生區域性失敗，則外部客戶將無法直接存取自動化異地複寫服務。 如果想要在發生區域性失敗時保留自動化設定和 Runbook：

1. 選取要與主要自動化帳戶地理區域配對的次要區域。

2. 在次要區域中建立自動化帳戶。

3. 在主要帳戶中，將 Runbook 匯出為指令碼檔案。

4. 將 Runbook 匯入至次要區域中的自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure 自動化中的安全資產，請參閱 [Azure 自動化中的安全資產加密](automation-secure-asset-encryption.md)。
* 若要深入了解異地複寫的詳細資訊，請參閱[建立並使用主動式異地複寫](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) (英文)。