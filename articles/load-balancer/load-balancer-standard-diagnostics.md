---
title: 引入指標、警示及資源執行狀況的診斷 - Azure 標準負載均衡器
description: 使用可用的指標、警報和資源運行狀況資訊來診斷 Azure 標準負載均衡器。
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
ms.openlocfilehash: 951f24ad06014f6d95f10c91e1bad8e99bbbc736
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991768"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>包含計量、警示和資源健康情況的 Standard Load Balancer 診斷

Azure 標準負載均衡器公開以下診斷功能:

* **多維指標和警報**:通過[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)為標準負載均衡器配置提供多維診斷功能。 您可以監視、管理和排除標準負載均衡器資源。

* **資源運行狀況**:Azure 門戶中的"負載均衡器"頁和"資源運行狀況"頁(在監視器下)公開了標準負載均衡器的資源運行狀況部分。 

本文會簡要介紹這些功能，以及如何將這些功能使用於標準 Load Balancer。 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>多維指標

Azure 負載均衡器通過 Azure 門戶中的 Azure 指標提供多維指標,它可以説明您獲得對負載均衡器資源的即時診斷見解。 

各種標準 Load Balancer 組態提供下列計量：

| 計量 | 資源類型 | 描述 | 建議的彙總 |
| --- | --- | --- | --- |
| 資料路徑可用性(VIP 可用性)| 公用和內部負載平衡器 | 標準 Load Balancer 會在資料路徑上持續運用，從區域內到 Load Balancer 前端，再一路到支援 VM 的 SDN 堆疊。 只要狀況良好的執行個體持續存在，測量就會依循與您應用程式的負載平衡流量相同的路徑。 此外，也會驗證您客戶所使用的資料路徑。 此測量對您的應用程式來說是看不見的，也不會干擾到其他作業。| Average |
| 執行狀況偵測狀態(DIP 可用性) | 公用和內部負載平衡器 | 標準 Load Balancer 使用分散式健康情況探查服務，可根據您的組態設定監視應用程式端點的健康情況。 這個計量會提供負載平衡器集區中每個執行個體端點的彙總檢視，或各端點篩選過的檢視。 您可以看到 Load Balancer 藉由健康情況探查設定如何檢視應用程式的健康情況。 |  Average |
| SYN (同步) 封包 | 公用和內部負載平衡器 | 標準 Load Balancer 不會終止傳輸控制通訊協定 (TCP) 連線，也不會與 TCP 或 UDP 封包流程互動。 流程及其交握一律是在來源與 VM 執行個體之間進行。 若要針對您的 TCP 通訊協定案例進行進一步疑難排解，您可使用 SYN 封包計數器來了解已進行多少次 TCP 連線嘗試。 此計量會回報已收到的 TCP SYN 封包數。| Average |
| SNAT 連線 | 公用 Load Balancer |標準 Load Balancer 會回報偽裝為公用 IP 位址前端的輸出流程數目。 來源網路位址轉譯 (SNAT) 連接埠是可耗盡的資源。 此計量可以指出應用程式有多依賴 SNAT 來處理連出的起始流程。 系統會回報成功和失敗之連出 SNAT 流程的計數器，而且可用來對連出流程的健康情況進行疑難排解及了解。| Average |
| 已配置的 SNAT 連接埠 | 公用 Load Balancer | 標準負載均衡器報告每個後端實體配置的 SNAT 連接埠 | 平均。 |
| 已使用的 SNAT 連接埠 | 公用 Load Balancer | 標準負載均衡器報告每個後端實例使用的 SNAT 連接埠數。 | Average | 
| 位元組計數器 |  公用和內部負載平衡器 | 標準 Load Balancer 會報告每個前端處理的資料。 您可能會注意到位元組在後端實例之間分佈不均。 這是預料之中的,因為 Azure 的負載均衡器演演算法基於流 | Average |
| 封包計數器 |  公用和內部負載平衡器 | 標準 Load Balancer 會報告每個前端處理的封包。| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>在 Azure 入口網站中檢視負載平衡器計量

Azure 門戶通過"指標"頁公開負載均衡器指標,該頁在特定資源的負載均衡器資源頁上和 Azure 監視器頁上都可用。 

若要檢視標準 Load Balancer 資源的計量：
1. 轉到「指標」頁,執行以下任一操作:
   * 在負載平衡器資源頁面上，選取下拉式清單中的計量類型。
   * 在 Azure 監視器頁面上，選取負載平衡器資源。
2. 設置適當的指標聚合類型。
3. 選擇性設定必要的篩選和群組。
4. 可以選擇配置時間範圍和聚合。 默認情況下,時間以 UTC 顯示。

  >[!NOTE] 
  >當將某些指標解釋為數據每分鐘採樣一次時,時間聚合非常重要。 如果時間聚合設置為五分鐘,並且指標聚合類型 Sum 用於 SNAT 分配等指標,則圖形將顯示總分配的 SNAT 連接埠的五倍。 

![標準負載均衡器的指標](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*圖:標準負載均衡器的資料路徑可用性指標*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>透過 API 以程式設計方式擷取多維度計量

如需可供擷取多維度計量定義和值的 API 指導方針，請參閱 [Azure 監視 REST API 逐步解說](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)。 這些指標只能通過"所有指標"選項寫入存儲帳戶。 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>為多維指標設定警示 ###

Azure 標準負載均衡器支援多維指標的易於配置的警報。 為特定指標配置自定義閾值以觸發嚴重程度不同的警報,從而提供無接觸的資源監視體驗。

設定警示：
1. 移至負載均衡器的警報子刀片
1. 建立新的警示規則
    1.  設定警示條件
    1.  ( 選擇性的 )新增自動修復操作群組
    1.  分配警報嚴重性、名稱和描述,以便做出直觀的反應

  >[!NOTE]
  >警示條件設定視窗將顯示訊號歷史記錄的時間序列。 可以選擇按後端 IP 等維度篩選此時間序列。 這將篩選時間序列圖,**但不會**篩選警報本身。 您不能為特定的後端 IP 位址設定警報。

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>常見診斷案例與建議的檢視

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>資料路徑已經啟用，且可以讓我的負載平衡器 VIP 使用嗎？
<details><summary>展開</summary>

「VIP 可用性」計量會描述區域內資料路徑 (此路徑通往 VM 所在的計算主機) 的健康情況。 此計量是 Azure 基礎結構的健康情況反映。 您可以使用此計量：
- 監視服務的外部可用性
- 挖掘更深入的資料，了解服務部署所在的平台是否狀況良好、您的客體 OS 或應用程式執行個體是否狀況良好。
- 找出事件是否與您的服務或基礎資料層相關。 請勿將此計量與健康情況探查狀態 (「DIP 可用性」) 搞混。

要取得標準負載均衡器資源的資料路徑可用性,請:
1. 確定已選取正確的負載平衡器資源。 
2. 在 **「指標**」 下拉清單中,選擇**資料路徑可用性**。 
3. 在 [彙總]**** 下拉式清單中，選取 [平均]****。 
4. 此外,在前端 IP 位址或前埠上添加篩選器作為具有所需前端 IP 位址或前端埠的維度,然後按所選維度對其進行分組。

![VIP 探查](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*圖:負載均衡器前端探測詳細資訊*

計量會由作用中的頻內測量所產生。 區域內的探查服務會產生此測量的流量。 此服務會在您使用公用前端建立部署時啟動，並繼續執行到您移除前端為止。 

系統會定期產生符合部署之前端和規則的封包。 封包會在區域中從來源周遊到主機 (後端集區中 VM 的所在位置)。 負載平衡器基礎結構會執行相同的負載平衡和轉譯作業，如同對所有其他流量所做的一樣。 這個探查是在負載平衡端點的頻內。 在探查抵達計算主機 (後端集區中狀況良好 VM 的所在位置) 之後，計算主機會針對探查服務產生回應。 您的 VM 不會看到這個流量。

VIP 可用性會因為下列原因而失敗：
- 您的部署在後端集區中沒有剩餘的狀況良好 VM。 
- 已發生基礎結構中斷。

出於診斷目的,您可以將[資料路徑可用性指標與執行狀況偵測狀態一起使用](#vipavailabilityandhealthprobes)。

在大部分的情況下，請使用**平均**彙總。
</details>

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>VIP 的後端執行個體會回應探查嗎？
<details>
  <summary>展開</summary>
「健康情況探查狀態」計量會描述應用程式部署的健康情況，這個部署是在您設定負載平衡器健康情況探查時由您所設定。 負載平衡器使用健康情況探查的狀態來判斷新流程要傳送到哪裡。 健康情況探查源自 Azure 基礎結構的位址，在 VM 的客體 OS 內可以看到。

要獲取標準負載均衡器資源的運行狀況探測狀態,請進行以下檢查:
1. 選擇具有**平均**聚合類型的**運行狀況探測狀態**指標。 
2. 在所需的前端 IP 位址或埠(或兩者)上應用篩選器。

健康情況探查會因為下列原因而失敗：
- 您可對未接聽、未回應或使用錯誤通訊協定的連接埠設定健康情況探查。 如果您的服務使用伺服器直接回傳 (DSR 或浮動 IP) 規則，請確定服務會接聽 NIC 之 IP 組態的 IP 位址，而且不只是接聽使用前端 IP 位址所設定的回送。
- 網路安全性群組、VM 的客體 OS 防火牆或應用程式層篩選不允許您的探查。

在大部分的情況下，請使用**平均**彙總。
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>如何查看我的輸出連線統計資料？ 
<details>
  <summary>展開</summary>
「SNAT 連線」計量會描述[輸出流程](https://aka.ms/lboutbound)的成功和失敗連線數量。

失敗連線數量大於零，表示 SNAT 連接埠耗盡。 您必須進一步調查，以判斷造成失敗的原因。 SNAT 連接埠耗盡的外在表現就是無法建立[輸出流程](https://aka.ms/lboutbound)。 請參閱輸出連線的文章，以了解案例和運作機制，以及了解如何減輕及設計以避免 SNAT 連接埠耗盡。 

若要取得 SNAT 連線統計資料：
1. 選取 [SNAT 連線]**** 計量類型，並選取 [總和]**** 作為彙總。 
2. 針對成功和失敗的 SNAT 連線計數 (以不同線條表示) 依 [連線狀態]**** 進行分組。 

![SNAT 連線](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*圖:負載均衡器 SNAT 連接計數*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>如何檢查我的 SNAT 連接埠使用方式和分配?
<details>
  <summary>展開</summary>
SNAT 使用情況指標指示 Internet 源與後端 VM 或虛擬機器規模集之間建立的唯一流數,該流位於負載均衡器後面且沒有公共 IP 位址。 通過與 SNAT 分配指標進行比較,可以確定服務是否遇到或面臨 SNAT 耗盡和導致出站流故障的風險。 

如果您的指標指示[出站流](https://aka.ms/lboutbound)失敗的風險,請參閱本文並採取措施來緩解這種情況,以確保服務運行狀況。

要檢視 SNAT 連接埠使用方式與分配:
1. 將圖形的時間聚合設置為 1 分鐘,以確保顯示所需的數據。
1. 選擇**SNAT 使用情況**與/或**SNAT 分配**為指標類型和**平均值**作為聚合
    * 默認情況下,這是分配給每個後端 VM 或 VMSS 的平均 SNAT 連接埠數,對應於映射到負載均衡器的所有前端公共 IP,這些埠透過 TCP 和 UDP 聚合。
    * 檢視負載均衡器使用或分配的 SNAT 連接埠總數,請使用指標聚合**總和**
1. 篩選到特定的**協定型態**,一組後端**IP**和/或**前端 IP**。
1. 要監視每個後端或前端實例的運行狀況,請應用拆分。 
    * 註釋拆分只允許一次顯示單個指標。 
1. 例如,要監視每台電腦 TCP 流的 SNAT 使用方式,按**平均值**聚合,按**後端 IP**拆分,按**協定類型**進行篩選。 

![SNAT 分配及使用](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*圖:一組後端 VM 的平均 TCP SNAT 連接埠配置和使用*

![後端介面的 SNAT 使用方式](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*圖:每個後端介面的 TCP SNAT 連接埠使用方式*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>如何查看服務的輸入/輸出連線嘗試？
<details>
  <summary>展開</summary>
「SYN 封包」計量會描述 TCP SYN 封包的數量，涵括與特定前端相關聯的已抵達或已傳送 (針對[輸出流程](https://aka.ms/lboutbound)) 封包。 您可以使用此計量來了解服務的 TCP 連線嘗試。

在大部分的情況下，請使用**總計**彙總。

![SYN 連線](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*圖:負載均衡器 SYN 計數*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>如何查看我的網路頻寬耗用？ 
<details>
  <summary>展開</summary>
「位元組和封包計數器」計量會描述您的服務在每個前端上所傳送或接收的位元組和封包數量。

在大部分的情況下，請使用**總計**彙總。

若要取得位元組或封包計數統計資料：
1. 選取 [位元組計數]**** 和/或 [封包計數]**** 計量類型，並選取 [平均]**** 作為彙總。 
2. 執行下列任一步驟：
   * 對特定前端 IP、前端連接埠或後端 IP 或後端連接埠套用篩選器。
   * 取得負載平衡器資源的整體統計資料 (不使用任何篩選)。

![位元組計數](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*圖:負載均衡器位元組計數*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>我要如何診斷我的負載平衡器部署？
<details>
  <summary>展開</summary>
在單一圖表上結合 VIP 可用性和健康情況探查計量，可讓您找出問題所在並解決問題。 您可以確定 Azure 正常運作，並利用此知識確定地判斷設定或應用程式是根本原因。

您可以透過健康情況探查計量來了解 Azure 依據您所提供的設定如何看待部署的健康情況。 查看健康情況探查永遠是監視或判斷原因時最好的第一步驟。

您可以採取進一步的動作，使用 VIP 可用性計量來深入了解針對架構出特定部署的基礎資料層，Azure 是如何看待其健康情況。 當您合併這兩個計量時，就能找出錯誤可能的位置，如此圖中範例所示：

![合併資料路徑可用性和執行狀況偵測狀態指標](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*圖:合併資料路徑可用性和執行狀況偵測狀態指標*

此圖表會顯示以下資訊：
- 託管 VM 的基礎結構不可用,圖表開頭的 0%。 稍後,基礎結構是健康的,VM是可訪問的,並且將多個 VM 放置在後端。 此資訊由資料路徑可用性(VIP 可用性)的藍色跟蹤指示,後者後來為 100%。 
- 由紫色跟蹤指示的運行狀況探測狀態 (DIP 可用性)在圖表的開頭為 0%。 綠色圓圈區域突出顯示運行狀況探測狀態 (DIP 可用性)變得正常,此時客戶的部署能夠接受新流。

此圖表讓客戶可以自行針對部署進行疑難排解，不必猜測或要求支援找出是否發生其他問題。 由於設定不正確或應用程式失敗導致健康情況探查失敗，所以服務變得無法使用。
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>資源健康情況狀態

標準 Load Balancer 資源的健康情況狀態會透過 [監視器] > [服務健康狀態]**** 底下現有的 [資源健康狀態]**** 公開。

若要檢視公用標準 Load Balancer 資源的健康情況：
1. 選擇**監看服務** > **執行狀況**。

   ![監視器頁面](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   圖：Azure 監視器上的服務健康情況連結**

2. 選取 [資源健康狀態]****，然後確定已選取 [訂用帳戶識別碼]**** 以及 [資源類型 = 負載平衡器]****。

   ![資源健康情況狀態](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   圖：選取資源以檢視健康情況**

3. 在清單中，選取 Load Balancer 資源，以檢視其過去的健康情況狀態。

    ![Load Balancer 健康情況狀態](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   圖：Load Balancer 資源的健康情況檢視**
 
下表列出各種資源健康狀態及其說明： 

| 資源健康情況狀態 | 描述 |
| --- | --- |
| 可用 | 您的標準負載均衡器資源是健康和可用的。 |
| 無法使用 | 您的標準負載均衡器資源不正常。 通過選擇 Azure**監視器** > **指標**來診斷運行狀況。<br>(*不可用*狀態還可能意味著資源未與您的標準負載均衡器連接。 |
| Unknown | 標準負載均衡器資源的資源運行狀況尚未更新。<br>(*未知*狀態還可能意味著資源未與您的標準負載均衡器連接。  |

## <a name="next-steps"></a>後續步驟

- 深入了解[標準負載平衡器](load-balancer-standard-overview.md)。
- 深入了解 [Load Balancer 輸出連線能力](https://aka.ms/lboutbound)。
- 了解 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)。
- 了解 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/) 和[如何透過 REST API 擷取計量](/rest/api/monitor/metrics/list)。
