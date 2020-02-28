---
title: 針對適用于 Windows 的 Log Analytics 代理程式問題進行疑難排解
description: 描述 Azure 監視器中適用于 Windows 的 Log Analytics 代理程式最常見問題的徵兆、原因和解決方式。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 78625707bfa296eeb7ad8cc658657f46da1dc495
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668786"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何針對適用于 Windows 的 Log Analytics 代理程式問題進行疑難排解 

本文提供在 Azure 監視器中使用適用于 Windows 的 Log Analytics 代理程式時可能遇到之錯誤的疑難排解，並建議可能解決這些問題的解決方案。

如果這些步驟對您都沒有幫助，還有下列支援管道可供使用：

* 具有頂級支援權益的客戶可以透過[頂級支援](https://premier.microsoft.com/)開啟支援要求。
* 具有 Azure 支援合約的客戶可以在 [Azure 入口網站](https://manage.windowsazure.com/?getsupport=true)開啟支援要求。
* 造訪「Log Analytics 意見反應」頁面，以檢閱已提交的構想和錯誤[https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)，或提出新的構想和錯誤。 

## <a name="important-troubleshooting-sources"></a>重要疑難排解來源

 為了協助疑難排解與適用于 Windows 的 Log Analytics 代理程式相關的問題，代理程式會將事件記錄到 Windows 事件記錄檔，特別是在*Application 和 Services\Operations Manager*下。  

## <a name="connectivity-issues"></a>連線能力問題

如果代理程式是透過 proxy 伺服器或防火牆進行通訊，可能會有限制，防止來源電腦和 Azure 監視器服務的通訊。 萬一通訊遭到封鎖，因為設定錯誤，在嘗試安裝代理程式或將代理程式設定為回報給其他工作區時，工作區的註冊可能會失敗。 成功註冊後，代理程式通訊可能會失敗。 本節說明使用 Windows 代理程式對這類問題進行疑難排解的方法。

再次檢查防火牆或 proxy 是否已設定為允許下列的埠和 Url，如下表所述。 此外，請確認未針對 web 流量啟用 HTTP 檢查，因為它可以防止代理程式和 Azure 監視器之間的安全 TLS 通道。  

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  

如需 Azure Government 所需的防火牆資訊，請參閱[Azure Government 管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 如果您打算使用 Azure 自動化混合式 Runbook 背景工作角色來連線至自動化服務並向其註冊，以便在您的環境中使用 runbook 或管理解決方案，它必須能夠存取[設定混合式 Runbook 背景工作角色的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的埠號碼和 url。 

有數種方式可供您確認代理程式是否成功與 Azure 監視器通訊。

- 在工作區中啟用[Azure Log Analytics 代理程式健全狀況評](../insights/solution-agenthealth.md)量。 從 [代理程式健全狀況] 儀表板中，查看 [**沒有回應的代理程式計數**] 資料行，以快速查看是否已列出代理程式。  

- 執行下列查詢，以確認代理程式正在傳送心跳至其設定報告的工作區。 將 `<ComputerName>` 取代為電腦的實際名稱。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果電腦已成功與服務通訊，則查詢應該會傳回結果。 如果查詢未傳回結果，請先確認代理程式已設定為向正確的工作區報告。 如果設定正確，請繼續進行步驟3並搜尋 Windows 事件記錄檔，以找出代理程式是否正在記錄哪個問題可能導致它無法與 Azure 監視器進行通訊。

- 另一個識別連接問題的方法是執行**TestCloudConnectivity**工具。 此工具預設會安裝在 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*資料夾中的代理程式。 從提高許可權的命令提示字元中，流覽至資料夾，然後執行工具。 此工具會傳回結果，並反白顯示測試失敗的位置（例如，如果它與已封鎖的特定埠/URL 相關）。 

    ![TestCloudConnection 工具執行結果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 依**事件來源**篩選*Operations Manager*事件記錄檔 - *健全狀況服務模組*、 *HealthService* 和 *服務連接器*，並依**事件層級***警告*和*錯誤*篩選，以確認它是否已寫入下表中的事件。 如果是，請檢查每個可能事件所包含的解決步驟。

    |事件識別碼 |來源 |描述 |解決方案 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |健全狀況服務 |從代理程式到服務的連接失敗 |當代理程式無法直接或透過防火牆/proxy 伺服器與 Azure 監視器服務通訊時，就會發生此錯誤。 確認代理程式 proxy 設定，或網路防火牆/proxy 允許從電腦到服務的 TCP 流量。|
    |2138 |健全狀況服務模組 |Proxy 需要驗證 |設定代理程式 proxy 設定，並指定用來向 proxy 伺服器進行驗證所需的使用者名稱/密碼。 |
    |2129 |健全狀況服務模組 |失敗的連線/SSL 協調失敗 |檢查您的網路介面卡 TCP/IP 設定和代理程式 proxy 設定。|
    |2127 |健全狀況服務模組 |傳送資料失敗時發生錯誤代碼 |如果只在一天內定期發生，它可能只是可以忽略的隨機異常。 監視以瞭解其發生頻率。 如果經常發生這種情況，請先檢查您的網路設定和 proxy 設定。 如果描述包含 HTTP 錯誤碼404，而且是第一次代理程式嘗試將資料傳送至服務，則會包含具有內部404錯誤碼的500錯誤。 404表示找不到，這表示新工作區的儲存區域仍在布建中。 下次重試時，資料將會如預期般成功寫入工作區。 HTTP 錯誤403可能表示許可權或認證問題。 403錯誤包含更多的資訊，以協助對問題進行疑難排解。|
    |4000 |服務連接器 |DNS 名稱解析失敗 |電腦無法解析將資料傳送至服務時所使用的網際網路位址。 這可能是您電腦上的 DNS 解析程式設定、不正確的 proxy 設定，或可能是提供者的暫時性 DNS 問題。 如果定期發生，可能是因為暫時性的網路相關問題所造成。|
    |4001 |服務連接器 |連接至服務失敗。 |當代理程式無法直接或透過防火牆/proxy 伺服器與 Azure 監視器服務通訊時，就會發生此錯誤。 確認代理程式 proxy 設定，或網路防火牆/proxy 允許從電腦到服務的 TCP 流量。|
    |4002 |服務連接器 |服務傳回 HTTP 狀態碼403以回應查詢。 請洽詢服務系統管理員以取得服務的健全狀況。 稍後將會重試查詢。 |此錯誤是在代理程式的初始註冊階段所撰寫，您會看到類似下列的 URL： *HTTPs://\<workspaceID >. opinsights. azure .com/agentservice.svc .svc/AgentTopologyRequest*。 錯誤碼403表示禁止，而且可能是因為輸入錯誤的工作區識別碼或金鑰，或是電腦上的資料和時間不正確所造成。 如果時間為自目前時間起的 + /-15 分鐘，則上架失敗。 若要修正此錯誤，請更新 Windows 電腦的日期和/或時區。|

## <a name="data-collection-issues"></a>資料收集問題

安裝代理程式並將其報告至其設定的工作區或工作區之後，它可能會停止接收設定、收集或轉送效能、記錄或其他資料至服務，視電腦的啟用和目標而定。 您必須判斷是否：

- 它是否為特定的資料類型或工作區中未提供的所有資料？
- 解決方案所指定的資料類型，或指定為工作空間資料收集設定的一部分？
- 有多少部電腦受到影響？ 這是向工作區報告的單一或多部電腦嗎？
- 它是否正常運作，是否在一天的特定時間停止，或從未收集過？ 
- 您使用的記錄搜尋查詢語法是否正確？ 
- 代理程式是否已從 Azure 監視器收到其設定？

進行疑難排解的第一個步驟是判斷電腦是否正在傳送「心跳」事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查詢傳回結果，則您需要判斷是否不會收集特定資料類型，並將其轉送至服務。 這可能是因為代理程式未收到來自服務的更新設定，或其他導致代理程式無法正常運作的徵兆所致。 請執行下列步驟以進一步進行疑難排解。

1. 在電腦上開啟提升許可權的命令提示字元，然後輸入 `net stop healthservice && net start healthservice`來重新開機 agent 服務。
2. 開啟*Operations Manager*事件記錄檔，並從 [**事件來源** *HealthService*] 搜尋**事件識別碼** *7023、7024、7025、7028*和*1210* 。  這些事件表示代理程式已成功接收來自 Azure 監視器的設定，且正在主動監視電腦。 事件識別碼1210的事件描述也會指定代理程式上監視範圍內所包含的所有解決方案和深入解析的最後一行。  

    ![事件識別碼1210描述](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 在幾分鐘之後，如果您在查詢結果或視覺效果中看不到預期的資料，請根據您是從 [ *Operations Manager* ] 事件記錄檔中查看資料，搜尋 [**事件來源**] *HealthService*並*健全狀況服務模組*，並依**事件層級***警告*和*錯誤*進行篩選，確認它是否已寫入下表中的事件。

    |事件識別碼 |來源 |描述 |解決方案 |
    |---------|-------|------------|
    |8000 |HealthService |這個事件會指定與所收集的效能、事件或其他資料類型相關的工作流程是否無法轉寄至服務，以供內嵌至工作區。 | 來源 HealthService 的事件識別碼2136會與此事件一併撰寫，而且可能會指出代理程式無法與服務通訊，可能是因為 proxy 和驗證設定不正確、網路中斷，或網路防火牆/proxy 不允許從電腦到服務的 TCP 流量。| 
    |10102和10103 |健全狀況服務模組 |工作流程無法解析資料來源。 |如果指定的效能計數器或實例不存在於電腦上，或工作區的資料設定不正確地定義，就可能發生這種情況。 如果這是使用者指定的[效能計數器](data-sources-performance-counters.md#configuring-performance-counters)，請確認指定的資訊是否遵循正確的格式，並存在於目的電腦上。 |
    |26002 |健全狀況服務模組 |工作流程無法解析資料來源。 |如果指定的 Windows 事件記錄檔不存在於電腦上，就會發生這種情況。 如果電腦不應該註冊此事件記錄檔，就可以放心忽略此錯誤，否則如果這是使用者指定的[事件記錄](data-sources-windows-events.md#configuring-windows-event-logs)檔，請確認指定的資訊是否正確。 |

