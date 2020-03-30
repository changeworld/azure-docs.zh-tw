---
title: Azure 安全中心的修復建議 |微軟文檔
description: 本文介紹如何在 Azure 安全中心中修復建議，以保護資源和遵守安全性原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603493"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure 資訊安全中心的補救建議

建議為您提供有關如何更好地保護資源的建議。 您可以按照建議中提供的修正步驟來實現建議。

## <a name="remediation-steps"></a>補救步驟<a name="remediation-steps"></a>

在審閱所有建議後，首先決定修復哪一個。 我們建議您使用[安全分數影響](security-center-recommendations.md#monitor-recommendations)來説明確定優先操作。

1. 從清單中按一下建議。

1. 請按照 **"補救步驟**"部分中的說明進行操作。 每個建議都有自己的一組說明。 以下螢幕截圖顯示了用於將應用程式佈建為僅允許通過 HTTPS 的流量的修正步驟。

    ![建議的詳細資料](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成後，將顯示一條通知，通知您修復是否成功。

## <a name="quick-fix-remediation"></a>快速修復修復<a name="one-click"></a>

通過快速修復，您可以快速修復對多個資源的建議。 它僅適用于具體建議。 快速修復簡化了補救，使您能夠快速提高安全分數，提高環境的安全性。

要實現快速修復修正：

1. 從具有**快速修復**的建議清單中！ 標籤，按一下建議。

    [![選擇快速修復！](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 從 **"不正常資源**"選項卡中，選擇要實現建議的資源，然後按一下"**修復**"。

    > [!NOTE]
    > 某些列出的資源可能會被禁用，因為您沒有修改它們的適當許可權。

1. 在確認框中，閱讀修復詳細資訊和含義。

    ![快速檢修](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影響在 **"修復資源"** 視窗中的灰色框中列出，該視窗中按一下"修復 **"** 後將打開該含義。 它們列出在繼續快速修復修正時發生的更改。

1. 如有必要，插入相關參數，並批准修正。

    > [!NOTE]
    > 修復完成後可能需要幾分鐘才能在 **"正常資源**"選項卡中查看資源。要查看修正操作，請檢查[活動日誌](#activity-log)。

1. 完成後，將顯示一條通知，通知您修復是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>快速修復活動日誌中的修正日誌記錄<a name="activity-log"></a>

修正操作使用範本部署或 REST PATCH API 呼叫在資源上應用配置。 這些操作記錄在[Azure 活動日誌](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="recommendations-with-quick-fix-remediation"></a>具有快速修復修復的建議

|建議|意義|
|---|---|
|應啟用 SQL 伺服器上的審核|此操作將啟用對這些伺服器及其資料庫的 SQL 審核。 <br>**注意**： <ul><li>對於所選 SQL 伺服器的每個區域，該區域中的所有伺服器都將創建並共用用於保存稽核記錄的存儲帳戶。</li><li>為確保正確審核，請勿刪除或重命名資源組或存儲帳戶。</li></ul>|
|應在 SQL 受控執行個體上啟用進階資料安全性|此操作將在選定的 SQL 託管實例上啟用 SQL 高級資料安全 （ADS）。 <br>**注意**： <ul><li>對於所選 SQL 託管實例的每個區域和資源組，將創建用於保存掃描結果的存儲帳戶，並由該區域中的所有實例共用。</li><li> ADS 收費，每個 SQL 託管實例為 15 美元。</li></ul>|
|SQL 受控執行個體上應啟用弱點評定|此操作將啟用所選 SQL 託管實例的 SQL 漏洞評估。 <br>**注意**：<ul><li>SQL 漏洞評估是 SQL 高級資料安全 （ADS） 包的一部分。 如果 AD 尚未啟用，則會自動在託管實例上啟用它。</li><li>對於所選 SQL 託管實例的每個區域和資源組，將創建存儲掃描結果的存儲帳戶，並由該區域中的所有實例共用。</li><li>ADS 每台 SQL 伺服器收費 15 美元。</li></ul>||
|應在 SQL 伺服器上啟用高級資料安全性|此操作將在這些選定的伺服器及其資料庫上啟用高級資料安全 （ADS）。 <br>**注意**：<ul><li>對於所選 SQL 伺服器的每個區域和資源組，該區域中的所有伺服器都將創建並共用用於存儲掃描結果的存儲帳戶 <。</li><li>ADS 每台 SQL 伺服器收費 15 美元。</li></ul>||
|應在 SQL 伺服器上啟用漏洞評估|此操作將啟用這些選定伺服器及其資料庫上的 SQL 漏洞評估。 <br>**注意**：<ul><li>SQL 漏洞評估是 SQL 高級資料安全 （ADS） 包的一部分。 如果 AD 尚未啟用，則 SQL 伺服器上將自動啟用它。</li><li>對於所選 SQL 伺服器的每個區域和資源組，將創建存儲掃描結果的存儲帳戶，並由該區域中的所有實例共用。</li><li>ADS 每台 SQL 伺服器收費 15 美元。</li></ul>||
|應啟用 SQL 資料庫上的透明資料加密|此操作支援所選資料庫上的 SQL 資料庫透明資料加密 （TDE）。 <br>**注意**：預設情況下，將使用服務管理的 TDE 金鑰。
|應啟用儲存體帳戶的安全傳輸|此操作會更新存儲帳戶安全性，使其僅允許安全連線的請求。 （HTTPS）。 <br>**注意**：<ul><li>使用 HTTP 的任何請求都將被拒絕。</li><li>使用 Azure 檔服務時，沒有加密的連接將失敗，包括使用 SMB 2.1、沒有加密的 SMB 3.0 以及 Linux SMB 用戶端的某些風格。 深入了解。</li></ul>|
|Web 應用程式應只可經由 HTTPS 存取|此操作將所選資源上的所有流量從 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 終結點將在瀏覽器中顯示"隱私錯誤"。 因此，具有自訂域的使用者需要驗證自己已設置 SSL 憑證。</li><li>確保資料包和 Web 應用程式防火牆保護應用服務，允許 HTTPS 會話轉發。</li></ul>|
|函式應用程式應只可經由 HTTPS 存取|此操作將所選資源上的所有流量從 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 終結點將在瀏覽器中顯示"隱私錯誤"。 因此，具有自訂域的使用者需要驗證自己已設置 SSL 憑證。</li><li>確保資料包和 Web 應用程式防火牆保護應用服務，允許 HTTPS 會話轉發。</li></ul>|
|API 應用程式應只可經由 HTTPS 存取|此操作將所選資源上的所有流量從 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 終結點將在瀏覽器中顯示"隱私錯誤"。 因此，具有自訂域的使用者需要驗證自己已設置 SSL 憑證。</li><li>確保資料包和 Web 應用程式防火牆保護應用服務，允許 HTTPS 會話轉發。</li></ul>|
|Web 應用程式的遠端偵錯應關閉|此操作將禁用遠端偵錯。|
|函式應用程式的遠端偵錯應關閉|此操作將禁用遠端偵錯。|
|應關閉 API 應用程式的遠端偵錯|此操作將禁用遠端偵錯。|
|CORS 不應允許每項資源存取您的 Web 應用程式|此操作阻止其他域訪問 Web 應用程式。 要允許特定域，請在"允許的原點"欄位中輸入它們（用逗號分隔）。 <br>**注意**：將欄位留空將阻止所有跨源調用。|
|CORS 不應允許每項資源存取函式應用程式|此操作阻止其他域訪問函數應用程式。 要允許特定域，請在"允許的原點"欄位中輸入它們（用逗號分隔）。 <br>**注意**：將欄位留空將阻止所有跨源調用。|
|CORS 不應允許每項資源存取您的 API 應用程式|此操作阻止其他域訪問 API 應用程式。 要允許特定域，請在"允許的原點"欄位中輸入它們（用逗號分隔）。 <br>**注意**：將欄位留空將阻止所有跨源調用。|
|應在虛擬機器上啟用監視代理|此操作在選定的虛擬機器上安裝監視代理。 選擇要向座席報告的工作區。<ul><li>如果更新策略設置為自動，它將部署在新的現有實例上。</li><li>如果更新策略設置為手動，並且希望在現有實例上安裝代理，請選擇核取方塊選項。 [深入了解](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|應啟用 Key Vault 中的診斷記錄|此操作支援金鑰保存庫上的診斷日誌。 診斷日誌和指標保存在所選工作區中。|
|應啟用服務匯流排中的診斷日誌|此操作可在服務匯流排上啟用診斷日誌。 診斷日誌和指標保存在所選工作區中。|

## <a name="next-steps"></a>後續步驟

在本文檔中，您被顯示如何在安全中心修復建議。 要瞭解有關安全中心的更多內容，請參閱以下主題：

* [在 Azure 安全中心設置安全性原則](tutorial-security-policy.md)- 瞭解如何為 Azure 訂閱和資源組配置安全性原則。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
