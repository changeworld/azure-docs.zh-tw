---
title: 連續匯出可將 Azure 資訊安全中心的警示和建議傳送至 Log Analytics 工作區或 Azure 事件中樞
description: 瞭解如何將安全性警示和建議的連續匯出設定至 Log Analytics 工作區或 Azure 事件中樞
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: 43b66a59062a230aa2fba6909172deb5f1740b28
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122307"
---
# <a name="continuously-export-security-center-data"></a>持續匯出資訊安全中心資料

Azure 資訊安全中心會產生詳細的安全性警示和建議。 您可以在入口網站中或透過程式設計工具來加以查看。 您可能也需要匯出部分或全部資訊，以與您環境中的其他監視工具進行追蹤。 

**連續匯出** 可讓您完整自訂將匯出的 *內容* ，以及將匯出到 *何處* 。 例如，您可以設定它，以便：

- 所有高嚴重性警示都會傳送至 Azure 事件中樞
- 從 SQL 伺服器的弱點評定掃描中，所有的中度或更高嚴重性結果都會傳送至特定的 Log Analytics 工作區
- 特定建議會在每次產生時傳遞至事件中樞或 Log Analytics 工作區 

本文說明如何設定對 Log Analytics 工作區或 Azure 事件中樞的連續匯出。

> [!NOTE]
> 如果您需要將安全中心與 SIEM 整合，請參閱將 [警示串流至 SIEM、SOAR 或 IT 服務管理解決方案](export-to-siem.md)。

> [!TIP]
> 「安全性中心」也提供選項來執行一次性的手動匯出至 CSV。 深入瞭解 [警示和建議的手動一次性匯出](#manual-one-time-export-of-alerts-and-recommendations)。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|免費|
|必要的角色和權限：|<ul><li>資源群組的 **安全性系統管理員** 或 **擁有** 者</li><li>目標資源的寫入權限</li><li>如果您使用下面所述的 Azure 原則 ' DeployIfNotExist ' 原則，您也需要指派原則的許可權</li></ul>|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov，其他 Gov<br>![是](./media/icons/yes-icon.png) Gov 至事件中樞) 的中國 (|
|||





## <a name="set-up-a-continuous-export"></a>設定連續匯出 

您可以使用提供的 Azure 原則範本，從 Azure 入口網站中的 [安全性中心] 頁面、透過 [安全性中心] REST API 或大規模設定連續匯出。 在下方選取適當的索引標籤，以取得每個的詳細資料。

### <a name="use-the-azure-portal"></a>[**使用 Azure 入口網站**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>從 Azure 入口網站中的 [安全性中心] 頁面設定連續匯出

無論您是設定連續匯出至 Log Analytics 工作區或 Azure 事件中樞，都必須執行下列步驟。

1. 從 [Security Center 的提要欄位] 中，選取 [ **定價 & 設定**]。
1. 選取您要設定資料匯出的特定訂用帳戶。
1. 從該訂用帳戶的 [設定] 頁面的側邊欄中，選取 [ **連續匯出**]。

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Azure 資訊安全中心中的匯出選項":::

    您會在這裡看到匯出選項。 每個可用的匯出目標都有一個索引標籤。 

1. 選取您要匯出的資料類型，然後從每種類型的篩選中選擇 (例如，[僅匯出高嚴重性警示]) 。
1. （選擇性）如果您的選擇包含這四個建議的其中一個，您可以將弱點評定結果與它們一起包含：
    - 應補救 SQL 資料庫的弱點評定結果
    - 您應補救電腦上 SQL server 的弱點評定結果 (預覽版) 
    - Azure Container Registry 映像中應予補救的弱點 (Qualys 技術提供)
    - 應補救虛擬機器中的弱點

    若要包含這些建議的結果，請啟用 [ **包含安全性結果** ] 選項。

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在連續匯出設定中包含安全性結果切換" :::

1. 從 [匯出目標] 區域中，選擇您想要儲存資料的位置。 資料可以儲存在不同訂用帳戶的目標中 (例如中央事件中樞實例或中央 Log Analytics 工作區) 。
1. 選取 [儲存]。

### <a name="use-the-rest-api"></a>[**使用 REST API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>使用 REST API 設定連續匯出

您可以透過 Azure 資訊安全中心 [自動化 API](/rest/api/securitycenter/automations)來設定和管理連續匯出。 您可以使用此 API 來建立或更新規則，以匯出到下列任何可能的目的地：

- Azure 事件中樞
- Log Analytics 工作區
- Azure Logic Apps 

API 提供 Azure 入口網站中無法使用的額外功能，例如：

* **更大的磁片** 區-API 可讓您在單一訂用帳戶上建立多個匯出設定。 安全中心的入口網站 UI 中的 [ **連續匯出** ] 頁面，每個訂用帳戶只支援一個匯出設定。

* **其他功能** -API 提供未顯示在 UI 中的其他參數。 例如，您可以將標籤新增至您的自動化資源，也可以根據在資訊安全中心的入口網站 UI 中 [ **連續匯出** ] 頁面所提供的更多警示和建議屬性來定義匯出。

* **更專注的範圍** -API 可為您的匯出設定範圍提供更細微的層級。 使用 API 定義匯出時，您可以在資源群組層級進行。 如果您是在安全中心的入口網站 UI 中使用 [ **連續匯出** ] 頁面，您必須在訂用帳戶層級定義它。

    > [!TIP]
    > 如果您已使用 API 來設定多個匯出設定，或您已使用僅限 API 的參數，這些額外的功能將不會顯示在 [安全性中心] UI 中。 相反地，會有橫幅通知您有其他設定存在。

在 [REST API 檔](/rest/api/securitycenter/automations)中深入瞭解自動化 API。





### <a name="deploy-at-scale-with-azure-policy"></a>[**使用 Azure 原則大規模部署**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>使用提供的原則大規模設定連續匯出

自動化組織的監視和事件回應程程序，可大幅改善調查和緩解安全性事件所需的時間。

若要在組織中部署連續匯出設定，請使用下列所述的 Azure 原則 ' DeployIfNotExist ' 原則來建立和設定連續匯出程式。

**若要執行這些原則**

1. 從下表中，選取您要套用的原則：

    |目標  |原則  |原則識別碼  |
    |---------|---------|---------|
    |連續匯出至事件中樞|[為 Azure 資訊安全中心警示與建議部署「匯出至事件中樞」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |連續匯出至 Log Analytics 工作區|[為 Azure 資訊安全中心警示與建議部署「匯出至 Log Analytics 工作區」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > 您也可以藉由搜尋 Azure 原則來找到這些內容：
    > 1. 開啟 Azure 原則。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="存取 Azure 原則":::
    > 2. 在 [Azure 原則] 功能表中，選取 [ **定義** ] 並依名稱搜尋。 

1. 從相關的 Azure 原則] 頁面中，選取 [ **指派**]。
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="指派 Azure 原則":::

1. 開啟每個索引標籤，並視需要設定參數：
    1. 在 [ **基本** ] 索引標籤中，設定原則的範圍。 若要使用集中式管理，請將原則指派給包含要使用連續匯出設定之訂用帳戶的管理群組。 
    1. 在 [ **參數** ] 索引標籤中，設定資源群組和資料類型的詳細資料。 
        > [!TIP]
        > 每個參數都有工具提示，說明您可以使用的選項。
        >
        > Azure 原則的 [參數] 索引標籤中 (1) 可存取與安全性中心的連續匯出頁面 (2) 類似的設定選項。
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="比較連續匯出與 Azure 原則的參數" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. （選擇性）若要將此指派套用至現有的訂用帳戶，請開啟 [ **補救** ] 索引標籤，然後選取建立補救工作的選項。
1. 查看 [摘要] 頁面，然後選取 [ **建立**]。

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>匯出至 Log Analytics 工作區的相關資訊

如果您想要分析 Log Analytics 工作區中的 Azure 資訊安全中心資料，或使用 Azure 警示搭配安全性中心警示，請將連續匯出設定為 Log Analytics 工作區。

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 資料表和架構

安全性警示和建議分別儲存在 *SecurityAlert* 和 *SecurityRecommendations* 資料表中。 

包含這些資料表的 Log Analytics 解決方案名稱，取決於您是否已啟用 Azure Defender：安全性 ( 「安全性與稽核」 ) 或 SecurityCenterFree。 

> [!TIP]
> 若要查看目的地工作區上的資料，您必須啟用 **安全性與稽核** 或 **SecurityCenterFree** 其中一個解決方案。

![Log Analytics 中的 * SecurityAlert * 資料表](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看已匯出資料類型的事件架構，請造訪 [Log Analytics 資料表架構](https://aka.ms/ASCAutomationSchemas)。


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>在 Azure 監視器中查看匯出的警示和建議

您也可以選擇在 [Azure 監視器](../azure-monitor/platform/alerts-overview.md)中查看匯出的安全性警示及/或建議。 

Azure 監視器針對各種不同的 Azure 警示（包括診斷記錄、計量警示，以及以 Log Analytics 工作區查詢為基礎的自訂警示）提供統一的警示體驗。

若要在 Azure 監視器中從資訊安全中心查看警示和建議，請根據 Log Analytics 查詢 (記錄警示) 來設定警示規則：

1. 從 Azure 監視器的 [ **警示** ] 頁面上，選取 [ **新增警示規則**]。

    ![Azure 監視器的警示頁面](./media/continuous-export/azure-monitor-alerts.png)

1. 在 [建立規則] 頁面中，以您在 [Azure 監視器) 中設定記錄警示規則](../azure-monitor/platform/alerts-unified-log.md) 的相同方式，設定新的規則 (：

    * 針對 [ **資源**]，選取您匯出安全性警示和建議的 Log Analytics 工作區。

    * 針對 [ **條件**]，選取 [ **自訂記錄搜尋**]。 在出現的頁面中，設定查詢、回顧期限和頻率週期。 在搜尋查詢中，您可以輸入 *SecurityAlert* 或 *SecurityRecommendation* ，以查詢當您啟用「連續匯出至 Log Analytics」功能時，「安全性中心」持續匯出的資料類型。 
    
    * （選擇性）設定您想要觸發的 [動作群組](../azure-monitor/platform/action-groups.md) 。 動作群組可以觸發電子郵件傳送、ITSM 票證、Webhook 等等。
    ![Azure 監視器警示規則](./media/continuous-export/azure-monitor-alert-rule.png)

您現在會看到新的 Azure 資訊安全中心警示或建議 (根據您設定的連續匯出規則，以及您在 Azure 監視器警示規則中所定義的條件) Azure 監視器警示，並自動觸發動作群組 (（如果有提供) ）。

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>手動一次匯出警示和建議

若要下載警示或建議的 CSV 報告，請開啟 [ **安全性警示** ] 或 [ **建議** ] 頁面，然後選取 [ **下載 csv 報表** ] 按鈕。

[![將警示資料下載為 CSV 檔案](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 這些報表包含目前所選訂用帳戶中的資源警示和建議。


## <a name="faq---continuous-export"></a>常見問題-連續匯出

### <a name="what-are-the-costs-involved-in-exporting-data"></a>匯出資料涉及哪些成本？

啟用連續匯出不會產生任何費用。 視您在 Log Analytics 工作區中的設定而定，可能會產生成本，以在您的 Log Analytics 工作區中內嵌和保留資料。 

深入瞭解 [Log Analytics 工作區定價](https://azure.microsoft.com/pricing/details/monitor/)。

深入瞭解 [Azure 事件中樞定價](https://azure.microsoft.com/pricing/details/event-hubs/)。


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>匯出是否包含所有資源目前狀態的相關資料？

否。 為 **事件** 串流建立連續匯出：

- 啟用匯出之前所收到的 **警示** 將不會匯出。
- 每當資源的合規性狀態變更時，就會傳送 **建議**。 例如，當資源變成狀況不良時。 因此，與警示相同，將不會匯出因為您啟用匯出而未變更狀態之資源的建議。


### <a name="why-are-recommendations-sent-at-different-intervals"></a>為何會以不同的間隔傳送建議？

不同的建議有不同的合規性評估間隔，可能會因幾分鐘而異。 因此，建議在您的匯出中出現的時間長度會有所不同。

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>連續匯出是否支援任何商務持續性或嚴重損壞修復 (BCDR) 案例？

針對目標資源發生中斷或其他嚴重損壞的 BCDR 案例準備您的環境時，組織會負責根據 Azure 事件中樞、Log Analytics 工作區和邏輯應用程式的指導方針建立備份，以防止資料遺失。

深入瞭解 [Azure 事件中樞異地災難](../event-hubs/event-hubs-geo-dr.md)復原。


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>Azure 資訊安全中心免費提供連續匯出嗎？

可以！ 請注意，只有當您已啟用 Azure Defender 時，才會提供許多安全性中心警示。 若要預覽您在匯出的資料中所收到的警示，最好的方式就是查看 Azure 入口網站中的「安全性中心」頁面中顯示的警示。



## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定建議和警示的連續匯出。 您也已瞭解如何以 CSV 檔案的形式下載您的警示資料。 

如需相關材質，請參閱下列檔： 

- 深入瞭解 [工作流程自動化範本](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。
- [Azure 事件中樞文件](../event-hubs/index.yml)
- [Azure Sentinel 文件](../sentinel/index.yml)
- [Azure 監視器文件](../azure-monitor/index.yml)
- [匯出資料類型架構](https://aka.ms/ASCAutomationSchemas)