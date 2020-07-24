---
title: Azure 自動化資料安全性
description: 本文可協助您瞭解 Azure 自動化如何保護您的隱私權及保護您的資料。
services: automation
ms.subservice: shared-capabilities
ms.date: 07/20/2020
ms.topic: conceptual
ms.openlocfilehash: 610c2050150a533e246bc74ed7750ce87f7cf617
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004642"
---
# <a name="management-of-azure-automation-data"></a>管理 Azure 自動化資料

本文包含幾個主題，說明如何在 Azure 自動化環境中保護和保護資料。

## <a name="tls-12-enforcement-for-azure-automation"></a>Azure 自動化的 TLS 1.2 強制

若要確保傳輸中的資料安全性以 Azure 自動化，我們強烈建議您設定傳輸層安全性（TLS）1.2 的使用。 以下是透過 HTTPS 與 Automation 服務通訊的方法或用戶端清單：

* Webhook 呼叫

* 混合式 Runbook 背景工作角色，包括由更新管理管理的電腦，以及變更追蹤和清查。

* DSC 節點

我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。 從2020年9月開始，我們會開始強制執行 TLS 1.2 和更新版本的加密通訊協定。

除非有絕對必要，否則我們不建議將代理程式明確地設定為只使用 TLS 1.2，因為這樣可能會中斷平台層級的安全性功能，此功能可在更安全的較新通訊協定 (例如 TLS 1.3) 推出時，自動偵測並加以運用。

如需使用適用于 Windows 和 Linux 的 Log Analytics 代理程式的 TLS 1.2 支援相關資訊，這是混合式 Runbook 背景工作角色的相依性，請參閱[Log analytics 代理程式總覽-TLS 1.2](..//azure-monitor/platform/log-analytics-agent.md#tls-12-protocol)。 

### <a name="platform-specific-guidance"></a>平台專屬的指引

|平台/語言 | 支援 | 相關資訊 |
| --- | --- | --- |
|Linux | Linux 發行版本通常會依賴 [OpenSSL](https://www.openssl.org) 來取得 TLS 1.2 支援。  | 請檢查 [OpenSSL 變更記錄](https://www.openssl.org/news/changelog.html)來確認支援的 OpenSSL 版本。|
| Windows 8.0 - 10 | 支援，而且已預設為啟用。 | 請確認您仍在使用[預設設定](/windows-server/security/tls/tls-registry-settings)。  |
| Windows Server 2012 - 2016 | 支援，而且已預設為啟用。 | 確認您仍在使用[預設設定](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 和 Windows Server 2008 R2 SP1 | 支援，但預設為不啟用。 | 請參閱[傳輸層安全性 (TLS) 登錄設定](/windows-server/security/tls/tls-registry-settings)頁面，了解詳細的啟用方式。  |

## <a name="data-retention"></a>資料保留

當刪除 Azure 自動化中的資源時，會在永久移除該資源之前，將其保留數天作為稽核用途。 在這段期間將無法看到或使用該資源。 此原則也適用於屬於已刪除自動化帳戶的資源。

下表摘要說明不同的資源的保留原則。

| 資料 | 原則 |
|:--- |:--- |
| 帳戶 |帳戶會在使用者刪除的 30 天後永久移除。 |
| Assets |資產會在使用者刪除該資產，或刪除持有該資產的帳戶 30 天後永久移除。 資產包括變數、排程、認證、憑證、Python 2 套件和連線。 |
| DSC 節點 |當使用 Azure 入口網站或在 Windows PowerShell 中使用 [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) Cmdlet 從自動化帳戶取消註冊 DSC 節點後，該節點會在此動作的 30 天後永久移除。 節點也會在使用者刪除持有該節點的帳戶 30 天後永久移除。 |
| 工作 |工作會在經過修改 (例如工作完成、停止或暫停) 的 30 天後刪除並永久移除。 |
| 模組 |模組會在使用者刪除該模組，或刪除持有該模組的帳戶 30 天後永久移除。 |
| 節點組態/MOF 檔案 |舊節點設定會在新節點設定產生的 30 天後永久移除。 |
| 節點報告 |節點產生新的報告之後，舊節點報告會於 90 天後永久移除。 |
| Runbook |Runbook 會在使用者刪除資源，或刪除持有該資源的帳戶 30 天後永久移除。 |

保留原則適用於所有使用者，且目前無法自訂。 不過，如果需要將資料保留較長的時間，則可[將 Azure 自動化工作資料轉送至 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>資料備份

當在 Azure 中刪除自動化帳戶時，也會刪除帳戶中的所有物件。 這些物件包括 Runbook、模組、組態、設定、工作和資產。 刪除帳戶之後即無法復原物件。 您可使用下列資訊，在刪除之前備份自動化帳戶的內容。

### <a name="runbooks"></a>Runbook

您可以使用 Azure 入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition)Cmdlet，將您的 Runbook 匯出為指令碼檔案。 您可將這些指令碼檔案匯入至另一個自動化帳戶，如[在 Azure 自動化中管理 Runbook](manage-runbooks.md) 所述。

### <a name="integration-modules"></a>整合模組

您無法從 Azure 自動化匯出整合模組。 您必須在自動化帳戶之外提供這些模組。

### <a name="assets"></a>Assets

您無法匯出 Azure 自動化資產：憑證、連線、認證、排程和變數。 相反地，您可使用 Azure 入口網站和 Azure Cmdlet 來記錄這些資產的詳細資料， 然後使用這些詳細資料，為所匯入至另一個自動化帳戶的 Runbook，建立要供其使用的任何資產。

您無法使用 Cmdlet 擷取加密變數或認證密碼欄位的值。 如果不知道這些值，則可在 Runbook 中加以擷取。 如需擷取變數值，請參閱 [Azure 自動化中的變數資產](shared-resources/variables.md)。 若要深入了解如何擷取認證值的詳細資訊，請參閱 [Azure 自動化中的認證資產](shared-resources/credentials.md)。

### <a name="dsc-configurations"></a>DSC 組態

您可使用 Azure 入口網站或 Windows PowerShell 中的 [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0) Cmdlet，將 DSC 設定匯出為指令碼檔案。 您可匯入至另一個自動化帳戶中來使用這些設定。

## <a name="geo-replication-in-azure-automation"></a>Azure 自動化中的異地複寫

異地複寫在 Azure 自動化帳戶中是標準功能。 在設定帳戶時，選擇主要區域。 內部自動化地理複寫服務會自動將次要區域指派給該帳戶。 然後，服務會持續從主要區域將帳戶資料備份到次要區域。 如需主要和次要區域的完整清單，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域](../best-practices-availability-paired-regions.md)。

自動化異地複寫服務所建立備份是自動化資產、設定與其他類似項目等的完整複本。 如果主要區域停止服務且遺失資料，即可使用此備份。 萬一主要區域的資料遺失，Microsoft 即會嘗試將其復原。 如果公司無法復原主要資料，則會使用自動容錯移轉，並透過 Azure 訂用帳戶來通知狀況。

如果發生區域性失敗，則外部客戶將無法直接存取自動化異地複寫服務。 如果想要在發生區域性失敗時保留自動化設定和 Runbook：

1. 選取要與主要自動化帳戶地理區域配對的次要區域。

2. 在次要區域中建立自動化帳戶。

3. 在主要帳戶中，將 Runbook 匯出為指令碼檔案。

4. 將 Runbook 匯入至次要區域中的自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure 自動化中的安全資產，請參閱 [Azure 自動化中的安全資產加密](automation-secure-asset-encryption.md)。

* 若要深入了解異地複寫的詳細資訊，請參閱[建立並使用主動式異地複寫](../azure-sql/database/active-geo-replication-overview.md) (英文)。
