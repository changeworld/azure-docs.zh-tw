---
title: 連接監視器 (預覽) |Microsoft Docs
description: 瞭解如何使用連線監視器 (預覽版) 來監視分散式環境中的網路通訊。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 80934dca73d7f8a205c62a49c418828cab1820e7
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123727"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>使用連線監視器 (預覽) 的網路連線能力監視

連線監視器 (預覽版) 可在 Azure 網路監看員中提供整合的端對端連線監視。 連接監視器 (預覽版) 功能支援混合式和 Azure 雲端部署。 網路監看員提供工具來監視、診斷和查看 Azure 部署的連線相關計量。

以下是連線監視器 (預覽) 的一些使用案例：

- 您的前端網頁伺服器 VM 會與多層式應用程式中的資料庫伺服器 VM 進行通訊。 您想要檢查兩部 Vm 之間的網路連線能力。
- 您想要讓美國東部區域中的 Vm 偵測美國中部區域中的 Vm，而您想要比較跨區域網路延遲。
- 您有多個內部部署辦公室網站位於西雅圖、華盛頓州和阿什本（弗吉尼亞州）。 您的 office 網站會連接到 Microsoft 365 的 Url。 針對 Microsoft 365 Url 的使用者，比較西雅圖與阿什本之間的延遲。
- 混合式應用程式需要連線到 Azure 儲存體端點。 您的內部部署網站和 Azure 應用程式會連接到相同的 Azure 儲存體端點。 您想要比較內部部署網站的延遲與 Azure 應用程式的延遲。
- 您想要檢查內部部署環境與裝載雲端應用程式的 Azure Vm 之間的連線能力。

在其預覽階段中，連線監視器結合了兩項功能的最大功能：網路監看員連線 [監視器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) 功能和網路效能監控 (NPM) [服務連線監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)、 [ExpressRoute 監視](https://docs.microsoft.com/azure/expressroute/how-to-npm)和 [效能監視](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-performance-monitor) 功能。

以下是連接監視器 (Preview) 的一些優點：

* 適用于 Azure 和混合式監視需求的統一、直覺體驗
* 跨區域、跨工作區的連線能力監視
* 更高的探查頻率及更佳的網路效能可見度
* 更快速的混合式部署警示
* 支援以 HTTP、TCP 和 ICMP 為基礎的連接檢查 
* Azure 和非 Azure 測試設定的計量和 Log Analytics 支援

![顯示連線監視器如何與 Azure Vm、非 Azure 主機、端點和資料儲存位置進行互動的圖表](./media/connection-monitor-2-preview/hero-graphic.png)

若要開始使用連線監視器 (預覽) 進行監視，請遵循下列步驟： 

1. 安裝監視代理程式。
1. 在您的訂用帳戶上啟用網路監看員。
1. 建立連線監視器。
1. 設定資料分析和警示。
1. 診斷網路中的問題。

下列各節提供這些步驟的詳細資料。

## <a name="install-monitoring-agents"></a>安裝監視代理程式

連線監視器依賴輕量可執行檔來執行連線檢查。  它支援來自 Azure 環境與內部部署環境的連線能力檢查。 您所使用的可執行檔取決於您的 VM 是裝載于 Azure 或內部部署。

### <a name="agents-for-azure-virtual-machines"></a>適用于 Azure 虛擬機器的代理程式

若要讓連線監視器將您的 Azure Vm 辨識為監視來源，請在其上安裝網路監看員代理程式虛擬機器擴充功能。 此延伸模組也稱為網路監看員 *延伸*模組。 Azure 虛擬機器需要擴充功能來觸發端對端監視和其他先進的功能。 

當您 [建立 VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)時，可以安裝網路監看員延伸模組。 您也可以針對 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 和 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的網路監看員延伸模組進行個別安裝、設定和疑難排解。

 (NSG) 或防火牆的網路安全性群組規則可以封鎖來源和目的地之間的通訊。 連線監視器會偵測到此問題，並將其顯示為拓撲中的診斷訊息。 若要啟用連線監視，請確定 NSG 和防火牆規則允許來源和目的地之間的 TCP 或 ICMP 的封包。

### <a name="agents-for-on-premises-machines"></a>內部部署機器的代理程式

若要讓連線監視器將內部部署機器辨識為要監視的來源，請在機器上安裝 Log Analytics 代理程式。 然後啟用網路效能監控的解決方案。 這些代理程式會連結到 Log Analytics 工作區，因此您必須先設定工作區識別碼和主要金鑰，代理程式才能開始監視。

若要安裝適用于 Windows 機器的 Log Analytics 代理程式，請參閱 [Azure 監視器 windows 的虛擬機器擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)功能。

如果路徑包含防火牆或網路虛擬裝置 (Nva) ，請確定可連線到目的地。

## <a name="enable-network-watcher-on-your-subscription"></a>在您的訂用帳戶上啟用網路監看員

具有虛擬網路的所有訂用帳戶都會使用網路監看員來啟用。 當您在訂用帳戶中建立虛擬網路時，會在虛擬網路的區域和訂用帳戶中自動啟用網路監看員。 這種自動啟用並不會影響您的資源，也不會產生費用。 確定您的訂用帳戶未明確停用網路監看員。 

如需詳細資訊，請參閱 [啟用網路](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)監看員。

## <a name="create-a-connection-monitor"></a>建立連線監視 

連線監視器會以固定間隔監視通訊。 它會通知您有能力和延遲的變更。 您也可以檢查來源代理程式和目的地端點之間的目前和歷史網路拓撲。

來源可以是已安裝監視代理程式的 Azure Vm 或內部部署機器。 目的地端點可以是 Microsoft 365 Url、Dynamics 365 Url、自訂 Url、Azure VM 資源識別碼、IPv4、IPv6、FQDN 或任何功能變數名稱。

### <a name="access-connection-monitor-preview"></a>存取連線監視器 (預覽) 

1. 在 Azure 入口網站首頁上，移至 **網路**監看員。
1. 在左側的 [ **監視** ] 區段中，選取 [連線 **監視器 (預覽]) **。
1. 您會看到在連線監視器中建立的所有連線監視器 (Preview) 。 若要查看在連線監視器的傳統體驗中建立的連線監視器，請移至 [連線 **監視器** ] 索引標籤。
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>建立連線監視

在您于連線監視器中建立的連線監視器 (預覽版) ，您可以將內部部署機器和 Azure Vm 作為來源來新增。 這些連線監視器也可以監視端點的連線能力。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連接監視器 (預覽) 包含下列實體：

* 連線**監視器資源**–區域特定的 Azure 資源。 下列所有實體都是連線監視器資源的屬性。
* **端點** –參與連線能力檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試** 設定–適用于測試的通訊協定特定設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率及其他參數。
* **測試群組** –包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試** –來源端點、目的地端點和測試設定的組合。 測試是可使用監視資料的最細微層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回行程時間。

 ![顯示連接監視器、定義測試群組與測試之間關聯性的圖表](./media/connection-monitor-2-preview/cm-tg-2.png)

您可以使用[Azure 入口網站](connection-monitor-preview-create-using-portal.md)或[ARMClient](connection-monitor-preview-create-using-arm-client.md)建立連線監視器預覽

您加入至測試群組的所有來源、目的地和測試設定都會細分為個別測試。 以下是來源和目的地如何細分的範例：

* 測試群組： TG1
* 來源： 3 (A，B，C) 
* 目的地： 2 (D，E) 
* 測試設定： 2 (Config 1，Config 2) 
* 已建立的測試總數：12

| 測試編號 | 來源 | Destination | 測試組態 |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a> 調整限制

連線監視器具有下列規模限制：

* 每個區域每個訂用帳戶的連線監視器上限：100
* 每個連接監視器的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限：20

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析監視資料並設定警示

建立連線監視器之後，來源會根據您的測試設定，檢查目的地的連線能力。

### <a name="checks-in-a-test"></a>測試中的簽入

根據您在測試設定中選擇的通訊協定， (Preview) 的連線監視器會針對來源-目的地配對執行一系列檢查。 檢查會根據您所選擇的測試頻率執行。

如果您使用 HTTP，此服務會計算傳回有效回應碼的 HTTP 回應數目。 您可以使用 PowerShell 和 CLI 來設定有效的回應碼。 結果會決定失敗檢查的百分比。 為了計算 RTT，服務會測量 HTTP 呼叫和回應之間的時間。

如果您使用 TCP 或 ICMP，此服務會計算封包遺失百分比，以判斷失敗的檢查百分比。 為了計算 RTT，此服務會測量收到所傳送封包的通知 (ACK) 所花的時間。 如果您已啟用網路測試的追蹤路由資料，您可以看到內部部署網路的逐躍點遺失和延遲。

### <a name="states-of-a-test"></a>測試的狀態

根據檢查傳回的資料，測試可能會有下列狀態：

* **Pass** –失敗檢查的百分比實際值，而 RTT 在指定的閾值內。
* **失敗** –失敗的檢查或 RTT 百分比的實際值超過指定的閾值。 如果未指定任何臨界值，當失敗檢查的百分比為100時，測試便會進入失敗狀態。
* **警告** – 
     * 如果指定了臨界值，且連線監視器 (預覽) 觀察到的閾值超過80% 的閾值，則測試會標示為「警告」。
     * 如果沒有指定的閾值，連接監視器 (預覽) 會自動指派閾值。 當超過該臨界值時，測試狀態會變更為 [警告]。若為 TCP 或 ICMP 測試中的往返時間，則會750msec 臨界值。 針對檢查失敗的百分比，閾值為10%。 
* **不定**  – Log Analytics 工作區中沒有任何資料。檢查計量。 
* **未執行**  –停用測試群組  

### <a name="data-collection-analysis-and-alerts"></a>資料收集、分析和警示

連線監視器 (預覽版) 收集的資料會儲存在 Log Analytics 工作區中。 當您建立連線監視器時，會設定此工作區。 

Azure 監視器計量也提供監視資料。 您可以使用 Log Analytics 來保留您的監視資料，只要您想要。 Azure 監視器預設只會儲存30天的度量。 

您可以 [針對資料設定以計量為基礎的警示](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>監視儀表板

在監視儀表板上，您會看到可供您存取的連線監視器清單、區域、時間戳記、來源和目的地類型。

當您從網路監看員移至連線監視器 (預覽版) 時，您可以透過下列方式來查看資料：

* 連線**監視器**–針對您的訂用帳戶、區域、時間戳記、來源和目的地類型所建立的所有連線監視器清單。 此檢視為預設。
* **測試群組** –針對您的訂用帳戶、區域、時間戳記、來源和目的地類型建立的所有測試群組清單。 這些測試群組不會透過連接監視器進行篩選。
* **測試** –針對您的訂用帳戶、區域、時間戳記、來源和目的地類型執行的所有測試清單。 這些測試不會依連接監視器或測試群組進行篩選。

在下圖中，箭號1表示三個資料檢視。

在儀表板上，您可以展開每個連接監視器，以查看其測試群組。 然後您可以展開每個測試群組，以查看在其中執行的測試。 

您可以根據下列條件篩選清單：

* **最上層篩選** -依文字、實體類型 (連接監視器、測試群組或測試) 時間戳記和範圍搜尋清單。 範圍包括訂用帳戶、區域、來源和目的地類型。 請參閱下圖中的 box 1。
* 以**狀態為基礎的篩選**條件–依連線監視器、測試群組或測試的狀態進行篩選。 請參閱下圖中的 box 2。
* **警示型篩選** 器-依連線監視器資源上引發的警示進行篩選。 請參閱下圖中的 box 3。

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
例如，若要查看連線監視器中的所有測試 (預覽版) 10.192.64.56 來源 IP：
1. 變更要 **測試**的視圖。
1. 在 [搜尋] 欄位中，輸入 *10.192.64.56*
1. 在 [最上層篩選的 **範圍** ] 中，選取 [ **來源**]。

若只要在連線監視器中顯示失敗的測試 (預覽) 10.192.64.56 來源 IP 的來源 IP：
1. 變更要 **測試**的視圖。
1. 如果是以狀態為基礎的篩選準則，請選取 [ **失敗**]。
1. 在 [搜尋] 欄位中，輸入 *10.192.64.56*
1. 在 [最上層篩選的 **範圍** ] 中，選取 [ **來源**]。

若只要在連線監視器中顯示失敗的測試 (預覽) 目的地為 outlook.office365.com：
1. 將 view 變更為 **Test**。
1. 如果是以狀態為基礎的篩選準則，請選取 [ **失敗**]。
1. 在 [搜尋] 欄位中，輸入 *outlook.office365.com*
1. 在 [最上層篩選的 **範圍** ] 中，選取 [ **目的地**]。
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/tests-view.png":::

若要知道連線監視或測試群組或測試失敗的原因，請按一下名為 [原因] 的資料行。  這會指出 ( 檢查失敗的閾值% 或 RTT) 被入侵，以及相關的診斷訊息
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
若要查看 RTT 的趨勢，以及連線監視器的失敗檢查百分比：
1. 選取您要調查的連接監視器。

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. 您將會看到下列區段  
    1. Essentials-所選連線監視器的資源特定屬性 
    1. 小結 
        1. 連接監視器中所有測試的 RTT 匯總趨勢線和失敗檢查百分比。 您可以設定特定時間來查看詳細資料。
        1. 測試群組中的前5個、來源和目的地，以失敗檢查的 RTT 或百分比為基礎。 
    1. 測試群組、來源、目的地和測試設定的索引標籤-列出連接監視器中的測試群組、來源或目的地。 檢查測試失敗、匯總 RTT 和檢查失敗的% 值。  您也可以回頭返回以查看資料。 
    1. 問題-連接監視器中每個測試的躍點層級問題。 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. 您可以
    * 按一下 [查看所有測試] 以查看連接監視器中的所有測試
    * 按一下 [View all test groups]、[測試設定]、[來源] 和 [目的地]，即可查看每個測試群組的特定 
    * 選擇 [測試群組]、[測試設定]、[來源] 或 [目的地]，以查看實體中的所有測試。

1. 從 [所有測試] 視圖中，您可以：
    * 選取 [測試]，然後按一下 [比較]。
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * 使用叢集將 VNET、子網等複合資源展開至其子資源
    * 按一下 [拓撲] 以查看任何測試的拓撲。

若要查看 RTT 的趨勢，以及測試群組的失敗檢查百分比：
1. 選取您要調查的測試群組。 
1. 您將會看到類似于連線監視器-基本、摘要、適用于測試群組、來源、目的地和測試設定的資料表。 如同您針對連線監視所做的一樣地流覽

若要查看 RTT 的趨勢，以及測試的失敗檢查百分比：
1. 選取您要調查的測試。 您會看到網路拓撲，以及檢查失敗% 和來回行程時間的端對端趨勢圖。 若要查看已識別的問題，請在拓撲中選取路徑中的任何躍點。  (這些躍點都是 Azure 資源。 ) 這項功能目前不適用於內部部署網路

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 中的記錄查詢

使用 Log Analytics 來建立監視資料的自訂視圖。 UI 所顯示的所有資料都是來自 Log Analytics。 您可以用互動方式分析存放庫中的資料。 將代理程式健全狀況或其他以 Log Analytics 為基礎的解決方案的資料相互關聯。 將資料匯出至 Excel 或 Power BI，或建立可共用的連結。

#### <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

在連線監視器之前建立的連線監視器 (預覽版) 體驗中，總共提供四個度量：% 探查失敗、AverageRoundtripMs、ChecksFailedPercent (Preview) 和 RoundTripTimeMs (Preview) 。 在連線監視器中建立的連線監視器 (預覽) 體驗中，資料僅適用于以 * (Preview) *標記的度量。

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

當您使用計量時，請將資源類型設為 Microsoft. Network/networkWatchers/connectionMonitors

| 計量 | 顯示名稱 | Unit | 彙總類型 | 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗的探查百分比 | 百分比 | 平均 | 連線能力監視探查的百分比失敗。 | 無維度 |
| AverageRoundtripMs | Avg.來回時間 (毫秒) | 毫秒 | Average | 在來源與目的地之間傳送之連線能力監視探查的平均網路 RTT。 |             無維度 |
| ChecksFailedPercent (Preview)  | % 檢查失敗 (預覽)  | 百分比 | 平均 | 測試失敗檢查的百分比。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>區域 |
| RoundTripTimeMs (Preview)  | 來回時間 (ms)  (Preview)  | 毫秒 | Average | 在來源與目的地之間傳送檢查的 RTT。 此值不是平均值。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>區域 |

#### <a name="metric-based-alerts-for-connection-monitor"></a>連接監視的計量型警示

您可以使用下列方法，在連線監視器上建立計量警示 

1. 從連線監視器 (預覽) 在[使用 Azure 入口網站](connection-monitor-preview-create-using-portal.md#)建立連線監視器期間 
1. 從連線監視器 (預覽) ，在儀表板中使用 [設定警示] 
1. 從 Azure 監視器-在 Azure 監視器中建立警示： 
    1. 選擇您在連線監視器中建立的連線監視器資源 (Preview) 。
    1. 確定計量 **顯示為連線監視的信號** 類型。
    1. 在 [ **新增條件**] 的 [ **信號名稱**] 中，選取 [ **ChecksFailedPercent (Preview]) ** 或 [ **RoundTripTimeMs (預覽]) **。
    1. 針對 [ **信號類型**]，選擇 [ **計量**]。 例如，選取 [ **ChecksFailedPercent (預覽]) **。
    1. 系統會列出度量的所有維度。 選擇維度名稱和維度值。 例如，選取 [ **來源位址** ]，然後輸入連線監視器中任何來源的 IP 位址。
    1. 在 **警示邏輯**中，填寫下列詳細資料：
        * **條件類型**： **靜態**。
        * **條件** 和 **臨界值**。
        * **匯總的資料細微性和評估頻率**：連線監視器 (預覽) 每分鐘更新資料。
    1. 在 [ **動作**] 中，選擇您的動作群組。
    1. 提供警示詳細資料。
    1. 建立警示規則。

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="螢幕擷取畫面，顯示連線監視器 (預覽版中建立的連線監視器) " lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>診斷網路中的問題

連線監視器 (預覽) 可協助您診斷連線監視器和網路中的問題。 您稍早安裝的 Log Analytics 代理程式會偵測到混合式網路中的問題。 網路監看員延伸模組偵測到 Azure 中的問題。 

您可以在網路拓撲中查看 Azure 網路中的問題。

針對其來源為內部部署 Vm 的網路，可以偵測到下列問題：

* 要求逾時。
* DNS 無法解析的端點–暫時性或持續性。 URL 無效。
* 找不到任何主機。
* 來源無法連接到目的地。 無法透過 ICMP 連線到目標。
* 憑證相關問題： 
    * 驗證代理程式所需的用戶端憑證。 
    * 無法存取憑證重新配置清單。 
    * 端點的主機名稱與憑證的主體或主體替代名稱不相符。 
    * 來源的本機電腦「信任的憑證授權單位單位」存放區中遺失根憑證。 
    * SSL 憑證已過期、無效、已撤銷或不相容。

針對其來源為 Azure Vm 的網路，可以偵測到下列問題：

* 代理程式問題：
    * 代理程式已停止。
    * DNS 解析失敗。
    * 沒有在目的地埠上接聽的應用程式或接聽程式。
    * 無法開啟通訊端。
* VM 狀態問題： 
    * 啟動中
    * 停止中
    * 已停止
    * 正在解除配置
    * 已解除配置
    * 重 啟
    * 未配置
* 遺漏 ARP 資料表專案。
* 流量因為本機防火牆問題或 NSG 規則而遭到封鎖。
* 虛擬網路閘道問題： 
    * 遺漏路由。
    * 兩個閘道之間的通道已中斷連線或遺失。
    * 通道找不到第二個閘道。
    * 找不到對等互連資訊。
* Microsoft Edge 中缺少路由。
* 流量因為系統路由或 UDR 而停止。
* 閘道連線上未啟用 BGP。
* 負載平衡器已關閉 DIP 探查。

## <a name="next-steps"></a>後續步驟
    
   * 瞭解 [如何使用 Azure 入口網站建立連線監視器 (預覽版) ](connection-monitor-preview-create-using-portal.md)  
   * 瞭解 [如何使用 ARMClient 建立連線監視器 (預覽版) ](connection-monitor-preview-create-using-arm-client.md)  
