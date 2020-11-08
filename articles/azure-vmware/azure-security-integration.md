---
title: 使用 Azure 資訊安全中心整合來保護您的 Azure VMware 解決方案 Vm
description: 瞭解如何從 Azure 資訊安全中心中的單一儀表板，使用 Azure 的原生安全性工具來保護您的 Azure VMware 解決方案 Vm。
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 53669f2988a7ff7ab1150b155a65c7a187c6f1c8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370151"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>使用 Azure 資訊安全中心整合來保護您的 Azure VMware 解決方案 Vm

Azure 原生安全性工具提供安全的基礎結構，適用于 Azure、Azure VMware 解決方案和內部部署虛擬機器 (Vm) 的混合式環境。 本文說明如何設定適用于混合式環境安全性的 Azure 工具。 您將使用各種工具來識別和解決不同類型的威脅。

## <a name="azure-native-services"></a>Azure 原生服務

以下是每個 Azure 原生服務的快速摘要：

- **Log Analytics 工作區：** Log Analytics 工作區是儲存記錄資料的唯一環境。 每個工作區都有自己的資料存放庫和設定。 資料來源和解決方案會設定為將其資料儲存在特定工作區中。
- **Azure 資訊安全中心：** Azure 資訊安全中心是統一的基礎結構安全性管理系統。 它可強化資料中心的安全性狀態，並為雲端或內部部署中的混合式工作負載提供先進的威脅防護。
- **Azure Sentinel：** Azure Sentinel 是雲端原生的安全性資訊事件管理 (SIEM) 和安全性協調流程自動化回應 (SOAR) 解決方案。 它可跨環境提供智慧型安全性分析和威脅情報。 它是警示偵測、威脅可見度、主動式搜尋和威脅回應的單一解決方案。

## <a name="topology"></a>拓撲

![顯示 Azure 整合式安全性架構的圖表。](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics 代理程式可讓您從 Azure、Azure VMware 解決方案和內部部署 Vm 收集記錄資料。 記錄資料會傳送至 Azure 監視器記錄，並儲存在 Log Analytics 工作區中。 您可以針對新的和現有的 Vm，使用啟用 Arc 的伺服器 [VM 延伸模組支援](../azure-arc/servers/manage-vm-extensions.md) 來部署 Log Analytics 代理程式。 

Log Analytics 工作區收集記錄之後，您就可以使用 Azure 資訊安全中心來設定 Log Analytics 工作區。 Azure 資訊安全中心會評估 Azure VMware 解決方案 Vm 的弱點狀態，並針對任何重大弱點引發警示。 例如，它會評估遺失的作業系統修補程式、安全性錯誤配置和 [endpoint protection](../security-center/security-center-services.md)。

您可以使用 Azure Sentinel 來設定 Log Analytics 工作區，以進行警示偵測、威脅可見度、主動式搜尋和威脅回應。 在上圖中，Azure 資訊安全中心使用 Azure 資訊安全中心連接器連接到 Azure Sentinel。 Azure 資訊安全中心會將環境弱點轉送到 Azure Sentinel，以建立事件並與其他威脅對應。 您也可以建立排程的規則查詢來偵測不必要的活動，並將它轉換成事件。

## <a name="benefits"></a>優點

- Azure 原生服務可用於 Azure、Azure VMware 解決方案和內部部署服務中的混合式環境安全性。
- 使用 Log Analytics 工作區，您可以將資料或記錄檔收集到單一點，並將相同的資料呈現給不同的 Azure 原生服務。
- Azure 資訊安全中心提供了安全性功能，例如檔案完整性監視、無檔案攻擊偵測、作業系統修補程式評估、安全性錯誤配置評估，以及 endpoint protection 評定。
- Azure Sentinel 可讓您：
    - 在內部部署和多個雲端中的所有使用者、裝置、應用程式和基礎結構之間，收集雲端規模的資料。
    - 偵測先前未偵測到的威脅。
    - 使用人工智慧調查威脅，並大規模搜尋可疑的活動。
    - 使用內建的常見工作協調流程和自動化，快速回應事件。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

您將需要 Log Analytics 工作區，以收集來自各種來源的資料。 請參閱 [從 Azure 入口網站建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)中的步驟。 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>部署安全性中心並設定 Azure VMware 解決方案 Vm

Azure 資訊安全中心是預先設定的工具，不需要部署。 在 Azure 入口網站中，搜尋「 **安全性中心** 」並選取它。

### <a name="enable-azure-defender"></a>啟用 Azure Defender

Azure Defender 在內部部署和雲端中，將 Azure 資訊安全中心的先進威脅防護延伸到混合式工作負載。 因此，若要保護您的 Azure VMware 解決方案 Vm，您將需要啟用 Azure Defender。 

1. 在 [安全性中心] 中，選取 [ **快速入門** ]。

2. 選取 [ **升級** ] 索引標籤，然後選取您的訂用帳戶或工作區。 

3. 選取 [升級] 以啟用 Azure Defender。

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>將 Azure VMware 解決方案 Vm 新增至安全中心

1. 在 Azure 入口網站中，搜尋 **Azure Arc** 並加以選取。

2. 在 [資源] 底下選取 [ **伺服器** ]，然後選取 [ **+ 新增** ]。

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="顯示 Azure Arc 伺服器] 頁面的螢幕擷取畫面，可將 Azure VMware 解決方案 VM 新增至 Azure。":::

3. 選取 [ **產生腳本** ]。
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Azure Arc 頁面的螢幕擷取畫面，其中顯示使用互動式腳本新增伺服器的選項。"::: 
 
4. 在 [ **必要條件** ] 索引標籤上選取 **[下一步** ]。

5. 在 [ **資源詳細資料** ] 索引標籤上，填寫下列詳細資料： 
    - 訂用帳戶
    - 資源群組
    - 區域 
    - 作業系統
    - Proxy 伺服器詳細資料
    
    然後選取 **[下一步：標記]** 。

6. 在 [ **標記** ] 索引標籤上選取 **[下一步]** 。

7. 在 [ **下載並執行腳本** ] 索引標籤上，選取 [ **下載** ]。

8. 指定您的作業系統，並在 Azure VMware 解決方案 VM 上執行腳本。

## <a name="view-recommendations-and-passed-assessments"></a>查看建議和通過的評量

1. 在 Azure 資訊安全中心中，從左窗格選取 [ **清查** ]。

2. 針對 [資源類型]，選取 [ **伺服器-Azure Arc** ]。
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Azure 資訊安全中心清查] 頁面的螢幕擷取畫面，其中顯示 [資源類型] 下選取的伺服器 Azure Arc。":::

3. 選取您資源的名稱。 頁面隨即開啟，其中顯示資源的安全性健康情況詳細資料。

4. 在 [ **建議清單** ] 底下，選取 [ **建議** ]、[已 **通過的評定** ] 和 [ **無法使用的評估** ] 索引標籤，

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="顯示安全性建議和評量 Azure 資訊安全中心的螢幕擷取畫面。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>部署 Azure Sentinel 工作區

Azure Sentinel 是以 Log Analytics 工作區為基礎。 上架 Azure Sentinel 的第一個步驟是選取您想要用於該用途的 Log Analytics 工作區。

1. 在 Azure 入口網站中，搜尋 **Azure Sentinel** ，然後選取它。

2. 在 [Azure Sentinel 工作區] 頁面上，選取 [ **+ 新增** ]。

3. 選取 Log Analytics 工作區，然後選取 [ **新增** ]。

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>在 Azure VMware 解決方案 Vm 上啟用資料收集器的安全性事件

現在您已準備好將 Azure Sentinel 與您的資料來源（在此案例中為安全性事件）進行連線。

1. 在 [Azure Sentinel 工作區] 頁面上，選取已設定的工作區。

2. 在 [設定] 底下，選取 [ **資料連線器** ]。

3. 在 [連接器名稱] 資料行下，從清單中選取 [ **安全性事件** ]，然後選取 [ **開啟連接器] 頁面** 。

4. 在 [連接器] 頁面上，選取您要串流的事件，然後選取 [套用 **變更** ]。

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel 的 [安全性事件] 頁面的螢幕擷取畫面，您可以在其中選取要串流的事件。":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>使用 Azure 資訊安全中心連接 Azure Sentinel  

1. 在 [Azure Sentinel 工作區] 頁面上，選取已設定的工作區。

2. 在 [設定] 底下，選取 [ **資料連線器** ]。

3. 從清單中選取 **Azure 資訊安全中心** ，然後選取 [ **開啟連接器] 頁面** 。

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Sentinel 中的 [資料連線器] 頁面的螢幕擷取畫面，其中顯示連接 Azure 資訊安全中心與 Azure Sentinel 的選取專案。":::

4. 選取 **[連線]，** 將 Azure 資訊安全中心連接到 Azure Sentinel。

5. 啟用 **建立事件** 以產生 Azure 資訊安全中心的事件。

## <a name="create-rules-to-identify-security-threats"></a>建立規則以識別安全性威脅

將資料來源連接到 Azure Sentinel 之後，您可以建立規則，根據偵測到的威脅來產生警示。 在下列範例中，我們將建立一個規則，以找出嘗試以錯誤的密碼登入 Windows server。

1. 在 [Azure Sentinel 總覽] 頁面的 [設定] 底下，選取 [ **分析** ]。

2. 在 [設定] 底下，選取 [ **分析** ]。

3. 選取 [ **+ 建立** ]，然後在下拉式清單中選取 [已 **排程的查詢規則** ]。

4. 在 [ **一般** ] 索引標籤上，輸入必要的資訊。

    - Name
    - 說明
    - 策略
    - 嚴重性
    - 狀態

    選取 **[下一步：設定規則邏輯 >]** 。

5. 在 [ **設定規則邏輯** ] 索引標籤上，輸入必要的資訊。

    - 以下顯示範例查詢的規則查詢 () 
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - 地圖實體
    - 查詢排程
    - 警示閾值
    - 事件群組
    - 隱藏

    選取 [下一步]。

6. 在 [ **事件設定** ] 索引標籤上，啟用 **此分析規則所觸發之警示的 [建立事件** ]，然後選取 **[下一步：自動回應 >** 。
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="[分析規則] 的螢幕擷取畫面，可讓您在 Azure Sentinel 中顯示此分析規則所觸發之警示的 [建立事件] 為 [已啟用] 來建立新規則。":::

7. 選取 **[下一步：檢查 >]** 。

8. 在 [ **審核和建立** ] 索引標籤上，檢查資訊並選取 [ **建立** ]。

第三次嘗試登入 Windows server 失敗之後，已建立的規則會觸發每次失敗嘗試的事件。

## <a name="view-generated-alerts"></a>查看產生的警示

您可以使用 Azure Sentinel 來查看產生的事件。 您也可以在問題解決之後，指派事件並關閉事件，這些都是從 Azure Sentinel。

1. 移至 [Azure Sentinel 總覽] 頁面。

2. 在 [威脅管理] 下方，選取 [ **事件** ]。

3. 選取事件。 然後，您可以將事件指派給小組進行解決。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="已選取事件的 Azure Sentinel 事件] 頁面的螢幕擷取畫面，以及指派事件以進行解析的選項。":::

    解決問題之後，您可以將它關閉。

## <a name="hunt-security-threats-with-queries"></a>使用查詢搜尋安全性威脅

您可以在 Azure Sentinel 中建立查詢或使用可用的預先定義查詢，以識別環境中的威脅。 下列步驟會執行預先定義的查詢。

1. 移至 [Azure Sentinel 總覽] 頁面。

2. 在 [威脅管理] 下方，選取 [ **搜尋** ]。 預先定義的查詢清單隨即顯示。

3. 選取查詢，然後選取 [ **執行查詢** ]。

4. 選取 [ **View results** ] 以檢查結果。

### <a name="create-a-new-query"></a>建立新的查詢

1.  在 [威脅管理] 下方，選取 [ **搜尋** ]，然後選取 [ **+ 新增查詢** ]。

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="醒目提示 [+ 新增] 查詢的 Azure Sentinel 搜尋頁面的螢幕擷取畫面。":::

2. 填寫下列資訊以建立自訂查詢。

    - Name
    - 說明
    - 自訂查詢
    - 輸入對應
    - 策略
    
3. 選取 [建立]  。 然後，您可以選取已建立的查詢、 **執行查詢** 和 **查看結果** 。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用 [Azure Defender 儀表板](../security-center/azure-defender-dashboard.md)。
- 探索 [Azure Defender](../security-center/azure-defender.md)提供的完整防護範圍。
- 瞭解 [Azure Sentinel 中的 Advanced 多階段攻擊偵測](../azure-monitor/learn/quick-create-workspace.md)。
