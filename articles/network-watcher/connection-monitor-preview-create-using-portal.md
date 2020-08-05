---
title: 建立連線監視器預覽-Azure 入口網站
titleSuffix: Azure Network Watcher
description: 瞭解如何使用 Azure 入口網站建立 (預覽) 的連線監視器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567894"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>使用 Azure 入口網站建立 (預覽) 的連線監視器

瞭解如何 (預覽) 建立連線監視器，以監視您的資源之間使用 Azure 入口網站的通訊。 它支援混合式和 Azure 雲端部署。

## <a name="before-you-begin"></a>開始之前 

在您于連線監視器中建立的連線監視器 (預覽) 中，您可以將內部部署機器和 Azure Vm 新增為來源。 這些連接監視器也可以監視端點的連線。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連線監視器 (預覽) 包含下列實體：


* 連線**監視資源**–區域特定的 Azure 資源。 下列所有實體都是連接監視器資源的屬性。
* **端點**–參與連線性檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試**設定–適用于測試的通訊協定專屬設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率和其他參數。
* **測試群組**–包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試**–來源端點、目的地端點和測試設定的組合。 測試是可用的監視資料最細微的層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回時間。

    ![圖表顯示連接監視器，定義測試群組與測試之間的關聯性](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>建立步驟

若要使用 Azure 入口網站建立 (預覽) 的連線監視器，請遵循下列步驟：

1. 在 [Azure 入口網站] 首頁上，移至 [**網路監看員**]。
1. 在左側的 [**監視**] 區段中，選取 [連線**監視器] (預覽**] [) ]。
1. 您會看到所有在連線監視器中建立的連線監視器 (預覽) 。 若要查看在傳統連線監視器中建立的連線監視器，請移至 [**連接監視器**] 索引標籤。

    ![顯示連線監視器中建立的連線監視器 (預覽的螢幕擷取畫面) ](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. 在 [連線**監視器] (預覽) ** ] 儀表板左上角，選取 [**建立**]。
1. 在 [**基本**] 索引標籤上，輸入連線監視器的資訊：
   * 連線**監視名稱**–新增連接監視器的名稱。 使用 Azure 資源的標準命名規則。
   * **訂**用帳戶–選擇連線監視的訂用帳戶。
   * **區域**–選擇連接監視的區域。 您只能選取在此區域中建立的來源 Vm。
   * **工作區**設定-您的工作區會保留您的監視資料。 您可以使用自訂工作區或預設工作區。 
       * 若要使用預設工作區，請選取核取方塊。 
       * 若要選擇自訂工作區，請清除核取方塊。 然後選擇您自訂工作區的訂用帳戶和區域。 
1. 在索引標籤的底部，選取 **[下一步：測試群組]**。

   ![顯示連線監視器中 [基本] 索引標籤的螢幕擷取畫面](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在 [**測試群組**] 索引標籤上，選取 [ **+ 測試群組**]。 若要設定您的測試群組，請參閱[在連接監視器中建立測試群組](#create-test-groups-in-a-connection-monitor)。 
1. 在索引標籤的底部，選取 **[下一步：審查 + 建立]** 以審查您的連線監視器。

   ![顯示 [測試群組] 索引標籤和窗格的螢幕擷取畫面，您可以在其中加入測試群組詳細資料](./media/connection-monitor-2-preview/create-tg.png)

1. 在 [**審查 + 建立**] 索引標籤上，建立連線監視器之前，請先查看基本資訊和測試群組。 如果您需要編輯連接監視器：
   * 若要編輯基本的詳細資料，請選取鉛筆圖示。
   * 若要編輯測試群組，請選取它。

   > [!NOTE] 
   > [**審核**] 和 [建立] 索引標籤會顯示連線監視器預覽階段期間每個月的成本。 目前，[**目前的成本**] 資料行不會顯示任何費用。 當連線監視器正式推出時，此欄會顯示每月費用。 
   > 
   > 即使在連線監視器預覽階段中，也會收取 Log Analytics 的內嵌費用。

1. 當您準備好建立連線監視器時，請在 [**審核 + 建立**] 索引標籤底部選取 [**建立**]。

   ![連線監視器的螢幕擷取畫面，其中顯示 [審查 + 建立] 索引標籤](./media/connection-monitor-2-preview/review-create-cm.png)

連接監視器 (預覽) 會在背景建立連線監視器資源。

## <a name="create-test-groups-in-a-connection-monitor"></a>在連接監視器中建立測試群組

連接監視器中的每個測試群組都會包含在網路參數上進行測試的來源和目的地。 它們會測試失敗的檢查百分比，以及測試設定的 RTT。

在 [Azure 入口網站] 中，若要在連接監視器中建立測試群組，請指定下欄欄位的值：

* **停用測試群組**–您可以選取此欄位，針對測試群組指定的所有來源和目的地停用監視。 預設會清除此選取專案。
* **名稱**-為您的測試群組命名。
* **來源**–您可以將 Azure vm 和內部部署機器指定為來源（如果代理程式已安裝在這些電腦上）。 若要為您的來源安裝代理程式，請參閱[安裝監視代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)程式。
   * 若要選擇 Azure 代理程式，請選取 [ **Azure 代理**程式] 索引標籤。在這裡，您只會看到已系結至您在建立連線監視器時所指定之區域的 Vm。 根據預設，Vm 會分組成其所屬的訂用帳戶。 這些群組會折迭。 
   
       您可以從訂用帳戶層級向下切入至階層中的其他層級：

      **訂**  >  用帳戶**資源群組**  > **Vnet**  > **子網**  > **具有代理程式的 vm**

      您也可以變更 [**群組依據**] 欄位的值，從任何其他層級啟動樹狀結構。 例如，如果您依虛擬網路分組，您會看到階層中有代理程式的 vm 會**vnet**  >  **子網**  >  **vm 與代理**程式。

      ![連線監視器的螢幕擷取畫面，其中顯示 [新增來源] 面板和 [Azure 代理程式] 索引標籤](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 若要選擇內部部署代理程式，請選取 [**非 Azure 代理**程式] 索引標籤。根據預設，代理程式會依區域分組到工作區。 所有這些工作區都已設定網路效能監控解決方案。 
   
       如果您需要將網路效能監控新增至您的工作區，請從[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)取得。 如需如何新增網路效能監控的詳細資訊，請參閱[Azure 監視器中的監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在 [**建立連線監視器**] 視圖的 [**基本**] 索引標籤上，選取 [預設區域]。 如果您變更區域，則可以從新區域中的工作區選擇 [代理程式]。 您也可以將 [**分組方式**] 欄位的值變更為 [依子網群組]。

      ![連線監視器的螢幕擷取畫面，其中顯示 [新增來源] 面板和 [非 Azure 代理程式] 索引標籤](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 若要查看您選取的 Azure 和非 Azure 代理程式，請移至 [**審查**] 索引標籤。

      ![連線監視器的螢幕擷取畫面，其中顯示 [新增來源] 面板和 [審核] 索引標籤](./media/connection-monitor-2-preview/review-sources.png)

   * 當您完成來源設定時，請在 [**新增來源**] 面板底部選取 [**完成**]。

* **目的地**–您可以將 Azure vm 或任何端點 (公用 IP、URL 或) FQDN 的連線，指定為目的地，以監視其連線能力。 在單一測試群組中，您可以新增 Azure Vm、Office 365 Url、Dynamics 365 Url 和自訂端點。

    * 若要選擇 Azure Vm 作為目的地，請選取 [ **Azure vm** ] 索引標籤。根據預設，Azure Vm 會分組成一個訂用帳戶階層，此階層位於您在**建立連線監視器**視圖的 [**基本**] 索引標籤上選取的相同區域中。您可以變更區域，並從新選取的區域中選擇 [Azure Vm]。 接著，您可以從訂用帳戶層級向下切入至階層中的其他層級（例如 Azure 代理程式等級）。

       ![[新增目的地] 窗格的螢幕擷取畫面，其中顯示 [Azure Vm] 索引標籤](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![[新增目的地] 窗格的螢幕擷取畫面，其中顯示訂用帳戶層級](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 若要選擇端點作為目的地，請選取 [**端點**] 索引標籤。端點清單包含 Office 365 測試 Url 和 Dynamics 365 測試 Url （依名稱分組）。 除了這些端點之外，您還可以在相同的連線監視器中選擇在其他測試群組中建立的端點。 
    
        若要加入新的端點，請在右上角選取 [ **+ 端點**]。 然後提供端點名稱和 URL、IP 或 FQDN。

       ![螢幕擷取畫面，顯示在連接監視器中新增端點作為目的地的位置](./media/connection-monitor-2-preview/add-endpoints.png)

    * 若要檢查您所選擇的 Azure Vm 和端點，請選取 [**審查**] 索引標籤。
    * 當您完成選擇目的地時，請選取 [**完成**]。

* **測試**設定–您可以將測試群組中的測試設定產生關聯。 Azure 入口網站只允許每個測試群組有一個測試設定，但您可以使用 ARMClient 來新增更多。

    * **名稱**–測試設定的名稱。
    * **通訊協定**–選擇 [TCP]、[ICMP] 或 [HTTP]。 若要將 HTTP 變更為 HTTPS，請選取 [ **HTTP** ] 作為通訊協定，然後選取 [ **443** ] 作為 [埠]。
        * **建立網路測試**設定-只有當您在 [**通訊協定**] 欄位中選取 [ **HTTP** ] 時，才會顯示此核取方塊。 選取此方塊來建立另一個測試設定，以使用您在設定中其他位置所指定的相同來源和目的地。 新建立的測試設定會命名為 `<the name of your test configuration>_networkTestConfig` 。
        * **停用追蹤路由**–此欄位適用于通訊協定為 TCP 或 ICMP 的測試群組。 選取此方塊，可防止來源探索拓撲和逐躍點的 RTT。
    * **目的地埠**–您可以使用您選擇的目的地埠來自訂此欄位。
    * **測試頻率**–使用此欄位可選擇來源將在您指定的通訊協定和埠上 ping 目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘或30分鐘。 來源會根據您所選擇的值，測試目的地的連線能力。  例如，如果您選取30秒，則來源至少會在30秒的期間內檢查目的地的連線能力。
    * **成功閾值**–您可以設定下列網路參數的閾值：
       * **檢查失敗**–設定當來源使用您指定的準則來檢查目的地的連線時，可能會失敗的檢查百分比。 針對 TCP 或 ICMP 通訊協定，失敗檢查的百分比可以等同于至封包遺失的百分比。 若為 HTTP 通訊協定，此欄位代表未收到回應的 HTTP 要求百分比。
       * **來回時間**–設定 RTT （以毫秒為單位），以瞭解來源在測試設定上連接到目的地所需的時間。
    
       ![螢幕擷取畫面，顯示在連接監視器中設定測試設定的位置](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>調整限制

連接監視器具有下列調整限制：

* 每個訂用帳戶每個區域的連線監視器上限：100
* 每個連線監視的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限：2透過 Azure 入口網站

## <a name="next-steps"></a>後續步驟

* 瞭解[如何分析監視資料及設定警示](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 瞭解[如何診斷網路中的問題](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
