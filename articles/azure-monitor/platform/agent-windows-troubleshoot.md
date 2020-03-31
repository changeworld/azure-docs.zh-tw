---
title: 解決 Windows 日誌分析代理的問題
description: 使用 Azure 監視器中 Windows 的日誌分析代理描述最常見的問題的症狀、原因和解決方法。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333511"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何針對 Log Analytics Windows 代理程式的問題進行疑難排解 

本文提供了使用 Azure 監視器中 Windows 的日誌分析代理解決錯誤可能遇到的説明，並推薦瞭解決這些問題的可能解決方案。

如果這些步驟對您都沒有幫助，還有下列支援管道可供使用：

* 具有頂級支援權益的客戶可以透過[頂級支援](https://premier.microsoft.com/)開啟支援要求。
* 具有 Azure 支援合約的客戶可以在 [Azure 入口網站](https://manage.windowsazure.com/?getsupport=true)開啟支援要求。
* 訪問日誌分析回饋頁面，查看已提交的想法和 Bug[https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)或提交新的想法和錯誤。 

## <a name="important-troubleshooting-sources"></a>重要的故障排除源

 為了協助解決與 Windows 的日誌分析代理相關的問題，代理將事件記錄到 Windows 事件日誌，特別是在*應用程式和服務_操作管理器*下。  

## <a name="connectivity-issues"></a>連線能力問題

如果代理通過代理伺服器或防火牆進行通信，則可能存在阻止源電腦和 Azure 監視器服務通信的限制。 如果通信被阻止，則由於配置錯誤，在嘗試安裝代理或將代理設置後配置為向其他工作區報告時，工作區的註冊可能會失敗。 代理通信在成功註冊後可能會失敗。 本節介紹解決 Windows 代理此類問題的方法。

仔細檢查防火牆或代理是否配置為允許下表中描述的以下埠和 URL。 還要確認未為 Web 流量啟用 HTTP 檢查，因為它可以防止代理和 Azure 監視器之間出現安全的 TLS 通道。  

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  

有關 Azure 政府所需的防火牆資訊，請參閱[Azure 政府管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 如果計畫使用 Azure 自動化混合 Runbook 輔助角色連接到自動化服務並在自動化服務中註冊以在環境中使用 Runbook 或管理解決方案，則必須有權訪問埠號和在[為混合 Runbook 工作執行緒配置網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中描述的 URL。 

有幾種方法可以驗證代理是否成功與 Azure 監視器通信。

- 在工作區中啟用[Azure 日誌分析代理運行狀況評估](../insights/solution-agenthealth.md)。 在"代理運行狀況"儀表板中，查看**無回應代理計數**列，以快速查看是否列出了代理。  

- 運行以下查詢以確認代理正在向配置為報告的工作區發送活動訊號。 替換為`<ComputerName>`機器的實際名稱。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果電腦成功與服務通信，則查詢應返回結果。 如果查詢未返回結果，請先驗證代理配置為向正確的工作區報告。 如果配置正確，請繼續執行步驟 3 並搜索 Windows 事件日誌，以確定代理是否正在記錄可能阻止它與 Azure 監視器通信的問題。

- 識別連接問題的另一種方法是運行**TestCloud 連接**工具。 預設情況下，該工具與資料夾中的代理一起安裝 *%SystemRoot%\程式檔\Microsoft 監視代理\代理*。 從提升的命令提示符導航到資料夾並運行該工具。 該工具返回測試結果並突出顯示測試失敗的位置（例如，如果它與被阻止的特定埠/URL 相關）。 

    ![測試雲連接工具執行結果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 按**事件源** - *"運行狀況服務"、**運行狀況服務**和服務連接器*篩選*操作管理器*事件日誌，並按**事件級別***警告*和*錯誤*進行篩選，以確認其是否從下表中寫入了事件。 如果是，請查看每個可能事件中包含的解決方法步驟。

    |事件識別碼 |來源 |描述 |解決方案 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |健全狀況服務 |從代理連接到服務失敗 |當代理無法直接或通過防火牆/代理伺服器與 Azure 監視器服務通信時，可能會發生此錯誤。 驗證代理代理設置或網路防火牆/代理是否允許 TCP 流量從電腦到服務。|
    |2138 |健全狀況服務模組 |Proxy 需要驗證 |配置代理代理設置並指定與代理伺服器進行身份驗證所需的使用者名/密碼。 |
    |2129 |健全狀況服務模組 |連接失敗/TLS 協商失敗 |檢查網路介面卡 TCP/IP 設置和代理代理設置。|
    |2127 |健全狀況服務模組 |發送收到的資料錯誤代碼失敗 |如果它只在白天定期發生，則可能只是一個隨機異常，可以忽略。 監控以瞭解發生的頻率。 如果這種情況在一天中頻繁發生，請先檢查您的網路設定和代理設置。 如果說明包含 HTTP 錯誤代碼 404，並且這是代理首次嘗試將資料發送到服務，則它將包含包含包含內部 404 錯誤代碼的 500 錯誤。 404 表示找不到，表示新工作區的存儲區域仍在預配中。 在下一次重試時，資料將按預期成功寫入工作區。 HTTP 錯誤 403 可能表示許可權或憑據問題。 403 錯誤包含更多資訊，可説明解決問題。|
    |4000 |服務連接器 |DNS 名稱解析失敗 |電腦無法解析向服務發送資料時使用的 Internet 位址。 這可能是電腦上的 DNS 解析程式設置、不正確的代理設置，或者可能是提供程式的臨時 DNS 問題。 如果定期發生，則可能是由暫時性網路相關問題引起的。|
    |4001 |服務連接器 |與服務的連接失敗。 |當代理無法直接或通過防火牆/代理伺服器與 Azure 監視器服務通信時，可能會發生此錯誤。 驗證代理代理設置或網路防火牆/代理是否允許 TCP 流量從電腦到服務。|
    |4002 |服務連接器 |服務傳回 HTTP 狀態碼 403 以回應查詢。 請與服務管理員聯繫，瞭解服務的運行狀況。 稍後將重試查詢。 |此錯誤是在代理的初始註冊階段編寫的，您將看到類似于以下內容的*URL：HTTPs://\<工作區 ID>.oms.opinsights.azure.com/AgentServiceService.svc/AgentTost 請求*。 錯誤代碼 403 表示禁止，並且可能由鍵入錯誤的工作區 ID 或金鑰引起，或者電腦上的資料和時間不正確。 如果時間為自目前時間起的 + /-15 分鐘，則上架失敗。 要更正此問題，請更新 Windows 電腦的日期和/或時區。|

## <a name="data-collection-issues"></a>資料收集問題

安裝代理並報告其配置的工作區或工作區後，它可能會停止接收配置、收集或轉發性能、日誌或其他資料到服務，具體取決於已啟用的內容和電腦的目標。 有必要確定：

- 是特定資料類型還是工作區中不可用的所有資料？
- 資料類型是否由解決方案指定或指定為工作區資料收集配置的一部分？
- 有多少台電腦受到影響？ 是向工作區報告的單個電腦還是多台電腦？
- 它是否工作，在一天的特定時間停止，或從未收集過？ 
- 您使用的日誌搜索查詢在語法上是否正確？ 
- 代理從 Azure 監視器收到其配置嗎？

故障排除的第一步是確定電腦是否正在發送活動訊號事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查詢返回結果，則需要確定是否未收集特定資料類型並將其轉發到服務。 這可能是由於代理未從服務接收更新的配置，或者某些其他症狀阻止代理正常運行造成的。 執行以下步驟以進一步疑難排解。

1. 在電腦上打開一個提升的命令提示符，並通過鍵入`net stop healthservice && net start healthservice`重新開機代理服務。
2. 打開*操作管理器*事件日誌，並從**事件源***HealthService*搜索事件*I7023、7024、7025、7028*和*1210。* **event IDs**  這些事件表示代理已成功從 Azure 監視器接收配置，並且它們正在主動監視電腦。 事件 ID 1210 的事件描述還將在最後一行上指定包含在代理監視範圍內的所有解決方案和見解。  

    ![事件 ID 1210 說明](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 如果幾分鐘後在查詢結果或視覺化中看不到預期資料，具體取決於是否從操作*管理器*事件日誌中查看來自解決方案或 Insight 的資料，請搜索**事件源***運行狀況服務和**運行狀況服務模組*，然後按**事件級別***警告*和*錯誤*進行篩選，以確認其是否從下表中寫入了事件。

    |事件識別碼 |來源 |描述 |解決方案 |
    |---------|-------|------------|
    |8000 |HealthService |此事件將指定與收集的性能、事件或其他資料類型相關的工作流是否無法轉發到服務以引入工作區。 | 源 HealthService 的事件 ID 2136 與此事件一起編寫，可以指示代理無法與服務通信，這可能是由於代理和身份驗證設置配置錯誤、網路中斷或網路防火牆/代理不允許 TCP 流量從電腦到服務。| 
    |10102 和 10103 |健全狀況服務模組 |工作流無法解決資料來源。 |如果指定的效能計數器或實例在電腦上不存在或在工作區資料設置中定義不正確，則可能發生此情況。 如果這是使用者指定的[效能計數器](data-sources-performance-counters.md#configuring-performance-counters)，請驗證指定的資訊遵循正確的格式並在目的電腦上存在。 |
    |26002 |健全狀況服務模組 |工作流無法解決資料來源。 |如果電腦上不存在指定的 Windows 事件日誌，則可能發生此情況。 如果電腦不應註冊此事件日誌，則可以安全地忽略此錯誤，否則，如果這是使用者指定的[事件日誌](data-sources-windows-events.md#configuring-windows-event-logs)，請驗證指定的資訊是否正確。 |

