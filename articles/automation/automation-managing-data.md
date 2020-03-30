---
title: 管理 Azure 自動化資料
description: 本文章包含用於管理 Azure 自動化環境的多個主題。  目前將資料保留和備份 Azure 自動化災害復原併入 Azure 自動化中。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132888"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自動化資料

本文章包含用於管理 Azure 自動化環境的多個主題。

## <a name="data-retention"></a>資料保留

刪除 Azure 自動化中的資源時，在永久刪除之前，將資源保留 30 天以進行審核。 在此期間，您無法查看或使用資源。 此原則也適用於屬於已刪除的自動化帳戶的資源。

Azure 自動化會自動刪除並永久刪除超過 30 天的作業。

下表摘要說明不同的資源的保留原則。

| 資料 | 原則 |
|:--- |:--- |
| 帳戶 |在使用者刪除帳戶 30 天后永久刪除。 |
| Assets |在使用者刪除資產 30 天后或使用者刪除持有資產的帳戶 30 天后永久刪除。 |
| 模組 |在使用者刪除模組 30 天后或使用者刪除保留該模組的帳戶 30 天后永久刪除。 |
| Runbook |在使用者刪除資源 30 天后或使用者刪除保存資源的帳戶 30 天后永久刪除。 |
| 工作 |刪除並永久刪除後 30 天上次修改。 這可以是在工作完成、停止或暫止之後。 |
| 節點組態/MOF 檔案 |生成新節點配置 30 天后，將永久刪除舊節點配置。 |
| DSC 節點 |使用 Azure 門戶或 Windows PowerShell 中的[取消註冊 Azure 自動化Ddlet](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode)從自動化帳戶登出節點 30 天后永久刪除。 節點也會在使用者刪除保存節點的帳戶 30 天后永久刪除。 |
| 節點報告 |該節點產生新的報告之後的 90 天永久移除 |

保留原則適用於所有使用者，而且目前無法自訂。

但是，如果需要將資料保留更長時間，可以將 Runbook 作業日誌轉發到 Azure 監視器日誌。 有關詳細資訊，請查看將[Azure 自動化作業資料轉發到 Azure 監視器日誌](automation-manage-send-joblogs-log-analytics.md)。

## <a name="backing-up-azure-automation"></a>備份 Azure 自動化

在 Microsoft Azure 中刪除自動化帳戶時，會刪除帳戶中的所有物件，包括 Runbook、模組、組態、設定、工作和資產。 刪除帳戶之後，就無法復原物件。  您可以使用下列資訊，在刪除之前備份您的自動化帳戶的內容。

### <a name="runbooks"></a>Runbook

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition)Cmdlet，將您的 Runbook 匯出為指令碼檔案。 可以將這些指令碼檔案匯入到另一個自動化帳戶，如 [建立或匯入 Runbook](/previous-versions/azure/dn643637(v=azure.100))中所述。

### <a name="integration-modules"></a>整合模組

您無法從 Azure 自動化匯出整合模組。 您必須確定它們可供在自動化帳戶外部使用。

### <a name="assets"></a>Assets

您無法從 Azure 自動化匯出 [資產](/previous-versions/azure/dn939988(v=azure.100)) 。  使用 Azure 入口網站時，您必須記下變數、認證、憑證、連線及排程的詳細資料。  然後必須手動建立您匯入到另一個自動化的 Runbook 所使用的任何資產。

您可以使用 [Azure Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) 來擷取未加密的資產的詳細資料並加以儲存供日後參考，或在另一個自動化帳戶中建立對等的資產。

您無法使用 Cmdlet 擷取加密的變數的值或認證的密碼欄位。 如果您不知道這些值，則可從 Runbook 使用 [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) 和 [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) 活動來擷取它們。

您無法從 Azure 自動化匯出憑證。 您必須確定 Azure 外部有任何憑證可供使用。

### <a name="dsc-configurations"></a>DSC 組態

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) Cmdlet，將您的設定匯出為指令碼檔案。 這些組態可以匯入並用於另一個自動化帳戶中。

## <a name="geo-replication-in-azure-automation"></a>Azure 自動化中的異地複寫

異地複寫是 Azure 自動化帳戶中的標準功能，可將帳戶資料備份到其他地理區域做為備援。 您可以在設定帳戶時選擇主要區域，然後就會自動指派次要區域給帳戶。 從主要區域複製到次要區域的資料會持續更新，以防止資料遺失。  

下表顯示可用的主要和次要區域配對：

| Primary | 次要 |
| --- | --- |
| 美國中南部 |美國中北部 |
| 美國東部 2 |美國中部 |
| 西歐 |北歐 |
| 東南亞 |東亞 |
| 日本東部 |日本西部 |

萬一主區域資料遺失，Microsoft 會嘗試將它復原。 如果主要資料無法復原，則會執行異地容錯移轉，而且將透過受影響客戶的訂用帳戶將此情況通知他們。
