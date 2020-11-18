---
title: 具有計量、警示和資源健康狀態的診斷-Azure Standard Load Balancer
description: 使用可用的計量、警示和資源健康狀態資訊來診斷您的 Azure Standard Load Balancer。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 9c322620e1d66182937be41bb02d48fd1469f459
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697555"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>包含計量、警示和資源健康情況的 Standard Load Balancer 診斷

Azure Standard Load Balancer 會公開下列診斷功能：

* **多維度計量和警示**：透過適用于標準負載平衡器設定的 [Azure 監視器](../azure-monitor/overview.md) ，提供多維度的診斷功能。 您可以監視、管理標準負載平衡器資源，並對其進行疑難排解。

* **資源健康狀態**：您 Load Balancer 的資源健康狀態狀態可在 [監視] 底下的 [資源健康狀態] 頁面中取得。 這項自動檢查會通知您 Load Balancer 資源目前的可用性。

本文會簡要介紹這些功能，以及如何將這些功能使用於標準 Load Balancer。 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>多維度計量

Azure Load Balancer 透過 Azure 入口網站的 Azure 計量提供多維度計量，並協助您取得負載平衡器資源的即時診斷見解。 

各種標準 Load Balancer 組態提供下列計量：

| 計量 | 資源類型 | 描述 | 建議的彙總 |
| --- | --- | --- | --- |
| 資料路徑可用性 | 公用和內部負載平衡器 | 標準 Load Balancer 會在資料路徑上持續運用，從區域內到 Load Balancer 前端，再一路到支援 VM 的 SDN 堆疊。 只要狀況良好的執行個體持續存在，測量就會依循與您應用程式的負載平衡流量相同的路徑。 此外，也會驗證您客戶所使用的資料路徑。 此測量對您的應用程式來說是看不見的，也不會干擾到其他作業。| Average |
| 健康情況探查狀態 | 公用和內部負載平衡器 | 標準 Load Balancer 使用分散式健康情況探查服務，可根據您的組態設定監視應用程式端點的健康情況。 這個計量會提供負載平衡器集區中每個執行個體端點的彙總檢視，或各端點篩選過的檢視。 您可以看到 Load Balancer 藉由健康情況探查設定如何檢視應用程式的健康情況。 |  Average |
| SYN (同步) 封包 | 公用和內部負載平衡器 | 標準 Load Balancer 不會終止傳輸控制通訊協定 (TCP) 連線，也不會與 TCP 或 UDP 封包流程互動。 流程及其交握一律是在來源與 VM 執行個體之間進行。 若要針對您的 TCP 通訊協定案例進行進一步疑難排解，您可使用 SYN 封包計數器來了解已進行多少次 TCP 連線嘗試。 此計量會回報已收到的 TCP SYN 封包數。| Average |
| SNAT 連線 | 公用 Load Balancer |標準 Load Balancer 會回報偽裝為公用 IP 位址前端的輸出流程數目。 來源網路位址轉譯 (SNAT) 連接埠是可耗盡的資源。 此計量可以指出應用程式有多依賴 SNAT 來處理連出的起始流程。 系統會回報成功和失敗之連出 SNAT 流程的計數器，而且可用來對連出流程的健康情況進行疑難排解及了解。| Average |
| 配置的 SNAT 埠 | 公用 Load Balancer | Standard Load Balancer 報告每個後端實例配置的 SNAT 埠數目 | 平均。 |
| 使用的 SNAT 埠 | 公用 Load Balancer | Standard Load Balancer 報告每個後端實例使用的 SNAT 埠數目。 | Average | 
| 位元組計數器 |  公用和內部負載平衡器 | 標準 Load Balancer 會報告每個前端處理的資料。 您可能有注意到，位元組不會在後端實例間平均散發。 這是預期的情況，因為 Azure 的 Load Balancer 演算法是以流程為基礎 | Average |
| 封包計數器 |  公用和內部負載平衡器 | 標準 Load Balancer 會報告每個前端處理的封包。| Average |

  >[!NOTE]
  >當您透過 NVA 或防火牆 Syn 封包使用來自內部負載平衡器的流量，而位元組計數器和封包計數器計量無法使用時，將會顯示為零。 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>在 Azure 入口網站中檢視負載平衡器計量

Azure 入口網站會透過 [計量] 頁面公開負載平衡器計量，此頁面可在特定資源的負載平衡器資源頁面和 [Azure 監視器] 頁面上取得。 

若要檢視標準 Load Balancer 資源的計量：
1. 移至 [計量] 頁面，並執行下列其中一項：
   * 在負載平衡器資源頁面上，選取下拉式清單中的計量類型。
   * 在 Azure 監視器頁面上，選取負載平衡器資源。
2. 設定適當的度量匯總類型。
3. 選擇性設定必要的篩選和群組。
4. （選擇性）設定時間範圍和匯總。 依預設，時間會以 UTC 格式顯示。

  >[!NOTE] 
  >在解讀某些計量時，時間匯總很重要，因為每分鐘會將資料取樣一次。 如果 [時間匯總] 設定為 [5 分鐘]，而 [計量匯總類型總和] 用於 SNAT 配置之類的計量，則您的圖形會顯示配置的 SNAT 埠總數五倍。 

![Standard Load Balancer 的計量](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*圖： Standard Load Balancer 的資料路徑可用性度量*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>透過 API 以程式設計方式擷取多維度計量

如需可供擷取多維度計量定義和值的 API 指導方針，請參閱 [Azure 監視 REST API 逐步解說](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api)。 這些計量只能透過 [所有度量] 選項寫入至儲存體帳戶。 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>設定多維度計量的警示 ###

Azure Standard Load Balancer 支援可輕鬆設定多維度計量的警示。 設定特定計量的自訂閾值，以觸發具有不同嚴重性等級的警示，以提升 touchless 資源監視體驗。

設定警示：
1. 移至負載平衡器的警示子分頁
1. 建立新的警示規則
    1.  設定警示條件
    1.   (選擇性) 新增自動修復的動作群組
    1.  指派警示嚴重性、名稱和描述，以啟用直覺反應

  >[!NOTE]
  >警示條件設定視窗會顯示信號歷程記錄的時間序列。 有一個選項可依維度（例如後端 IP）來篩選此時間序列。 這會篩選時間序列圖形，而 **不** 是警示本身。 您無法設定特定後端 IP 位址的警示。

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>常見診斷案例與建議的檢視

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>資料路徑是否已啟動並可供我的 Load Balancer 前端使用？
<details><summary>展開</summary>

資料路徑可用性度量描述區域內資料路徑的健康情況，以及您 Vm 所在的計算主機。 此計量是 Azure 基礎結構的健康情況反映。 您可以使用此計量：
- 監視服務的外部可用性
- 挖掘更深入的資料，了解服務部署所在的平台是否狀況良好、您的客體 OS 或應用程式執行個體是否狀況良好。
- 找出事件是否與您的服務或基礎資料層相關。 請勿將此度量與健康情況探查狀態混淆 ( 「後端實例可用性」 ) 。

若要取得 Standard Load Balancer 資源的資料路徑可用性：
1. 確定已選取正確的負載平衡器資源。 
2. **在 [計量**] 下拉式清單中，選取 [**資料路徑可用性**]。 
3. 在 [彙總] 下拉式清單中，選取 [平均]。 
4. 此外，請將前端 IP 位址或前端埠上的篩選新增為具有必要前端 IP 位址或前端埠的維度，然後依選取的維度將其分組。

![VIP 探查](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*圖： Load Balancer 前端探查詳細資料*

計量會由作用中的頻內測量所產生。 區域內的探查服務會產生此測量的流量。 此服務會在您使用公用前端建立部署時啟動，並繼續執行到您移除前端為止。 

系統會定期產生符合部署之前端和規則的封包。 封包會在區域中從來源周遊到主機 (後端集區中 VM 的所在位置)。 負載平衡器基礎結構會執行相同的負載平衡和轉譯作業，如同對所有其他流量所做的一樣。 這個探查是在負載平衡端點的頻內。 在探查抵達計算主機 (後端集區中狀況良好 VM 的所在位置) 之後，計算主機會針對探查服務產生回應。 您的 VM 不會看到這個流量。

資料路徑可用性因為下列原因而失敗：
- 您的部署在後端集區中沒有剩餘的狀況良好 VM。 
- 已發生基礎結構中斷。

基於診斷用途，您可以使用 [資料路徑可用性度量以及健康情況探查狀態](#vipavailabilityandhealthprobes)。

在大部分的情況下，請使用 **平均** 彙總。
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>我的 Load Balancer 的後端實例會回應探查嗎？
<details>
  <summary>展開</summary>
「健康情況探查狀態」計量會描述應用程式部署的健康情況，這個部署是在您設定負載平衡器健康情況探查時由您所設定。 負載平衡器使用健康情況探查的狀態來判斷新流程要傳送到哪裡。 健康情況探查源自 Azure 基礎結構的位址，在 VM 的客體 OS 內可以看到。

若要取得 Standard Load Balancer 資源的健康情況探查狀態：
1. 選取 [**平均**] 匯總類型的 **健康情況探查狀態** 度量。 
2. 將篩選套用至所需的前端 IP 位址或埠 (或兩個) 。

健康情況探查會因為下列原因而失敗：
- 您可對未接聽、未回應或使用錯誤通訊協定的連接埠設定健康情況探查。 如果您的服務使用伺服器直接回傳 (DSR 或浮動 IP) 規則，請確定服務會接聽 NIC 之 IP 組態的 IP 位址，而且不只是接聽使用前端 IP 位址所設定的回送。
- 網路安全性群組、VM 的客體 OS 防火牆或應用程式層篩選不允許您的探查。

在大部分的情況下，請使用 **平均** 彙總。
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>如何查看我的輸出連線統計資料？ 
<details>
  <summary>展開</summary>
「SNAT 連線」計量會描述[輸出流程](./load-balancer-outbound-connections.md)的成功和失敗連線數量。

失敗連線數量大於零，表示 SNAT 連接埠耗盡。 您必須進一步調查，以判斷造成失敗的原因。 SNAT 連接埠耗盡的外在表現就是無法建立[輸出流程](./load-balancer-outbound-connections.md)。 請參閱輸出連線的文章，以了解案例和運作機制，以及了解如何減輕及設計以避免 SNAT 連接埠耗盡。 

若要取得 SNAT 連線統計資料：
1. 選取 [SNAT 連線] 計量類型，並選取 [總和] 作為彙總。 
2. 針對成功和失敗的 SNAT 連線計數 (以不同線條表示) 依 [連線狀態] 進行分組。 

![SNAT 連線](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*圖： Load Balancer SNAT 連線計數*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>如何? 檢查我的 SNAT 埠使用量和配置嗎？
<details>
  <summary>展開</summary>
使用的 SNAT 埠計量會追蹤耗用多少 SNAT 埠來維持輸出流程。 這表示在網際網路來源與後端 VM 或負載平衡器後方的虛擬機器擴展集之間建立了多少個唯一的流程，而且沒有公用 IP 位址。 藉由比較您所使用的 SNAT 埠數目與配置的 SNAT 埠計量，您可以判斷您的服務是否遇到，或有 SNAT 耗盡的風險，以及產生的輸出流程失敗。 

如果您的計量指出 [輸出流程](./load-balancer-outbound-connections.md) 失敗的風險，請參考該文章並採取步驟來減輕這個問題，以確保服務健康狀態。

若要查看 SNAT 埠的使用方式和配置：
1. 將圖形的時間匯總設定為1分鐘，以確保會顯示所需的資料。
1. 選取已 **使用的 Snat 埠** 及/或配置的 **snat 埠** 作為度量類型，並將 **平均** 配置為匯總
    * 根據預設，這些計量是每個後端 VM 或 VMSS 所配置或使用的 SNAT 埠平均數目，對應至對應至 Load Balancer 的所有前端公用 Ip （透過 TCP 和 UDP 進行匯總）。
    * 若要查看或配置給負載平衡器使用的 SNAT 埠總數，請使用計量匯總 **總和**
1. 篩選特定的 **通訊協定類型**、一組 **後端 ip** 和/或 **前端 ip**。
1. 若要監視每個後端或前端實例的健全狀況，請套用分割。 
    * 注意：分割只允許一次顯示單一度量。 
1. 例如，若要針對每部電腦監視 TCP 流量的 SNAT 使用量，請依 **平均** 匯總、依 **後端 ip** 分割，然後依 **通訊協定類型** 進行篩選。 

![SNAT 配置和使用方式](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*圖：一組後端 Vm 的平均 TCP SNAT 埠配置和使用量*

![後端實例的 SNAT 使用量](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*圖：每個後端實例的 TCP SNAT 埠使用量*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>如何查看服務的輸入/輸出連線嘗試？
<details>
  <summary>展開</summary>
「SYN 封包」計量會描述 TCP SYN 封包的數量，涵括與特定前端相關聯的已抵達或已傳送 (針對[輸出流程](./load-balancer-outbound-connections.md)) 封包。 您可以使用此計量來了解服務的 TCP 連線嘗試。

在大部分的情況下，請使用 **總計** 彙總。

![SYN 連線](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*圖： Load Balancer SYN 計數*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>如何查看我的網路頻寬耗用？ 
<details>
  <summary>展開</summary>
「位元組和封包計數器」計量會描述您的服務在每個前端上所傳送或接收的位元組和封包數量。

在大部分的情況下，請使用 **總計** 彙總。

若要取得位元組或封包計數統計資料：
1. 選取 [位元組計數] 和/或 [封包計數] 計量類型，並選取 [平均] 作為彙總。 
2. 請執行下列其中一個動作：
   * 對特定前端 IP、前端連接埠或後端 IP 或後端連接埠套用篩選器。
   * 取得負載平衡器資源的整體統計資料 (不使用任何篩選)。

![位元組計數](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*圖： Load Balancer 的位元組計數*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>我要如何診斷我的負載平衡器部署？
<details>
  <summary>展開</summary>
藉由在單一圖表上使用資料路徑可用性和健康情況探查狀態計量的組合，您可以找出問題所在的位置，並解決問題。 您可以確定 Azure 正常運作，並利用此知識確定地判斷設定或應用程式是根本原因。

您可以透過健康情況探查計量來了解 Azure 依據您所提供的設定如何看待部署的健康情況。 查看健康情況探查永遠是監視或判斷原因時最好的第一步驟。

您可以更進一步地使用資料路徑可用性度量，以深入瞭解 Azure 如何查看負責您特定部署的基礎資料平面健康情況。 當您合併這兩個計量時，就能找出錯誤可能的位置，如此圖中範例所示：

![結合資料路徑可用性和健康情況探查狀態計量](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*圖：結合資料路徑可用性和健康情況探查狀態計量*

此圖表會顯示以下資訊：
- 裝載 Vm 的基礎結構無法使用，而且在圖表的開頭為0%。 之後，基礎結構的狀況良好，且 Vm 可連線，而且後端中有一個以上的 VM。 這項資訊是由適用于資料路徑可用性的藍色追蹤所表示，之後是100%。 
- 健康情況探查狀態（以紫色追蹤表示）在圖表的開頭為0%。 綠色的圓形區域會反白顯示健康情況探查狀態變成狀況良好的位置，以及客戶的部署可以接受新流程的位置。

此圖表讓客戶可以自行針對部署進行疑難排解，不必猜測或要求支援找出是否發生其他問題。 由於設定不正確或應用程式失敗導致健康情況探查失敗，所以服務變得無法使用。
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>資源健康情況狀態

標準 Load Balancer 資源的健康情況狀態會透過 [監視器] > [服務健康狀態] 底下現有的 [資源健康狀態] 公開。

若要檢視公用標準 Load Balancer 資源的健康情況：
1. 選取 [**監視**  >  **服務健康** 情況]。

   ![監視器頁面](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   圖：Azure 監視器上的服務健康情況連結

2. 選取 [資源健康狀態]，然後確定已選取 [訂用帳戶識別碼] 以及 [資源類型 = 負載平衡器]。

   ![資源健康情況狀態](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   圖：選取資源以檢視健康情況

3. 在清單中，選取 Load Balancer 資源，以檢視其過去的健康情況狀態。

    ![Load Balancer 健康情況狀態](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   圖：Load Balancer 資源的健康情況檢視
 
一般資源健康狀態原因可在 [RHC 檔](../service-health/resource-health-overview.md)中取得。 下表列出 Azure Load Balancer 的特定狀態： 

| 資源健康情況狀態 | 描述 |
| --- | --- |
| 可用 | 您的標準負載平衡器資源狀況良好且可供使用。 |
| 已降級 | 您的標準負載平衡器有影響效能的平臺或使用者起始事件。 資料路徑可用性計量回報低於90%，但至少有兩分鐘的健康狀態超過25%。 您將體驗到嚴重的效能影響。 [遵循疑難排解資料路徑可用性指南]，判斷是否有使用者起始的事件造成影響您的可用性。
| [無法使用] | 您的標準負載平衡器資源狀況不良。 資料路徑可用性計量回報的健康情況低於至少兩分鐘的25% 健全狀況。 您將會遇到對輸入連線能力造成顯著的效能影響或缺乏可用性。 可能是使用者或平臺事件造成無法使用的情形。 [遵循疑難排解資料路徑可用性指南]，判斷是否有使用者起始的事件影響您的可用性。 |
| 未知 | 標準負載平衡器資源的資源健康狀態尚未更新，或尚未收到過去10分鐘的資料路徑可用性資訊。 此狀態應該是暫時性的，而且會在收到資料後立即反映正確的狀態。 |

## <a name="next-steps"></a>後續步驟

- 深入了解[標準負載平衡器](./load-balancer-overview.md)。
- 深入了解 [Load Balancer 輸出連線能力](./load-balancer-outbound-connections.md)。
- 了解 [Azure 監視器](../azure-monitor/overview.md)。
- 了解 [Azure 監視器 REST API](/rest/api/monitor/) 和[如何透過 REST API 擷取計量](/rest/api/monitor/metrics/list)。