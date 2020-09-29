---
title: Azure 資訊安全中心的檔案完整性監視
description: 瞭解如何使用 Azure 資訊安全中心中的檔案完整性監視比較基準。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 89230b6aca9e225c28a1efd0b99476b35f4d8db0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439552"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>使用檔案完整性監視比較基準 (FIM)

檔案完整性監視 (FIM) 在資源中的機密區域發生變更時通知您，因此您可以調查並處理未經授權的活動。 FIM 會監視 Windows 檔案、Windows 登錄和 Linux 檔案。

本主題說明如何在檔案和登錄上啟用 FIM。 如需 FIM 的詳細資訊，請參閱 [Azure 資訊安全中心中的檔案完整性監視](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>為何要使用 FIM？

作業系統、應用程式和相關設定會控制資源的行為和安全性狀態。 因此，攻擊者會以控制您資源的檔案為目標，以便 overtake 資源的作業系統和（或）執行活動，而不會被偵測到。

事實上，許多法規合規性標準（例如 PCI-DSS & ISO 17799）都需要執行 FIM 控制項。  

## <a name="enable-built-in-recursive-registry-checks"></a>啟用內建遞迴登錄檢查

FIM 登錄 hive 預設值提供便利的方式來監視一般安全性區域內的遞迴變更。  例如，敵人可以設定在啟動或關閉時執行，以在 LOCAL_SYSTEM 內容中執行腳本。  若要監視此類型的變更，請啟用內建的檢查。  

![登錄](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 遞迴檢查只適用于建議的安全性 hive，而不會套用至自訂登錄路徑。  

## <a name="add-a-custom-registry-check"></a>新增自訂登錄檢查

FIM 基準一開始會識別作業系統和支援應用程式的已知良好狀態特性。  在此範例中，我們將著重于 Windows Server 2008 和更新版本的密碼原則設定。


|原則名稱                 | 登錄設定|
|---------------------------------------|-------------|
|網域控制站：拒絕電腦帳戶密碼變更| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|網域成員：安全通道資料加以數位加密或簽章 (自動)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|網域成員：安全通道資料加以數位加密 (可能的話)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|網域成員：安全通道資料加以數位簽章 (可能的話)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|網域成員：停用電腦帳戶密碼變更|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|網域成員：最長電腦帳戶密碼有效期|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|網域成員：要求增強式 (Windows 2000 或更新) 工作階段索引鍵|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|網路安全性：限制 NTLM：此網域中的 NTLM 驗證|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|網路安全性：限制 NTLM：新增此網域中的伺服器例外狀況|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|網路安全性: 限制 NTLM: 稽核這個網域的 NTLM 驗證|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 若要深入瞭解各種作業系統版本所支援的登錄設定，請參閱 [群組原則設定參考試算表](https://www.microsoft.com/download/confirmation.aspx?id=25250)。

*若要設定 FIM 來監視登錄基準：*

1. 在 [ **為變更追蹤新增 windows** 登錄] 視窗的 [ **Windows** 登錄機碼] 文字方塊中，輸入登錄機碼。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![在登錄上啟用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="track-changes-to-windows-files"></a>追蹤 Windows 檔案的變更

1. 在 [ **新增變更追蹤的 Windows** 檔案] 視窗的 [ **輸入路徑** ] 文字方塊中，輸入包含您要追蹤之檔案的資料夾。在下圖的範例中， **Contoso Web 應用程式** 位於 D:\ **ContosWebApp** 資料夾結構內的磁片磁碟機。  
1. 藉由提供設定類別的名稱、啟用遞迴，以及指定具有萬用字元 ( * ) 尾碼的最上層資料夾，來建立自訂的 Windows 檔案專案。

    ![在檔案上啟用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieve-change-data"></a>取出變更資料

檔案完整性監視資料位於 Azure Log Analytics/ConfigurationChange 資料表集內。  

 1. 設定時間範圍以抓取依資源變更的摘要。
在下列範例中，我們會在登錄和檔案的分類中，抓取過去14天內的所有變更：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 若要查看登錄變更的詳細資料：

    1. 從**where** **子句移除檔案**， 
    1. 移除摘要行，並以排序子句取代：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

您可以將報表匯出至 CSV 以封存及/或向 Power BI 報表。  

![FIM 資料](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)