---
title: 補救 Azure 資訊安全中心中的建議 |Microsoft Docs
description: 本文說明如何在 Azure 資訊安全中心中修復建議，以保護您的資源並符合安全性原則。
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
ms.openlocfilehash: 9beb617ed8626b1fda1c9db98d626ca70ee01755
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042912"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure 資訊安全中心的補救建議

建議可讓您建議如何更有效地保護您的資源。 您可以遵循建議中提供的補救步驟來執行建議。

## <a name="remediation-steps"></a>補救步驟<a name="remediation-steps"></a>

在檢查所有建議之後，請決定要先補救哪一個。 建議您使用「[安全分數影響](security-center-recommendations.md#monitor-recommendations)」來協助優先處理。

1. 從清單中，按一下 [建議]。

1. 遵循**補救步驟**一節中的指示。 每個建議都有自己的一組指示。 下列螢幕擷取畫面顯示將應用程式設定為只允許透過 HTTPS 流量的補救步驟。

    ![建議的詳細資料](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成後，系統會顯示通知，告知您補救是否成功。

## <a name="quick-fix-remediation"></a>快速修正修復<a name="one-click"></a>

快速修正可讓您快速補救多個資源上的建議。 僅適用于特定的建議。 快速修正可簡化補救，並可讓您快速增加安全分數，進而提升您的環境安全性。

若要執行快速修正修復：

1. 從具有**快速修正**的建議清單中！ 標籤中，按一下 [建議]。

    [![選取 [快速修復]！](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 從 [**狀況不良的資源**] 索引標籤中，選取您想要在其中執行建議的資源，然後按一下 [**修復**]。

    > [!NOTE]
    > 部分列出的資源可能已停用，因為您沒有適當的許可權可修改它們。

1. 在確認方塊中，閱讀補救詳細資料和含意。

    ![快速檢修](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 這些含意會列在 [**補救資源**] 視窗中，按一下 [**修復**] 之後開啟的灰色方塊中。 它們會列出繼續進行快速修正補救時所發生的變更。

1. 視需要插入相關的參數，並核准補救措施。

    > [!NOTE]
    > 補救完成後可能需要幾分鐘的時間，才能在 [**狀況良好的資源**] 索引標籤中查看資源。若要查看修復動作，請檢查[活動記錄](#activity-log)。

1. 完成後，系統會顯示通知，告知您補救是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>在活動記錄中快速修正補救記錄<a name="activity-log"></a>

補救作業會使用範本部署或 REST 修補程式 API 呼叫，將設定套用至資源。 這些作業會記錄在[Azure 活動記錄](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="recommendations-with-quick-fix-remediation"></a>具有快速修正補救的建議

|建議|公開|
|---|---|
|應該啟用 SQL Database 上的審核|此動作會在這些伺服器及其資料庫上啟用 SQL 審核。 <br>**注意**： <ul><li>針對所選 SQL Database 的每個區域，將會建立用於儲存 audit 記錄的儲存體帳戶，並由該區域中的所有伺服器共用。</li><li>若要確保適當的審核，請不要刪除或重新命名資源群組或儲存體帳戶。</li></ul>|
|應在 SQL 受控執行個體上啟用進階資料安全性|此動作會在選取的 SQL 受控實例上啟用 SQL Advanced Data Security （ADS）。 <br>**注意**： <ul><li>針對所選 SQL 受控執行個體的每個區域和資源群組，將會建立用於儲存掃描結果的儲存體帳戶，並由該區域中的所有實例共用。</li><li> ADS 的費用為每個 SQL 受控執行個體 $15。</li></ul>|
|應在 SQL 受控執行個體上啟用弱點評定|此動作會在選取的 SQL 受控執行個體上啟用 SQL 弱點評估。 <br>**注意**：<ul><li>SQL 弱點評定是 SQL Advanced Data Security （ADS）套件的一部分。 如果尚未啟用 ADS，則會自動在受控實例上啟用。</li><li>針對所選 SQL 受控執行個體的每個區域和資源群組，將會建立用於儲存掃描結果的儲存體帳戶，並由該區域中的所有實例共用。</li><li>廣告的收費為每 SQL Database $15。</li></ul>||
|應在您的 SQL Database 上啟用 Advanced Data Security|此動作會在這些選取的伺服器和其資料庫上啟用 Advanced Data Security （ADS）。 <br>**注意**：<ul><li>針對所選 SQL Database 的每個區域和資源群組，將會建立用於儲存掃描結果的儲存體帳戶，並由該區域中的所有伺服器共用。 <</li><li>廣告的收費為每 SQL Database $15。</li></ul>||
|應在您的 SQL Database 上啟用弱點評估|此動作會在這些選取的伺服器及其資料庫上啟用 SQL 弱點評估。 <br>**注意**：<ul><li>SQL 弱點評定是 SQL Advanced Data Security （ADS）套件的一部分。 如果尚未啟用廣告，則會自動在 SQL Database 上啟用。</li><li>針對所選 SQL Database 的每個區域和資源群組，將會建立用於儲存掃描結果的儲存體帳戶，並由該區域中的所有實例共用。</li><li>廣告的收費為每 SQL Database $15。</li></ul>||
|應該啟用 SQL Database 上的透明資料加密|此動作會在選取的資料庫上啟用 SQL Database 透明資料加密（TDE）。 <br>**注意**：根據預設，將會使用服務管理的 TDE 金鑰。
|應啟用儲存體帳戶的安全傳輸|此動作會將您的儲存體帳戶安全性更新為只允許安全連線的要求。 （HTTPS）。 <br>**注意**：<ul><li>任何使用 HTTP 的要求都會遭到拒絕。</li><li>當您使用 Azure 檔案服務時，不加密的連線將會失敗，包括使用 SMB 2.1 的案例、沒有加密的 SMB 3.0，以及 Linux SMB 用戶端的某些類別。 深入了解。</li></ul>|
|Web 應用程式應只可經由 HTTPS 存取|此動作會將所選資源上的所有流量從 HTTP 重新導向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 端點會顯示在瀏覽器中，並出現「隱私權錯誤」。 因此，具有自訂網域的使用者必須確認他們已設定 SSL 憑證。</li><li>請確定用來保護 app service 的封包和 web 應用程式防火牆，允許 HTTPS 會話轉送。</li></ul>|
|函式應用程式應只可經由 HTTPS 存取|此動作會將所選資源上的所有流量從 HTTP 重新導向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 端點會顯示在瀏覽器中，並出現「隱私權錯誤」。 因此，具有自訂網域的使用者必須確認他們已設定 SSL 憑證。</li><li>請確定用來保護 app service 的封包和 web 應用程式防火牆，允許 HTTPS 會話轉送。</li></ul>|
|API 應用程式應只可經由 HTTPS 存取|此動作會將所選資源上的所有流量從 HTTP 重新導向到 HTTPS。 <br>**注意**：<ul><li>沒有 SSL 憑證的 HTTPS 端點會顯示在瀏覽器中，並出現「隱私權錯誤」。 因此，具有自訂網域的使用者必須確認他們已設定 SSL 憑證。</li><li>請確定用來保護 app service 的封包和 web 應用程式防火牆，允許 HTTPS 會話轉送。</li></ul>|
|Web 應用程式的遠端偵錯應關閉|此動作會停用遠端偵錯。|
|函式應用程式的遠端偵錯應關閉|此動作會停用遠端偵錯。|
|應關閉 API 應用程式的遠端偵錯|此動作會停用遠端偵錯。|
|CORS 不應允許每項資源存取您的 Web 應用程式|此動作會封鎖其他網域存取您的 Web 應用程式。 若要允許特定網域，請在 [允許的原始來源] 欄位中輸入它們（以逗號分隔）。 <br>**注意**：將此欄位保留空白會封鎖所有的跨原始來源呼叫。 ' Param 欄位標題： ' 允許的來源 '|
|CORS 不應允許每項資源存取函式應用程式|此動作會封鎖其他網域存取您的函數應用程式。 若要允許特定網域，請在 [允許的原始來源] 欄位中輸入它們（以逗號分隔）。 <br>**注意**：將此欄位保留空白會封鎖所有的跨原始來源呼叫。 ' Param 欄位標題： ' 允許的來源 '|
|CORS 不應允許每項資源存取您的 API 應用程式|此動作會封鎖其他網域存取您的 API 應用程式。 若要允許特定網域，請在 [允許的原始來源] 欄位中輸入它們（以逗號分隔）。 <br>**注意**：將此欄位保留空白會封鎖所有的跨原始來源呼叫。 ' Param 欄位標題： ' 允許的來源 '|
|監視代理程式應在您的虛擬機器上啟用|此動作會在選取的虛擬機器上安裝監視代理程式。 選取要向其報告之代理程式的工作區。<ul><li>如果您的更新原則設定為 [自動]，則會部署在新的現有實例上。</li><li>如果您的更新原則設定為 [手動]，而您想要在現有的實例上安裝代理程式，請選取 [核取方塊] 選項。 [深入了解](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|應啟用 Key Vault 中的診斷記錄|此動作會在金鑰保存庫上啟用診斷記錄。 診斷記錄和計量會儲存在選取的工作區中。|
|應啟用服務匯流排中的診斷記錄|此動作會啟用服務匯流排上的診斷記錄。 診斷記錄和計量會儲存在選取的工作區中。|

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何修復資訊安全中心中的建議。 若要深入瞭解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心中設定安全性原則](tutorial-security-policy.md)-瞭解如何為您的 Azure 訂用帳戶和資源群組設定安全性原則。
* [Azure 資訊安全中心中的安全性健全狀況監視](security-center-monitoring.md)-瞭解如何監視 Azure 資源的健康情況。
