---
title: 使用 Azure 私人連結將網路安全地連線到 Azure 監視器
description: 使用 Azure 私人連結將網路安全地連線到 Azure 監視器
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 61073ce7e8d3abc43d1db031608da72e6d3e0791
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926796"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>使用 Azure 私人連結將網路安全地連線到 Azure 監視器

[Azure 私人連結](../../private-link/private-link-overview.md)可讓您使用私人端點，將 Azure PaaS 服務安全地連結至您的虛擬網路。 如果有許多服務，您只需為每個資源設定一個端點。 不過，Azure 監視器是不同的相互連線服務的集合，它們可以一起監視您的工作負載。 因此，我們建立了稱為 Azure 監視器私人連結範圍 (AMPLS) 的資源，可讓您定義監視網路的界限，以及連線到您的虛擬網路。 本文涵蓋 Azure 監視器私人連結範圍的使用時機以及如何設定 。

## <a name="advantages"></a>優點

有了私人連結，您可以：

- 私下連線到 Azure 監視器，而不需要開啟任何公用網路存取
- 確保您的監視資料只能透過授權的私人網路存取
- 藉由定義透過私人端點連線的特定 Azure 監視器資源，防止資料從您的私人網路外洩
- 使用 ExpressRoute 和私人連結，將您的私人內部部署網路安全地連線至 Azure 監視器
- 讓所有的流量都在 Microsoft Azure 骨幹網路內

如需詳細資訊，請參閱[私人連結的主要優點](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>運作方式

Azure 監視器私人連結範圍是一種群組資源，可將一或多個私人端點 (也就包括包含端點的虛擬網路) 連線到一或多個 Azure 監視器資源。 這些資源包括 Log Analytics 工作區和 Application Insights 元件。

![資源拓撲的圖表](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> 單一 Azure 監視器資源可以屬於多個 AMPLS，但是您無法將單一 VNet 連線到一個以上的 AMPLS。 

## <a name="planning-based-on-your-network"></a>根據您的網路進行規劃

在設定您的 AMPLS 資源之前，請考慮您的網路隔離需求。 評估您的虛擬網路對公用網際網路的存取，以及您的每個 Azure 監視器資源 (也就是 Application Insights 元件和 Log Analytics 工作區) 的存取限制。

> [!NOTE]
> 中樞和輪輻網路或對等互連網路的任何其他拓撲，都可以在中樞 (主要) VNet 和相關的 Azure 監視器資源之間設定 Private Link，而不是在每個 VNet 上設定 Private Link。 如果共用這些網路所使用的 Azure 監視器資源，這就特別有用。 但是，如果您想要讓每個 VNet 都能存取一組個別的監視資源，請為每個網路建立專用 AMPLS 的 Private Link。

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>評估哪些虛擬網路應該連線到私人連結

首先，評估您的虛擬網路 (Vnet) 中，哪一些有存取網際網路的限制。 具有免費網際網路的 Vnet 可能不需要私人連結就能存取您的 Azure 監視器資源。 您的 Vnet 所連線的監視資源可能會限制傳入流量，而且需要私人連結連線 (用於記錄擷取或查詢)。 在這種情況下，即使是可存取公用網際網路的 VNet，還是必須透過私人連結和 AMPLS 與這些資源連線。

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>評估哪些 Azure 監視器資源應該有私人連結

檢閱您的每個 Azure 監視器資源：

- 資源是否只允許從特定 Vnet 上的資源擷取記錄？
- 只有特定 Vnet 上的用戶端才能查詢資源？

如果上述任一問題的答案為「是」，請依照[設定 Log Analytics](#configure-log-analytics)工作區和[設定 Application Insights 元件](#configure-application-insights)所述設定限制，並將這些資源關聯到單一或多個 AMPLS。 應該存取這些監視資源的虛擬網路，必須擁有連線到相關 AMPLS 的私人端點。
請記住，您可以將相同的工作區或應用程式連線到多個 AMPLS，以允許不同的網路觸達它們。

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>依網路協助工具將監視資源群組在一起

因為每個 VNet 只能連線到一個 AMPLS 資源，所以您必須將相同網路可存取的監視資源群組在一起。 管理此群組最簡單的方式，就是為每個 VNet 建立一個 AMPLS，然後選取連線到該網路的資源。 不過，若要減少資源及改善管理，您可能會想要跨網路重複使用 AMPLS。

例如，如果您的內部虛擬網路 VNet1 和 VNet2 應連線到 Workspace1、Workspace2 工作區以及 Application Insights 元件 Application Insights 3，請將這三個資源關聯至相同的 AMPLS。 如果 VNet3 只應存取 Workspace1，請建立另一個 AMPLS 資源，將其關聯至 Workspace1，並與 VNet3 連線，如下圖所示：

![AMPLS A 拓撲圖表](./media/private-link-security/ampls-topology-a-1.png)

![AMPLS B 拓撲圖表](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>考慮限制

規劃 Private Link 設定時，您應該考慮一些限制：

* VNet 只能連接到1個 AMPLS 物件。 這表示 AMPLS 物件必須提供 VNet 應可存取之所有 Azure 監視器資源的存取權。
* Azure 監視器資源 (工作區或 Application Insights 元件) 最多可連接至5個 AMPLSs。
* AMPLS 物件最多可連接至 50 Azure 監視器資源。
* AMPLS 物件最多可連接至10個私人端點。

在下列拓撲中：
* 每個 VNet 都會連接到1個 AMPLS 物件，因此無法連線到其他 AMPLSs。
* AMPLS B 會連接到 2 Vnet：使用其可能的私人端點連接的2/10。
* AMPLS 會連接到2個工作區和1個應用程式深入解析元件：使用3/50 的可能 Azure 監視器資源。
* 工作區2會連接到 AMPLS A 和 AMPLS B：使用2/5 的可能 AMPLS 連接。

![AMPLS 限制的圖表](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>範例連線

首先，建立 Azure 監視器私人連結範圍資源。

1. 在 Azure 入口網站中移至 [建立資源]，搜尋 **Azure 監視器私人連結範圍** 。

   ![尋找 Azure 監視器 Private Link 範圍](./media/private-link-security/ampls-find-1c.png)

2. 按一下 [建立]。
3. 選取訂用帳戶和資源群組。
4. 提供 AMPLS 的名稱。 名稱最好能夠清楚描述領域的用途和安全性界限，以免有人意外中斷網路安全性界限。 例如，AppServerProdTelem。
5. 按一下 [檢閱 + 建立]。 

   ![建立 Azure 監視器 Private Link 範圍](./media/private-link-security/ampls-create-1d.png)

6. 讓驗證通過，然後按一下 [建立]。

## <a name="connect-azure-monitor-resources"></a>連線 Azure 監視器資源

您可以將您的 AMPLS 先連線到私人端點，再連線到 Azure 監視器資源，或是反向操作，但如果您從 Azure 監視器資源開始，連線過程會更快。 以下是我們的做法，將 Azure 監視器 Log Analytics 工作區和 Application Insights 元件連線到 AMPLS。

1. 在您的 Azure 監視器私人連結範圍中，按一下左側功能表中的 [Azure 監視器資源]。 按一下 [新增]  按鈕。
2. 新增工作區或元件。 按一下 [新增] 按鈕會顯示一個對話方塊，您可以在其中選取 Azure 監視器資源。 您可以瀏覽您的訂用帳戶和資源群組，也可以輸入名稱將其篩選出來。 選取工作區或元件，然後按一下 [套用] 將其新增至您的範圍。

    ![選取範圍 UX 的螢幕擷取畫面](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> 若要刪除 Azure 監視器資源，您必須先將這些資源與所連接的任何 AMPLS 物件中斷連接。 無法刪除連線至 AMPLS 的資源。

### <a name="connect-to-a-private-endpoint"></a>連線到私人端點

現在您已將資源連線到您的 AMPLS，請建立私人端點，用來與我們的網路連線。 您可以在 [Azure 入口網站私人連結中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)，或在 Azure 監視器私人連結範圍內執行這項工作，如同此範例中所示。

1. 在您的範圍資源中，按一下左側資源功能表中的 [私人端點連線]。 按一下 [私人端點] 以啟動端點建立程序。 您也可以選取在私人連結中心啟動的連線，然後按一下 [核准] 來核准連線。

    ![私人端點連線 UX 的螢幕擷取畫面](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 挑選訂用帳戶、資源群組、端點的名稱，及其應存在的區域。 區域必須與您要連線的虛擬網路位於相同的區域。

3. 按一下 **[下一步** 資源]。 

4. 在 [資源] 畫面中，

   a. 挑選包含 Azure 監視器私人範圍資源的 [訂用帳戶]。 

   b. [資源類型] 請選擇 [Microsoft.insights/privateLinkScopes]。 

   c. 從 [資源] 下拉式選單，選擇您稍早建立的私人連結範圍。 

   d. 按一下 **下一步組態 >** 。
      ![選取建立私人端點的螢幕擷取畫面](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 在設定窗格上，

   a.    選擇您想要連線到 Azure 監視器資源的 [虛擬網路] 和 [子網路]。 
 
   b.    請在 [與私人 DNS 區域整合] 選擇 [是] ，讓它自動建立新的私人 DNS 區域。 實際的 DNS 區域可能與下列螢幕擷取畫面中顯示的不同。 
   > [!NOTE]
   > 如果您選擇 [ **否** ] 並偏好手動管理 DNS 記錄，請先完成您的 Private Link （包括此私人端點和 AMPLS 設定）。 然後，根據 [Azure 私人端點 DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)設定中的指示來設定您的 DNS。 請務必建立空白記錄，以準備 Private Link 安裝。 您所建立的 DNS 記錄可以覆寫現有的設定，並影響您與 Azure 監視器的連線能力。
 
   c.    按一下 [檢閱 + 建立]。
 
   d.    讓驗證通過。 
 
   e.    按一下頁面底部的 [新增] 。 

    ![選取建立私人端點 2 的螢幕擷取畫面](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

您現在已建立新的私人端點，並已將其連線到此 Azure 監視器私人連結範圍。

## <a name="configure-log-analytics"></a>設定 Log Analytics

移至 Azure 入口網站。 在您的 Log Analytics 工作區資源中，左側會有功能表項目 **網路隔離** 。 您可以從這個功能表控制兩種不同的狀態。 

![Log Analytics 網路隔離](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

首先，您可以將此 Log Analytics 資源連線到您可存取的任何 Azure 監視器私人連結範圍。 按一下 [新增]，然後選取 [Azure 監視器私人連結範圍]。  按一下 [套用] 以與之連線。 所有連線的範圍都會顯示在此畫面中。 進行此連線可以使連線的虛擬網路中的網路流量連線到此工作區。 建立連線的效果與從範圍進行連線相同，如同我們在[連線 Azure 監視器資源](#connect-azure-monitor-resources)中所做的。  

然後，您可以控制如何從上方所列的私人連結範圍外部連線到此資源。 如果您將 [允許擷取的公用網路存取] 設為 [否]，則連線範圍以外的電腦就無法將資料上傳到此工作區。 如果您將 [允許查詢的公用網路存取] 設為 [否]，則範圍以外的電腦就無法存取此工作區中的資料。 該資料包括對活頁簿、儀表板、以查詢 API 為基礎的用戶端體驗、Azure 入口網站中的深入解析等等的存取。 在 Azure 入口網站外執行的體驗，而且查詢 Log Analytics 資料也必須在私人連結的 VNET 內執行。

以這種方式限制存取並不適用于 Azure Resource Manager，因此有下列限制：
* 存取資料-雖然從公用網路封鎖查詢適用于大部分的 Log Analytics 體驗，但有些體驗會透過 Azure Resource Manager 來查詢資料，因此將無法查詢資料，除非將 Private Link 設定套用至 Resource Manager， (功能很快就會推出) 。 例如 Azure 監視器的解決方案、活頁簿和見解，以及 LogicApp 連接器。
* 工作區管理-工作區設定和設定變更 (包括開啟或關閉這些存取設定) 是由 Azure Resource Manager 管理。 使用適當的角色、許可權、網路控制項和審核來限制對工作區管理的存取。 如需詳細資訊，請參閱 [Azure 監視器的角色、權限和安全性](roles-permissions-security.md)。

> [!NOTE]
> 經由[診斷設定](diagnostic-settings.md)上傳至工作區的記錄和計量，是透過安全的私人 Microsoft 通道，不受這些設定控制。

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 解決方案套件下載

若要允許 Log Analytics 代理程式下載解決方案套件，請將適當的完整網域名稱新增至您的防火牆允許清單。 


| 雲端環境 | 代理程式資源 | 連接埠 | 方向 |
|:--|:--|:--|:--|
|Azure 公用     | scadvisorcontent.blob.core.windows.net         | 443 | 輸出
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  輸出
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 輸出

## <a name="configure-application-insights"></a>設定 Application Insights

移至 Azure 入口網站。 在您的 Azure 監視器中，Application Insights 元件資源是左側的 [網路隔離] 功能表項目。 您可以從這個功能表控制兩種不同的狀態。

![Application Insights 網路隔離](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

首先，您可以將此 Application Insights 資源連線到您可存取的 Azure 監視器私人連結範圍。 按一下 [新增]，然後選取 [Azure 監視器私人連結範圍]。 按一下 [套用] 以與之連線。 所有連線的範圍都會顯示在此畫面中。 進行此連線可以使連線的虛擬網路中的網路流量連線到此工作區元件。 建立連線的效果與從範圍進行連線相同，如同我們在[連線 Azure 監視器資源](#connect-azure-monitor-resources)中所做的。 

然後，您可以控制如何從先前所列的私人連結範圍外部連線到此資源。 如果您將 [允許擷取的公用網路存取] 設為 [否]，則連線範圍以外的電腦或 SDK 就無法將資料上傳到此元件。 如果您將 [允許查詢的公用網路存取] 設為 [否]，則範圍以外的電腦就無法存取此 Application Insights 資源中的資料。 該資料包括對 APM 記錄、計量、即時計量串流以及體驗的存取權，例如建立在活頁簿、儀表板、以查詢 API 為基礎的用戶端體驗、Azure 入口網站中的深入解析等等之上的體驗。 

請注意，非入口網站的使用體驗也必須在包含受監視工作負載的私人連結 VNET 內執行。 

您必須將裝載受監視工作負載的資源新增至私人連結。 以下是如何針對 App Service 執行此動作的[文件](../../app-service/networking/private-endpoint.md)。

以這種方式限制存取僅適用於 Application Insights 資源中的資料。 設定變更 (包括開啟或關閉這些存取設定) 是由 Azure Resource Manager 管理。 因此，使用適當的角色、權限、網路控制、稽核，限制對 Resource Manager 的存取。 如需詳細資訊，請參閱 [Azure 監視器的角色、權限和安全性](roles-permissions-security.md)。

> [!NOTE]
> 若要完整保護以工作區運作的 Application Insights，必須同時鎖定對 Application Insights 資源的存取，以及對其基礎 Log Analytics 工作區的存取。
>
> 程式碼層級診斷 (分析工具/偵錯工具) 需要您提供自己的儲存體帳戶以支援 private link。 以下是如何進行此動作的 [檔](../app/profiler-bring-your-own-storage.md) 。

## <a name="use-apis-and-command-line"></a>使用 API 和命令列

您可以使用 Azure Resource Manager 範本和命令列介面，將先前所述的程序自動化。

若要建立及管理私人連結範圍，請使用 [az monitor private-link-scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest)。 使用此命令，您可以建立範圍、將 Log Analytics 工作區和 Application Insights 元件建立關聯、新增/移除/核准私人端點。

若要管理網路存取，請在 [Log Analytics 工作區](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest)或 [Application Insights 元件](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest)上使用 `[--ingestion-access {Disabled, Enabled}]` 和 `[--query-access {Disabled, Enabled}]` 旗標。

## <a name="collect-custom-logs-over-private-link"></a>收集自訂記錄檔 Private Link

擷取自訂記錄的程序會使用儲存體帳戶。 根據預設，會使用服務管理的儲存體帳戶。 不過，若要在私人連結上擷取自訂記錄，您必須使用您自己的儲存體帳戶，並將其與 Log Analytics 工作區建立關聯。 請細看如需如何使用[命令列](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest)設定這類帳戶。

如需有關自備儲存體帳戶的詳細資訊，請參閱[客戶自備儲存體帳戶的記錄擷取](private-storage.md)。

## <a name="restrictions-and-limitations"></a>限制事項

### <a name="agents"></a>代理程式

Windows 和 Linux 代理程式的最新版本必須用於私人網路，才能安全地內嵌至 Log Analytics 工作區。 較舊的版本無法在私人網路中上傳監視資料。

**Log Analytics Windows 代理程式**

使用 Log Analytics 代理程式版本10.20.18038.0 或更新版本。

**Log Analytics Linux 代理程式**

使用代理程式 1.12.25 或更新版本。 如果您無法這樣做，請在您的 VM 上執行下列命令。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure 入口網站

若要使用 Azure 監視器的入口網站體驗，例如 Application Insights 和 Log Analytics，您必須允許在私人網路上存取 Azure 入口網站和 Azure 監視器擴充功能。 將 **AzureActiveDirectory** 、 **AzureResourceManager** 、 **AzureFrontDoor. FirstParty** 和 **AzureFrontDoor. 前端**[服務標記](../../firewall/service-tags.md)新增至您的網路安全性群組。

### <a name="programmatic-access"></a>以程式設計方式存取

若要在私人網路上使用 REST API、 [CLI](/cli/azure/monitor?view=azure-cli-latest) 或具有 Azure 監視器的 PowerShell，請在您的防火牆新增 **AzureActiveDirectory** 和 **AzureResourceManager** [服務標記](../../virtual-network/service-tags-overview.md)。

新增這些標記可讓您執行一些動作，例如查詢記錄資料、建立和管理 Log Analytics 工作區和 Application Insights 元件。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>從內容傳遞網路下載 Application Insights SDK

在您的指令碼中搭配 JavaScript 程式碼，讓瀏覽器不會嘗試從 CDN 下載程式碼。 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) 上有提供範例。

### <a name="browser-dns-settings"></a>瀏覽器 DNS 設定

如果您是透過 Private Link 連線到 Azure 監視器資源，則這些資源的流量必須通過您網路上設定的私人端點。 若要啟用私人端點，請更新您的 DNS 設定，如 [連接到私人端點](#connect-to-a-private-endpoint)中所述。 有些瀏覽器會使用自己的 DNS 設定，而不是您所設定的設定。 瀏覽器可能會嘗試連線到 Azure 監視器的公用端點，並完全略過 Private Link。 確認您的瀏覽器設定不會覆寫或快取舊的 DNS 設定。 

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [私人儲存體](private-storage.md)
