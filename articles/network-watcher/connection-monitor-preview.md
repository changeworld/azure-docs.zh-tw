---
title: 連接監視器 (預覽) |Microsoft Docs
description: 瞭解如何使用連線監視器 (預覽) 來監視分散式環境中的網路通訊。
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
ms.openlocfilehash: 0cb51cd224145e7fe359e2b14a87ed2b87b18c26
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563019"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>使用連線監視器 (預覽) 進行網路連線監視

連線監視器 (預覽) 在 Azure 網路監看員中提供整合的端對端連線監視。  (Preview) 功能的連線監視器支援混合式和 Azure 雲端部署。 網路監看員提供工具來監視、診斷及查看 Azure 部署的連線相關計量。

以下是連接監視器 (預覽) 的一些使用案例：

- 您的前端網頁伺服器 VM 會與多層式應用程式中的資料庫伺服器 VM 通訊。 您想要檢查兩個 Vm 之間的網路連線。
- 您想要讓美國東部區域中的 Vm ping 美國中部區域中的 Vm，而您想要比較跨區域的網路延遲。
- 您有多個內部部署辦公室網站位於西雅圖、華盛頓州和阿什本，弗吉尼亞州。 您的 office 網站會連接到 Office 365 Url。 針對您的 Office 365 Url 使用者，請比較西雅圖和阿什本之間的延遲。
- 您的混合式應用程式需要 Azure 儲存體端點的連線能力。 您的內部部署網站和 Azure 應用程式會連接到相同的 Azure 儲存體端點。 您想要將內部部署網站的延遲與 Azure 應用程式的延遲進行比較。
- 您想要檢查內部部署的連線與裝載雲端應用程式的 Azure Vm 之間的連線能力。

在其預覽階段中，連線監視結合了兩項功能的最大：網路監看員連線[監視](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能，以及網路效能監控 (NPM) [服務連線能力監視](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)功能。

以下是連接監視器 (Preview) 的一些優點：

* 適用于 Azure 和混合式監視需求的統一、直覺體驗
* 跨區域、跨工作區的連線能力監視
* 更高的探查頻率和更佳的網路效能可見度
* 更快的混合式部署警示
* 支援以 HTTP、TCP 和 ICMP 為基礎的連線檢查 
* Azure 和非 Azure 測試設定的計量和 Log Analytics 支援

![此圖顯示連線監視器與 Azure Vm、非 Azure 主機、端點和資料儲存位置之間的互動](./media/connection-monitor-2-preview/hero-graphic.png)

若要開始使用連線監視器 (預覽) 進行監視，請遵循下列步驟： 

1. 安裝監視代理程式。
1. 在您的訂用帳戶上啟用網路監看員。
1. 建立連線監視器。
1. 設定資料分析和警示。
1. 診斷網路中的問題。

下列各節提供這些步驟的詳細資料。

## <a name="install-monitoring-agents"></a>安裝監視代理程式

連線監視會依賴輕量可執行檔來執行連線能力檢查。  它支援來自 Azure 環境和內部部署環境的連線能力檢查。 您所使用的可執行檔取決於您的 VM 是裝載于 Azure 或內部部署環境。

### <a name="agents-for-azure-virtual-machines"></a>適用于 Azure 虛擬機器的代理程式

若要讓連線監視器將您的 Azure Vm 辨識為監視來源，請在其上安裝網路監看員代理程式虛擬機器擴充功能。 此延伸模組也稱為*網路監看員延伸*模組。 Azure 虛擬機器需要延伸模組來觸發端對端監視和其他先進功能。 

當您[建立 VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)時，可以安裝網路監看員擴充功能。 您也可以個別安裝、設定和疑難排解適用于[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的網路監看員延伸模組。

 (NSG) 或防火牆的網路安全性群組規則，可以封鎖來源與目的地之間的通訊。 連線監視會偵測此問題，並將其顯示為拓撲中的診斷訊息。 若要啟用連線監視，請確定 NSG 和防火牆規則允許來源與目的地之間的 TCP 或 ICMP 封包。

### <a name="agents-for-on-premises-machines"></a>內部部署機器的代理程式

若要讓連線監視器將您的內部部署機器辨識為監視的來源，請在電腦上安裝 Log Analytics 代理程式。 然後啟用網路效能監控解決方案。 這些代理程式會連結到 Log Analytics 工作區，因此您必須先設定工作區識別碼和主要金鑰，才能讓代理程式開始進行監視。

若要安裝適用于 Windows 機器的 Log Analytics 代理程式，請參閱[適用于 windows 的 Azure 監視器虛擬機器擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)功能。

如果路徑包含 (Nva) 的防火牆或網路虛擬裝置，請確定可連線到目的地。

## <a name="enable-network-watcher-on-your-subscription"></a>在您的訂用帳戶上啟用網路監看員

具有虛擬網路的所有訂用帳戶都已啟用網路監看員。 當您在訂用帳戶中建立虛擬網路時，會在虛擬網路的區域與訂用帳戶中自動啟用網路監看員。 這種自動啟用不會影響您的資源或產生費用。 確定您的訂用帳戶未明確停用網路監看員。 

如需詳細資訊，請參閱[Enable 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="create-a-connection-monitor"></a>建立連線監視 

連線監視器會以固定間隔監視通訊。 它會通知您有關可連線性和延遲的變更。 您也可以檢查來源代理程式與目的地端點之間的目前和歷史網路拓撲。

來源可以是已安裝監視代理程式的 Azure Vm 或內部部署機器。 目的地端點可以是 Office 365 Url、Dynamics 365 Url、自訂 Url、Azure VM 資源識別碼、IPv4、IPv6、FQDN 或任何功能變數名稱。

### <a name="access-connection-monitor-preview"></a>存取 (預覽) 的連線監視器

1. 在 [Azure 入口網站] 首頁上，移至 [**網路監看員**]。
1. 在左側的 [**監視**] 區段中，選取 [連線**監視器] (預覽**] [) ]。
1. 您會看到所有在連線監視器中建立的連線監視器 (預覽) 。 若要查看在傳統連線監視器中建立的連線監視器，請移至 [**連接監視器**] 索引標籤。

    ![顯示連線監視器中建立的連線監視器 (預覽的螢幕擷取畫面) ](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>建立連線監視

在您于連線監視器中建立的連線監視器 (預覽) 中，您可以將內部部署機器和 Azure Vm 新增為來源。 這些連接監視器也可以監視端點的連線。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連線監視器 (預覽) 包含下列實體：

* 連線**監視資源**–區域特定的 Azure 資源。 下列所有實體都是連接監視器資源的屬性。
* **端點**–參與連線性檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試**設定–適用于測試的通訊協定專屬設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率和其他參數。
* **測試群組**–包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試**–來源端點、目的地端點和測試設定的組合。 測試是可用的監視資料最細微的層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回時間。

 ![圖表顯示連接監視器，定義測試群組與測試之間的關聯性](./media/connection-monitor-2-preview/cm-tg-2.png)

您可以使用[Azure 入口網站](connection-monitor-preview-create-using-portal.md)或[ARMClient](connection-monitor-preview-create-using-arm-client.md)建立連接監視器預覽

您加入至測試群組的所有來源、目的地和測試設定都會細分為個別的測試。 以下是如何將來源和目的地細分的範例：

* 測試群組： TG1
* 來源： 3 (A、B、C) 
* 目的地： 2 (D，E) 
* 測試設定： 2 (Config 1，Config 2) 
* 已建立的測試總數：12

| 測試編號 | 來源 | Destination | 測試組態 |
| --- | --- | --- | --- |
| 1 | A | D | 設定1 |
| 2 | A | D | Config 2 |
| 3 | A | E | 設定1 |
| 4 | A | E | Config 2 |
| 5 | B | D | 設定1 |
| 6 | B | D | Config 2 |
| 7 | B | E | 設定1 |
| 8 | B | E | Config 2 |
| 9 | C | D | 設定1 |
| 10 | C | D | Config 2 |
| 11 | C | E | 設定1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>調整限制

連接監視器具有下列調整限制：

* 每個訂用帳戶每個區域的連線監視器上限：100
* 每個連線監視的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限： 
    * 20 via ARMClient
    * 2透過 Azure 入口網站

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析監視資料並設定警示

建立連線監視器之後，來源會根據您的測試設定來檢查目的地的連線能力。

### <a name="checks-in-a-test"></a>測試中的簽入

根據您在測試設定中選擇的通訊協定，連接監視器 (預覽) 會針對來源-目的地組執行一系列檢查。 檢查會根據您選擇的測試頻率來執行。

如果您使用 HTTP，服務會計算傳迴響應碼的 HTTP 回應數目。 結果會決定失敗檢查的百分比。 為了計算 RTT，服務會測量 HTTP 呼叫與回應之間的時間。

如果您使用 TCP 或 ICMP，服務會計算封包遺失百分比，以判斷失敗檢查的百分比。 為了計算 RTT，此服務會測量接收所傳送封包 (ACK) 所花費的時間。 如果您已啟用網路測試的追蹤路由資料，您可以看到內部部署網路的逐躍點遺失和延遲。

### <a name="states-of-a-test"></a>測試的狀態

根據檢查傳回的資料，測試可以具有下列狀態：

* **Pass** –失敗檢查和 RTT 百分比的實際值在指定的閾值內。
* **Fail** –失敗檢查或 RTT 百分比的實際值超過指定的閾值。 如果未指定任何臨界值，則當失敗檢查的百分比為100時，測試就會到達 [失敗] 狀態。
* **警告**–未針對失敗檢查的百分比指定任何準則。 如果沒有指定的準則，連線監視器 (預覽) 會自動指派閾值。 超過該臨界值時，測試狀態會變更為 [警告]。

### <a name="data-collection-analysis-and-alerts"></a>資料收集、分析和警示

連接監視器 (預覽) 收集的資料會儲存在 Log Analytics 工作區中。 您會在建立連線監視器時設定此工作區。 

Azure 監視器計量也提供監視資料。 您可以使用 Log Analytics 來保留您的監視資料，只要您想要。 根據預設，Azure 監視器只會儲存30天的計量。 

您可以[針對資料設定以計量為基礎的警示](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>監視儀表板

在 [監視] 儀表板上，您會看到可供您的訂用帳戶、區域、時間戳記、來源及目的地類型存取的連線監視器清單。

當您從網路監看員移至 [連線監視器] ([預覽) ] 時，您可以透過下列方式來查看資料：

* 連線**監視**–針對您的訂用帳戶、區域、時間戳記、來源及目的地類型所建立的所有連線監看清單。 此檢視為預設。
* **測試群組**–針對您的訂用帳戶、區域、時間戳記、來源及目的地類型所建立的所有測試群組清單。 這些測試群組不會透過連線監視器進行篩選。
* **測試**–針對您的訂用帳戶、區域、時間戳記、來源及目的地類型執行之所有測試的清單。 這些測試不會依連接監視器或測試群組進行篩選。

在下圖中，三個資料檢視會以箭號1來表示。

在儀表板上，您可以展開每個連線監視器以查看其測試群組。 然後您可以展開每個測試群組，以查看其中執行的測試。 

您可以根據下列條件篩選清單：

* **最上層篩選**–選擇 [訂用帳戶]、[區域]、[時間戳記來源] 和 [目的地類型]。 請參閱下圖中的 box 2。
* 以**狀態為基礎的篩選**–依連接監視器、測試群組或測試的狀態進行篩選。 請參閱下圖中的箭號3。
* **自訂篩選**–選擇 [**全選**] 以進行一般搜尋。 若要依特定實體進行搜尋，請從下拉式清單中選取。 請參閱下圖中的箭號4。

![螢幕擷取畫面：顯示如何在連線監視器中篩選連接監視器、測試群組和測試的視圖 (預覽) ](./media/connection-monitor-2-preview/cm-view.png)

例如，若要查看 [連線監視器] 中的所有測試，請 (預覽] 中10.192.64.56 來源 IP 的) ：
1. 將此視圖變更為 [**測試**]。
1. 在搜尋欄位中，輸入*10.192.64.56*
1. 在下拉式清單中，選取 [**來源**]。

若只要在連線監視器中顯示失敗的測試 (預覽) 其中10.192.64.56 來源 IP：
1. 將此視圖變更為 [**測試**]。
1. 針對以狀態為基礎的篩選準則，選取 [**失敗**]。
1. 在搜尋欄位中，輸入*10.192.64.56*
1. 在下拉式清單中，選取 [**來源**]。

若只要在連線監視器中顯示失敗的測試 (預覽) 其中目的地為 outlook.office365.com：
1. 將 view 變更為**Test**。
1. 針對以狀態為基礎的篩選準則，選取 [**失敗**]。
1. 在搜尋欄位中，輸入*outlook.office365.com*
1. 在下拉式清單中，選取 [**目的地**]。

   ![螢幕擷取畫面，顯示已篩選為只顯示 Outlook.Office365.com 目的地失敗測試的視圖](./media/connection-monitor-2-preview/tests-view.png)

若要查看 RTT 的趨勢，以及連接監視的失敗檢查百分比：
1. 選取您想要調查的連線監視器。 根據預設，監視資料是依測試群組來組織。

   ![顯示由測試群組顯示之連線監視器計量的螢幕擷取畫面](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 選擇您想要調查的測試群組。

   ![顯示要選取測試群組之位置的螢幕擷取畫面](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    您會根據 RTT 或失敗檢查的百分比，看到測試群組的前五個失敗測試。 針對每個測試，您會看到失敗檢查百分比的 RTT 和趨勢線。
1. 從清單中選取測試，或選擇另一個要調查的測試。 針對您的時間間隔和失敗檢查的百分比，您會看到閾值和實際值。 針對 RTT，您會看到 [臨界值]、[平均值]、[最小值] 和 [最大值]

   ![螢幕擷取畫面，顯示測試的 RTT 結果和失敗檢查的百分比](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 變更時間間隔以查看更多資料。
1. 變更視圖以查看來源、目的地或測試設定。 
1. 根據失敗的測試選擇來源，並調查前五個失敗的測試。 例如，選擇 [**依**  >  **來源**查看] 和 [**依**  >  **目的地**查看]，以調查連線監視器中的相關測試。

   ![顯示前五個失敗測試之效能計量的螢幕擷取畫面](./media/connection-monitor-2-preview/cm-drill-select-source.png)

若要查看 RTT 的趨勢，以及測試群組的失敗檢查百分比：

1. 選取您要調查的測試群組。 

    根據預設，監視資料會依來源、目的地和測試設定來排列， (測試) 。 之後，您可以將視圖從 [測試群組] 變更為 [來源]、[目的地] 或 [測試設定]。 然後選擇一個實體來調查前五個失敗的測試。 例如，將 [視圖] 變更為 [來源] 和 [目的地]，以調查所選連線監視器中的相關測試。
1. 選擇您想要調查的測試。

   ![顯示要選取測試之位置的螢幕擷取畫面](./media/connection-monitor-2-preview/tg-drill.png)

    針對您的時間間隔和失敗檢查的百分比，您會看到臨界值和實際值。 針對 RTT，您會看到 [閾值]、[平均]、[最小值] 和 [最大值] 您也會看到所選測試的已引發警示。
1. 變更時間間隔以查看更多資料。

若要查看 RTT 的趨勢，以及測試失敗檢查的百分比：
1. 選取您想要調查的來源、目的地和測試設定。

    針對您的時間間隔和失敗檢查的百分比，您會看到臨界值和實際值。 針對 RTT，您會看到 [閾值]、[平均]、[最小值] 和 [最大值] 您也會看到所選測試的已引發警示。

   ![顯示測試計量的螢幕擷取畫面](./media/connection-monitor-2-preview/test-drill.png)

1. 若要查看網路拓撲，請選取 [**拓撲**]。

   ![顯示 [網路拓撲] 索引標籤的螢幕擷取畫面](./media/connection-monitor-2-preview/test-topo.png)

1. 若要查看已識別的問題，請在拓撲中選取路徑中的任何躍點。  (這些躍點都是 Azure 資源。 ) 這項功能目前不適用於內部部署網路。

   ![顯示 [拓撲] 索引標籤上所選躍點連結的螢幕擷取畫面](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 中的記錄查詢

使用 Log Analytics 來建立監視資料的自訂視圖。 UI 顯示的所有資料都是來自 Log Analytics。 您可以互動方式分析存放庫中的資料。 將資料與以 Log Analytics 為基礎的代理程式健全狀況或其他解決方案相互關聯。 將資料匯出至 Excel 或 Power BI，或建立可共用的連結。

#### <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

在連線監視器 (Preview) 體驗之前建立的連線監視器中，有四個可用的計量：% 探查 Failed、AverageRoundtripMs、ChecksFailedPercent (Preview) 和 RoundTripTimeMs (Preview) 。 在連線監視器中建立的連線監視器 (預覽) 體驗中，資料僅適用于以* (Preview) *標記的計量。

![螢幕擷取畫面，顯示連線監視器中的計量 (預覽) ](./media/connection-monitor-2-preview/monitor-metrics.png)

當您使用計量時，請將資源類型設定為 [Microsoft]/[networkWatchers/connectionMonitors]。

| 計量 | 顯示名稱 | 單位 | 彙總類型 | 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗的探查百分比 | 百分比 | Average | 連線能力監視探查的百分比失敗。 | 無維度 |
| AverageRoundtripMs | Avg.來回時間 (毫秒) | 毫秒 | Average | 在來源與目的地之間傳送之連線能力監視探查的平均網路 RTT。 |             無維度 |
| ChecksFailedPercent (Preview)  | % 檢查失敗 (預覽)  | 百分比 | Average | 測試的失敗檢查百分比。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>區域 |
| RoundTripTimeMs (Preview)  | 來回時間 (ms)  (Preview)  | 毫秒 | Average | 來源與目的地之間所傳送之檢查的 RTT。 此值不是平均值。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>區域 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure 監視器中的計量警示

若要在 Azure 監視器中建立警示：

1. 選擇您在連線監視器 (預覽) 中建立的連線監視器資源。
1. 確定 [計量 **] 顯示為連線監視器的 [** 信號類型]。
1. 在 [**新增條件**] 的 [**信號名稱**] 中，選取 [ **ChecksFailedPercent (預覽]) **或**RoundTripTimeMs (preview) **]。
1. 針對 [**信號類型**]，選擇 [**計量**]。 例如，選取 [ **ChecksFailedPercent (預覽]) **。
1. 系統會列出度量的所有維度。 選擇維度名稱和維度值。 例如，選取 [**來源位址**]，然後輸入連線監視器中任何來源的 IP 位址。
1. 在 [**警示邏輯**] 中，填入下列詳細資料：
   * **條件類型**：**靜態**。
   * **條件**和**臨界值**。
   * **匯總細微性和評估頻率**：連線監視器 (預覽) 每分鐘更新資料。
1. 在 [**動作**] 中，選擇您的動作群組。
1. 提供警示詳細資料。
1. 建立警示規則。

   ![螢幕擷取畫面，顯示 Azure 監視器中的 [建立規則] 區域;[來源位址] 和 [來源端點名稱] 已反白顯示](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>診斷網路中的問題

連線監視器 (預覽) 可協助您診斷連線監視器和網路中的問題。 您稍早安裝的 Log Analytics 代理程式會偵測到混合式網路中的問題。 網路監看員延伸模組會偵測到 Azure 中的問題。 

您可以在網路拓撲中的 Azure 網路中查看問題。

對於來源為內部部署 Vm 的網路，可能會偵測到下列問題：

* 要求逾時。
* DNS 未解析的端點–暫時性或持續性。 URL 無效。
* 找不到任何主機。
* 來源無法連接到目的地。 無法透過 ICMP 到達目標。
* 憑證相關問題： 
    * 驗證代理程式所需的用戶端憑證。 
    * 無法存取憑證重新配置清單。 
    * 端點的主機名稱與憑證的主體或主體替代名稱不相符。 
    * 來源的本機電腦「信任的憑證授權單位單位」存放區中遺漏根憑證。 
    * SSL 憑證已過期、無效、已撤銷或不相容。

針對來源為 Azure Vm 的網路，可能會偵測到下列問題：

* 代理程式問題：
    * 代理程式已停止。
    * DNS 解析失敗。
    * 在目的地埠上沒有任何應用程式或接聽程式。
    * 無法開啟通訊端。
* VM 狀態問題： 
    * 啟動中
    * 停止中
    * 已停止
    * 正在解除配置
    * 已解除配置
    * 正在
    * 未配置
* 缺少 ARP 資料表專案。
* 流量因為本機防火牆問題或 NSG 規則而遭到封鎖。
* 虛擬網路閘道問題： 
    * 遺漏路由。
    * 兩個閘道之間的通道已中斷連線或遺失。
    * 通道找不到第二個閘道。
    * 找不到任何對等資訊。
* Microsoft Edge 中缺少路由。
* 流量因系統路由或 UDR 而停止。
* 未在閘道連線上啟用 BGP。
* DIP 探查會在負載平衡器關閉。
