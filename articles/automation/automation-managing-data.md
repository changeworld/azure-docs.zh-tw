---
title: 管理 Azure 自動化資料
description: 本文章包含用於管理 Azure 自動化環境的多個主題。  目前將資料保留和備份 Azure 自動化災害復原併入 Azure 自動化中。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984652"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自動化資料

本文包含多個主題，可用於管理 Azure 自動化環境中的資料。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="data-retention"></a>資料保留

當您刪除 Azure 自動化中的資源時，在永久移除之前，會保留幾天供審核之用。 您在這段時間內看不到或無法使用資源。 此原則也適用于屬於已刪除之自動化帳戶的資源。

下表摘要說明不同的資源的保留原則。

| 資料 | 原則 |
|:--- |:--- |
| 帳戶 |帳戶會在使用者刪除後的30天內永久移除。 |
| 資產 |資產會在使用者刪除後的30天內永久移除，或在使用者刪除持有資產的帳戶後30天。 |
| DSC 節點 |DSC 節點在使用 Azure 入口網站或 Windows PowerShell 中的[AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0)指令程式，從自動化帳戶取消註冊後的30天內永久移除。 節點也會在使用者刪除保存節點的帳戶後的30天內永久移除。 |
| 工作 |作業會在修改後的30天內刪除並永久移除，例如，在作業完成、停止或暫停之後。 |
| 模組 |模組會在使用者刪除之後的30天內永久移除，或在使用者刪除保存模組的帳戶後的30天內。 |
| 節點組態/MOF 檔案 |在新節點設定產生後的30天內，會永久移除舊的節點設定。 |
| 節點報告 |在為該節點產生新報告之後，會永久移除節點報告90天。 |
| Runbook |Runbook 會在使用者刪除資源後的30天內永久移除，或在使用者刪除保存資源的帳戶後30天。 |

保留原則適用于所有使用者，目前無法自訂。 不過，如果您需要將資料保留較長的時間，您可以將[Azure 自動化作業資料轉送至 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>資料備份

當您在 Azure 中刪除自動化帳戶時，會刪除帳戶中的所有物件。 這些物件包括 runbook、模組、設定、設定、作業和資產。 刪除帳戶之後，就無法復原它們。 您可以使用下列資訊來備份您的自動化帳戶的內容，然後再刪除它。

### <a name="runbooks"></a>Runbook

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)Cmdlet，將您的 Runbook 匯出為指令碼檔案。 您可以將這些腳本檔案匯入到另一個自動化帳戶，如[管理 Azure 自動化中的 runbook](manage-runbooks.md)中所述。

### <a name="integration-modules"></a>整合模組

您無法從 Azure 自動化匯出整合模組。 您必須將它們提供給自動化帳戶之外。

### <a name="assets"></a>資產

您無法匯出 Azure 自動化資產：憑證、連接、認證、排程和變數。 相反地，您可以使用 Azure 入口網站和 Azure Cmdlet 來記錄這些資產的詳細資料。 然後使用這些詳細資料，建立您匯入至另一個自動化帳戶的 runbook 所使用的任何資產。

您無法使用 Cmdlet 來抓取加密變數的值或認證的密碼欄位。 如果您不知道這些值，您可以在 runbook 中加以取出。 如需抓取變數值，請參閱[Azure 自動化中的變數資產](shared-resources/variables.md)。 若要瞭解如何取得認證值的詳細資訊，請參閱[Azure 自動化中的認證資產](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 組態

您可以使用 Windows PowerShell 中的 Azure 入口網站或[AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) Cmdlet，將 DSC 設定匯出至腳本檔案。 您可以在另一個自動化帳戶中匯入和使用這些設定。

## <a name="geo-replication-in-azure-automation"></a>Azure 自動化中的異地複寫

異地複寫在 Azure 自動化帳戶中是標準的。 當您設定帳戶時，請選擇主要區域。 內部自動化地理複寫服務會自動將次要區域指派給帳戶。 然後，服務會持續將帳戶資料從主要區域備份到次要區域。 主要和次要區域的完整清單可在[商務持續性和嚴重損壞修復（BCDR）： Azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中找到。 

自動化異地複寫服務所建立的備份，是自動化資產、設定等的完整複本。 如果主要區域停機並遺失資料，則可以使用此備份。 在不太可能發生的情況下，主要區域的資料遺失時，Microsoft 會嘗試加以復原。 如果公司無法復原主要資料，它會使用自動容錯移轉，並透過您的 Azure 訂用帳戶通知您這種情況。 

如果發生區域性失敗，自動化異地複寫服務無法直接存取外部客戶。 如果您想要在區域失敗期間維護自動化設定和 runbook：

1. 選取要與主要自動化帳戶的地理區域配對的次要區域。

2. 在次要區域中建立自動化帳戶。

3. 在主要帳戶中，將您的 runbook 匯出為腳本檔案。

4. 將 runbook 匯入至次要區域中的自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Azure 自動化中的安全資產，請參閱[Azure 自動化中的加密安全資產](automation-secure-asset-encryption.md)。

* 若要瞭解異地複寫的詳細資訊，請參閱[建立和使用主動式異地](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)複寫。