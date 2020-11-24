---
title: 建立連線監視器-Azure 入口網站
titleSuffix: Azure Network Watcher
description: 本文說明如何使用 Azure 入口網站在連線監視器中建立監視。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: a6ba9c02b384c1b30977d962659983d600c7b2ac
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545715"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>使用 Azure 入口網站在連線監視器中建立監視

瞭解如何使用連線監視器來監視資源之間的通訊。 本文說明如何使用 Azure 入口網站來建立監視。 連接監視器支援混合式和 Azure 雲端部署。


## <a name="before-you-begin"></a>開始之前 

在您使用連線監視器建立的連線監視器中，您可以新增內部部署機器和 Azure Vm 作為來源。 這些連線監視器也可以監視端點的連線能力。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

以下是一些可讓您開始使用的定義：

* **連接監視資源**。 特定區域的 Azure 資源。 下列所有實體都是連線監視器資源的屬性。
* **端點**。 參與連接檢查的來源或目的地。 端點的範例包括：
    * Azure Vm。
    * Azure 虛擬網路。
    * Azure 子網。
    * 內部部署代理程式。
    * 內部部署子網。
    * 包含多個子網的內部部署自訂網路。
    * Url 和 Ip。
* **測試** 設定。 測試的通訊協定特定設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率及其他參數。
* **測試群組**。 包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試**。 來源端點、目的地端點和測試設定的組合。 測試是可使用監視資料的最細微層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回行程時間。

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="顯示連接監視器並定義測試群組與測試之間關聯性的圖表。":::


## <a name="create-a-connection-monitor"></a>建立連線監視

若要使用 Azure 入口網站在連線監視器中建立監視：

1. 在 Azure 入口網站首頁上，移至 **網路** 監看員。
1. 在左窗格的 [ **監視** ] 區段中，選取 [連線 **監視器**]。

   您將會看到在連線監視器中建立的所有連線監視器。 若要查看在傳統連線監視器中建立的連線監視器，請移至 [連線 **監視器** ] 索引標籤。

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="顯示連線監視器中所建立連線監視器的螢幕擷取畫面。":::
   
    
1. 在 [ **連接監視器** ] 儀表板的左上角，選取 [ **建立**]。

   

1. 在 [ **基本** ] 索引標籤上，輸入連線監視的資訊： 
   * **連接監視器名稱**：輸入連線監視的名稱。 使用適用于 Azure 資源的標準命名規則。
   * **訂** 用帳戶：為您的連線監視器選取訂用帳戶。
   * **區域**：為您的連線監視器選取區域。 您只能選取在此區域中建立的來源 Vm。
   * **工作區** 設定：選擇自訂工作區或預設工作區。 您的工作區會保存您的監視資料。
       * 若要使用預設工作區，請選取核取方塊。 
       * 若要選擇自訂工作區，請清除此核取方塊。 然後選取自訂工作區的訂用帳戶和區域。 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="顯示 [連線監視器] 中 [基本] 索引標籤的螢幕擷取畫面。":::
   
1. 選取索引標籤底部的 **[下一步：測試群組]**。

1. 在您的測試群組中新增來源、目的地和測試設定。 若要瞭解如何設定您的測試群組，請參閱 [在連接監視器中建立測試群組](#create-test-groups-in-a-connection-monitor)。 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="顯示 [連線監視器] 中 [測試群組] 索引標籤的螢幕擷取畫面。":::

1. 選取索引標籤底部的 **[下一步：建立警示]**。 若要瞭解如何建立警示，請參閱 [在連線監視器中建立警示](#create-alerts-in-connection-monitor)。

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="顯示 [建立警示] 索引標籤的螢幕擷取畫面。":::

1. 選取索引標籤底部的 **[下一步：檢查 + 建立**]。

1. 在 [ **審核 + 建立** ] 索引標籤上，檢查基本資訊和測試群組，然後再建立連線監視器。 如果您需要編輯連線監視器，可以回到個別的索引標籤。 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="顯示連線監視器中 [審核 + 建立] 索引標籤的螢幕擷取畫面。":::
   > [!NOTE] 
   > [ **審查 + 建立** ] 索引標籤會顯示連線監視器階段在每月的成本。 目前，[ **目前的成本]/[月** ] 資料行不會顯示任何費用。 當連線監視器正式推出時，此欄會顯示每月費用。 
   > 
   > 即使在連線監視器階段，也會收取 Log Analytics 內嵌費用。

1. 當您準備好建立連線監視器時，請在 [ **審核 + 建立** ] 索引標籤的底部，選取 [ **建立**]。

連線監視器會在背景中建立連接監視器資源。

## <a name="create-test-groups-in-a-connection-monitor"></a>在連接監視器中建立測試群組

連線監視器中的每個測試群組都包含在網路參數上進行測試的來源和目的地。 它們會針對失敗的檢查百分比，以及測試設定的 RTT 進行測試。

在 [Azure 入口網站] 中，若要在連線監視器中建立測試群組，請指定下欄欄位的值：

* **停用測試群組**：您可以選取此核取方塊，以針對測試群組指定的所有來源和目的地停用監視。 預設會清除此選取專案。
* **名稱**：命名您的測試群組。
* **來源**：如果已安裝代理程式，您可以將 Azure vm 和內部部署機器指定為來源。 若要瞭解如何為您的來源安裝代理程式，請參閱 [安裝監視代理](./connection-monitor-overview.md#install-monitoring-agents)程式。
   * 若要選擇 Azure 代理程式，請選取 [ **azure 端點** ] 索引標籤。在這裡，您只會看到系結至您在建立連線監視器時所指定之區域的 Vm。 根據預設，Vm 會分組為其所屬的訂用帳戶。 這些群組會折迭。 
   
       您可以從 **訂** 用帳戶層級向下切入至階層中的其他層級：

      **訂**  >  用帳戶 **資源群組**  > **VNET**  > **子網**  > **具有代理程式的 vm**

      您也可以變更 [ **群組依據** ] 選取器，以從任何其他層級啟動樹狀結構。 例如，如果您依虛擬網路分組，您會看到 vm 在階層 **VNET**  >  **子網**  >  **vm** 中有代理程式。

       當您選取 VNET、子網或單一 VM 時，對應的資源識別碼會設定為端點。 根據預設，選取的 VNET 或子網中具有 Azure 網路監看員延伸模組的所有 Vm 都會參與監視。 若要減少範圍，請選取特定子網或代理程式，或變更 [領域] 屬性的值。 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="顯示 [新增來源] 窗格和 [連線監視器] 中 [Azure 端點] 索引標籤的螢幕擷取畫面。":::

   * 若要選擇內部部署代理程式，請選取 [ **非 Azure 端點** ] 索引標籤。依預設，代理程式依區域分組為工作區。 這些工作區都已設定網路效能監控。 
   
       如果您需要將網路效能監控新增至您的工作區，請從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)取得。 如需有關如何新增網路效能監控的詳細資訊，請參閱 [Azure 監視器中的監視解決方案](../azure-monitor/insights/solutions.md)。 
   
       在 [ **建立連線監視器**] 下的 [ **基本** ] 索引標籤上，會選取預設區域。 如果您變更區域，您可以從新區域中的工作區選擇代理程式。 您可以選取一或多個代理程式或子網。 在 **子網** 視圖中，您可以選取特定 ip 進行監視。 如果您新增多個子網，將會建立名為 **OnPremises_Network_1** 的自訂內部部署網路。 您也可以將 [ **群組依據** ] 選取器變更為 [依代理程式分組]。

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="顯示 [新增來源] 窗格和 [連線監視器] 中 [非 Azure 端點] 索引標籤的螢幕擷取畫面。":::

   * 若要選擇最近使用的端點，您可以使用 [最近使用的 **端點** ] 索引標籤 
   
   * 當您完成來源的設定時，請選取索引標籤底部的 [ **完成** ]。您仍然可以在 [ **建立測試群組** ] 視圖中選取端點，以編輯端點名稱之類的基本屬性。 

* **目的地**：您可以將虛擬 IP、URL 或 FQDN 指定為目的地，以監視對 Azure VM、內部部署機器或任何端點的連線， (公用 IP、URL 或 FQDN) 。 在單一測試群組中，您可以新增 Azure Vm、內部部署機器、Office 365 Url、Dynamics 365 Url 和自訂端點。

    * 若要選擇 Azure Vm 作為目的地，請選取 [ **azure 端點**] 索引標籤。根據預設，Azure Vm 會分組為訂用帳戶階層，其位於您在 [**基本**] 索引標籤的 [**建立連線監視器**] 下選取的區域中。您可以變更區域，並從新區域選擇 Azure Vm。 然後，您可以向下切入至階層中的其他 **層級，** 就如同設定來源 Azure 端點時一樣。

      您可以選取 Vnet、子網或單一 Vm，就像您在設定來源 Azure 端點時所能做的一樣。 當您選取 VNET、子網或單一 VM 時，對應的資源識別碼會設定為端點。 依預設，所選 VNET 或子網中具有網路監看員擴充功能的所有 Vm 都會參與監視。 若要減少範圍，請選取特定子網或代理程式，或變更 [領域] 屬性的值。 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<螢幕擷取畫面，顯示 [新增目的地] 窗格和 [Azure 端點] 索引標籤。 >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<螢幕擷取畫面，顯示訂用帳戶層級的 [新增目的地] 窗格。 >":::
       
    
    * 若要選擇非 Azure 代理程式作為目的地，請選取 [ **非 azure 端點** ] 索引標籤。依預設，代理程式依區域分組為工作區。 所有這些工作區都已設定網路效能監控。 
    
      如果您需要將網路效能監控新增至您的工作區，請從 Azure Marketplace 取得。 如需有關如何新增網路效能監控的詳細資訊，請參閱 [Azure 監視器中的監視解決方案](../azure-monitor/insights/solutions.md)。 

      在 [ **建立連線監視器**] 下的 [ **基本**] 索引標籤上   ，會選取預設區域。 如果您變更區域，您可以從新區域中的工作區選擇代理程式。 您可以選取一或多個代理程式或子網。 在 **子網** 視圖中，您可以選取特定 ip 進行監視。 如果您新增多個子網，將會建立名為 **OnPremises_Network_1** 的自訂內部部署網路。  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="顯示 [新增目的地] 窗格和 [非 Azure 端點] 索引標籤的螢幕擷取畫面。":::
    
    * 若要選擇公用端點作為目的地，請選取 [ **外部地址** ] 索引標籤。端點清單包括 Office 365 測試 Url 和 Dynamics 365 測試 Url （依名稱分組）。 您也可以選擇在相同連線監視器中，于其他測試群組中建立的端點。 
    
        若要加入端點，請選取右上角的 [ **新增端點**]。 然後提供端點名稱和 URL、IP 或 FQDN。

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="螢幕擷取畫面，顯示在連線監視器中將公用端點新增為目的地的位置。":::

    * 若要選擇最近使用的端點，請移至 [ **最近的端點**] 索引標籤   。
    * 當您完成選擇目的地時，請選取 [ **完成**]。 您仍然可以在 [ **建立測試群組** ] 視圖中選取端點，以編輯端點名稱之類的基本屬性。 

* **測試** 設定：您可以將一或多個測試設定加入至測試群組。 使用 [ **新** 設定] 索引標籤建立新的測試設定。或者，從 [ **選擇現有** 的] 索引標籤，在同一個連線監視器中的另一個測試群組加入測試設定。

    * **測試設定名稱**：命名測試設定。
    * **通訊協定**：選取 [ **TCP**]、[ **ICMP**] 或 [ **HTTP**]。 若要將 HTTP 變更為 HTTPS，請選取 [ **HTTP** ] 作為通訊協定，然後選取 [ **443** ] 作為埠。
        * **建立 TCP 測試** 設定：只有在 [**通訊協定**] 清單中選取 [ **HTTP** ] 時，才會顯示此核取方塊。 選取此核取方塊，以建立另一個使用您在設定中其他位置所指定之相同來源和目的地的測試設定。 新測試設定的名稱為 **\<name of test configuration> _networkTestConfig**。
        * **停用追蹤路由**：當通訊協定為 TCP 或 ICMP 時，此核取方塊會套用。 選取此方塊以防止來源探索拓撲和逐躍點的 RTT。
    * **目的地埠**：您可以提供所選的目的地埠。
        * **接聽埠**：當通訊協定為 TCP 時，此核取方塊會套用。 選取此核取方塊，以開啟所選的 TCP 埠（如果尚未開啟）。 
    * **測試頻率**：在此清單中，指定來源將在您指定的通訊協定和埠上偵測目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘或30分鐘。 選取 [ **自訂** ]，輸入介於30秒到30分鐘之間的另一個頻率。 來源會依據您選擇的值，測試目的地的連線能力。 例如，如果您選取30秒，則來源會在每隔30秒的期間內檢查目的地至少一次的連線能力。
    * **成功閾值**：您可以設定下列網路參數的閾值：
       * **檢查失敗**：設定當來源使用您指定的準則來檢查目的地的連接時，可能會失敗的檢查百分比。 針對 TCP 或 ICMP 通訊協定，失敗的檢查百分比可等同于為封包遺失的百分比。 若為 HTTP 通訊協定，此值代表未收到回應的 HTTP 要求百分比。
       * **來回行程時間**：設定以毫秒為單位的 RTT （以毫秒為單位），以供來源透過測試設定連接到目的地所需的時間。
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="顯示在連線監視器中設定測試設定之位置的螢幕擷取畫面。":::
       
## <a name="create-alerts-in-connection-monitor"></a>在連線監視器中建立警示

您可以根據測試設定中設定的臨界值，設定失敗的測試警示。

在 Azure 入口網站中，若要建立連線監視器的警示，請指定這些欄位的值： 

- **建立警示**：您可以選取此核取方塊，在 Azure 監視器中建立計量警示。 當您選取此核取方塊時，將會啟用其他欄位以進行編輯。 警示的額外費用會根據 [警示的定價](https://azure.microsoft.com/pricing/details/monitor/)而適用。 

- **範圍**  > **資源**  > 階層 **：系統** 會根據 [**基本**] 索引標籤上指定的值自動填入這些值。

- **條件名稱**：在度量上建立警示 `Test Result(preview)` 。 當連線監視測試的結果失敗時，將會引發警示規則。 

- **動作組名**：您可以直接輸入電子郵件，也可以透過動作群組建立警示。 如果您直接輸入電子郵件，則會建立名稱為 **NPM Email ActionGroup** 的動作群組。 電子郵件識別碼會新增至該動作群組。 如果您選擇使用動作群組，則必須選取先前建立的動作群組。 若要瞭解如何建立動作群組，請參閱 [Azure 入口網站中的建立動作群組](../azure-monitor/platform/action-groups.md)。 警示建立之後，您就可以 [管理您的警示](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal)。 

- **警示規則名稱**：連接監視的名稱。

- **在建立時啟用規則**：選取此核取方塊以根據條件啟用警示規則。 如果您想要在不啟用的情況下建立規則，請停用此核取方塊。 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="顯示 [連線監視器] 中 [建立警示] 索引標籤的螢幕擷取畫面。":::

## <a name="scale-limits"></a> 調整限制

連線監視器具有下列規模限制：

* 每個區域每個訂用帳戶的連線監視器上限：100
* 每個連接監視器的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限： 2 via Azure 入口網站

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何分析監視資料和設定警示](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)。
* 瞭解 [如何診斷網路中的問題](./connection-monitor-overview.md#diagnose-issues-in-your-network)。