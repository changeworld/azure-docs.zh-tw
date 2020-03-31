---
title: Azure 資訊安全中心的檔案完整性監視
description: 瞭解如何將基線與 Azure 安全中心中的檔完整性監視進行比較。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664400"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>使用檔案完整性監視比較基準 (FIM)

當資源中的敏感區域發生更改時，檔完整性監視 （FIM） 會通知您，以便您可以調查和解決未經授權的活動。 FIM 監視 Windows 檔、Windows 註冊表和 Linux 檔。

本主題介紹如何在檔和註冊表上啟用 FIM。 有關 FIM 的詳細資訊，請參閱[Azure 安全中心中的檔完整性監視](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>為什麼要使用 FIM？

作業系統、應用程式和相關配置控制資源的行為和安全狀態。 因此，攻擊者將目標鎖定控制資源的檔，以便超越資源的作業系統和/或執行活動而不檢測到。

事實上，許多法規遵從性標準（如 PCI-DSS & ISO 17799）都需要實施 FIM 控制。  

## <a name="enable-built-in-recursive-registry-checks"></a>啟用內置遞迴註冊表檢查

FIM 註冊表配置單元預設值提供了一種監視公共安全區域中的遞迴更改的便捷方法。  例如，攻擊者可以通過在啟動或關閉時配置執行來配置腳本以在LOCAL_SYSTEM上下文中執行。  要監視此類型的更改，請啟用內置檢查。  

![登錄](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 遞迴檢查僅適用于建議的安全配置單元，不適用於自訂註冊表路徑。  

## <a name="adding-a-custom-registry-check"></a>添加自訂註冊表檢查

FIM 基線首先確定作業系統和支援應用程式的已知良好狀態的特徵。  在此示例中，我們將重點介紹 Windows Server 2008 及更高版本的密碼原則配置。


|原則名稱                 | 登錄設定|
|---------------------------------------|-------------|
|網域控制站：拒絕電腦帳戶密碼變更| MACHINE_系統_當前控制集_服務 [Netlogon]參數\拒絕密碼更改|
|網域成員：安全通道資料加以數位加密或簽章 (自動)|MACHINE_系統_電流控制集_服務[淨]參數\需要簽名或密封|
|網域成員：安全通道資料加以數位加密 (可能的話)|MACHINE_系統_電流控制集_服務[Netlogon]參數\SealSecure通道|
|網域成員：安全通道資料加以數位簽章 (可能的話)|MACHINE_系統_當前控制集_服務 [Netlogon]參數\SignSecure通道|
|網域成員：停用電腦帳戶密碼變更|MACHINE_系統\當前控制集_服務 [Netlogon]參數\禁用密碼更改|
|網域成員：最長電腦帳戶密碼有效期|MACHINE_系統_電流控制集_服務[Netlogon]參數\最大密碼|
|網域成員：要求增強式 (Windows 2000 或更新) 工作階段索引鍵|MACHINE_系統_電流控制集_服務[淨日誌]參數\需要強鍵|
|網路安全：限制 NTLM：在此域中的 NTLM 身份驗證|MACHINE_系統_當前控制集_服務 [Netlogon]參數\限制NTLMInDomain|
|網路安全性：限制 NTLM：新增此網域中的伺服器例外狀況|MACHINE_系統_電流控制集_服務[Netlogon]參數\DC允許NTLM伺服器|
|網路安全性: 限制 NTLM: 稽核這個網域的 NTLM 驗證|MACHINE_系統_當前控制集_服務 [淨日誌]參數\審核NTLMInDomain|

> [!NOTE]
> 要瞭解有關各種作業系統版本支援的註冊表設置的詳細資訊，請參閱[群組原則設置參考試算表](https://www.microsoft.com/download/confirmation.aspx?id=25250)。

*要將 FIM 配置為監視註冊表基線，*

1. 在"**添加 Windows 註冊表用於更改跟蹤"** 視窗中，在 **"Windows 註冊表金鑰"** 文字方塊中輸入登錄機碼。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![在註冊表上啟用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>跟蹤對 Windows 檔的更改

1. 在"**添加 Windows 檔以進行更改跟蹤"** 視窗中，在 **"輸入路徑**文本"框中，輸入包含要跟蹤的檔的資料夾。在下圖中的示例中 **，Contoso Web 應用**駐留在 D：***ContosWebApp**資料夾結構中的磁碟機。  
1. 通過提供設置類的名稱、啟用遞迴以及使用萬用字元 （*） 尾碼指定頂部資料夾，創建自訂 Windows 檔條目。

    ![在檔上啟用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>檢索更改資料

檔完整性監視資料駐留在 Azure 日誌分析/配置更改表集中。  

 1. 設置時間範圍以按資源檢索更改摘要。
在下面的示例中，我們將檢索註冊表和檔類別中最近 14 天內的所有更改：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 要查看註冊表更改的詳細資訊，

    1. 從**where**子句中刪除**檔**， 
    1. 刪除合計列並將其替換為排序子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

報表可以匯出到 CSV 進行存檔和/或引導到 Power BI 報表。  

![職能指令手冊資料](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)