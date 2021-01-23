---
title: 規劃管理 App Service 成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure App Service 的成本。
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 9d742606435f62d48446bb8ad56ece7a31b76e47
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736216"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>規劃和管理 Azure App Service 的成本

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

本文說明如何規劃和管理 Azure App Service 的成本。 首先，您會使用 Azure 定價計算機來協助規劃 App Service 成本，然後再為服務新增任何資源以預估成本。 接下來，當您新增 Azure 資源時，請檢查預估成本。 當您開始使用 App Service 資源之後，請使用 [成本管理](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 功能來設定預算和監視成本。 您也可以查看預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。Azure App Service 的成本只是您 Azure 帳單中的每月成本的一部分。 雖然本文說明如何規劃和管理 App Service 的成本，但您必須支付 Azure 訂用帳戶中使用的所有 Azure 服務和資源的費用，包括協力廠商服務。

## <a name="relevant-costs-for-app-service"></a>App Service 的相關成本

App Service 會在 Azure 基礎結構上執行，以累算成本。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理該成本。

### <a name="costs-that-accrue-with-azure-app-service"></a>隨 Azure App Service 累積的成本

視您在 App Service 中使用的功能而定，可能會建立下列成本累積資源：

- **App Service 方案**  裝載 App Service 應用程式所需。
- **隔離層**  需要 App Service 環境的 [虛擬網路](../virtual-network/index.yml) 。
- **備份**  需要 [儲存體帳戶](../storage/index.yml) 才能進行備份。
- **診斷記錄**  您可以選取 [儲存體帳戶](../storage/index.yml) 做為記錄選項，或與 [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)整合。
- **App Service 憑證**  您在 Azure 中購買的憑證必須在 [Azure Key Vault](../key-vault/index.yml)中進行維護。

App Service 的其他成本資源 (請參閱 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/) 以取得詳細資料) ：

- [App Service 網域](manage-custom-dns-buy-domain.md)  如果您啟用自動續約，您的訂用帳戶會依年度註冊網域註冊。
- [App Service 憑證](configure-ssl-certificate.md#import-an-app-service-certificate)  購買時一次性費用。 如果您有多個子域要保護，您可以購買一個萬用字元憑證，而不是多個標準憑證來降低成本。
- 以[IP 為基礎的憑證](configure-ssl-bindings.md#create-binding)系結 系結會在應用層級的憑證上進行設定。 每個系結都會累積成本。 針對 **標準** 層和更新版本，第一個以 IP 為基礎的系結不會收費。

### <a name="costs-that-might-accrue-after-resource-deletion"></a>資源刪除之後可能會產生的成本

當您刪除 App Service 方案中的所有應用程式時，方案會繼續根據其設定的定價層和實例數目來產生費用。 若要避免不必要的費用，請刪除方案，或將其向下調整為 **免費** 層。

刪除 Azure App Service 資源之後，來自相關 Azure 服務的資源可能會繼續存在。 它們會繼續累積成本，直到您將其刪除為止。 例如：

- 您為 **隔離** 層建立的虛擬網路 App Service 方案
- 您建立用來儲存備份或診斷記錄的儲存體帳戶
- 您建立用來儲存 App Service 憑證的 Key Vault
- 您建立用來傳送診斷記錄的記錄檔分析命名空間
- 尚未過期 App Service 的[實例或戳記保留](#azure-reservations)

### <a name="using-monetary-credit-with-azure-app-service"></a>搭配 Azure App Service 使用貨幣點數

您可以使用 Azure 預付金支付 Azure App Service 費用 (之前稱為預付金) 點數。 不過，您無法使用 Azure 預付點數來支付協力廠商產品和服務的費用，包括來自 Azure Marketplace 的產品和服務。

## <a name="estimate-costs"></a>預估成本

您可以藉由使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)，以簡單的方式預估和優化您的 App Service 成本。

若要使用定價計算機，請按一下 [**產品**] 索引標籤中的 [ **App Service** ]。然後，向下滾動以使用計算機。 下列螢幕擷取畫面是一個範例，並不會反映目前的定價。

![顯示 Azure 定價計算機中預估成本的範例](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

當您建立 App Service 應用程式或 App Service 方案時，您可以看到預估成本。

若要建立應用程式並查看預估價格：

1. 在 [建立] 頁面上，向下 **App Service 計畫**]，然後按一下 [ **建立新** 的]。
1. 指定名稱，然後按一下 **[確定]**。
1. 在 [ **Sku 和大小**] 旁，按一下 [ **變更大小**]。
1. 查看摘要中顯示的預估價格。 下列螢幕擷取畫面是一個範例，並不會反映目前的定價。

    ![檢查入口網站中每個定價層的預估成本](media/overview-manage-costs/pricing-estimates.png)

如果您的 Azure 訂用帳戶有消費限制，Azure 會讓您無法消費于您的點數金額。 當您建立和使用 Azure 資源時，會使用您的點數。 當您達到點數限制時，就會在該計費期間的剩餘時間內停用您所部署的資源。 您無法變更點數限制，但可以將它移除。 如需消費限制的詳細資訊，請參閱 [Azure 消費限制](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="optimize-costs"></a>將成本優化

在基本層級中，App Service 應用程式會依裝載這些應用程式的 App Service 方案來收費。 與您的 App Service 部署相關聯的成本取決於幾個主要因素：

- **定價層**  也稱為 App Service 方案的 SKU。 較高層級提供更多的 CPU 核心、記憶體、儲存體或功能，或它們的組合。
- **實例計數**  專用層 (基本和以上的) 可以相應放大，而每個相應放大的實例則會累算成本。
- **戳記費用**  在隔離層中，不論裝載多少應用程式或背景工作實例，都會在您的 App Service 環境中產生固定費用。

App Service 方案可以裝載一個以上的應用程式。 視您的部署而定，您可以節省在一個 App Service 方案上裝載更多應用程式的成本， (也就是將您的應用程式裝載在) App Service 的方案上。

如需詳細資訊，請參閱 [App Service 計畫總覽](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>非生產工作負載

若要在累積低成本或最低成本的情況下測試 App Service 或您的解決方案，您可以從使用在共用實例上託管的兩個專案層級定價層（ **免費** 和 **共用**）開始。 若要在具有更佳效能的專用實例上測試您的應用程式，您可以升級到 **基本** 層，以支援 Windows 和 Linux 應用程式。 

> [!NOTE]
> **Azure 開發/測試定價**  若要測試需要較高層級的生產前工作負載 (除了 **隔離**) 之外，Visual Studio 訂閱者也可以利用 [Azure 開發/測試定價](https://azure.microsoft.com/pricing/dev-test/)，以提供大量折扣。
>
> **免費** 和 **共用** 層，以及 Azure 開發/測試定價折扣，都不會有有財務支援的 SLA。

### <a name="production-workloads"></a>生產工作負載

生產工作負載隨附專用 **標準** 定價層或更高版本的建議。 雖然價格較高，但也可提供更多的記憶體和儲存體及更高的效能，讓您在每個計算實例上提供更高的應用程式密度。 這會針對相同數目的應用程式轉譯為較低的實例計數，因此成本較低。 事實上， **Premium V3** (最高的非 **隔離** 層級) 是最符合成本效益的方式，可讓您的應用程式大規模服務。 若要增加成本，您可以取得 [Premium V3 保留](#azure-reservations)的深度折扣。

> [!NOTE]
> **Premium V3** 支援 Windows 容器和 Linux 容器。 

選擇您想要的定價層之後，您應該將閒置的實例降至最低。 在向外延展部署中，您可以在使用量過低的計算實例上浪費金錢。 您應該 [設定](../azure-monitor/platform/autoscale-get-started.md)自動調整功能，可在 **標準** 層和更新版本中使用。 藉由建立相應放大排程和計量型相應放大規則，您只需為您在任何特定時間所需的實例付費。

### <a name="azure-reservations"></a>Azure 保留

如果您打算充分利用一年或更多的計算實例數目，您應該利用 **Premium V3** 層，並將這些實例以1年或3年的增量保留，以大幅降低實例成本。 每月節省的費用最多可達每個實例55%。 可能有兩種類型的保留：

- **Windows (或平臺中立)**  可套用至您訂用帳戶中的 Windows 或 Linux 實例。
- **Linux 專用**  只適用于您訂用帳戶中的 Linux 實例。

保留實例定價適用于您訂用帳戶中適用的實例，最多可達您保留的實例數目。 保留的實例是計費的，且不會系結至特定的計算實例。 如果您在保留期間內的任何時間點執行較少的實例，您仍需支付保留實例的費用。 如果您在保留期間內的任何時間點執行多個實例，則需支付額外實例的一般應計成本。

**隔離** 層 (App Service 環境) 也支援1年期和3年期的保留，價格低廉。 如需詳細資訊，請參閱 [保留折扣如何套用至 Azure App Service 隔離的戳記](../cost-management-billing/reservations/reservation-discount-app-service-isolated-stamp.md)。

## <a name="monitor-costs"></a>監視成本

當您搭配 App Service 使用 Azure 資源時，您會產生費用。 Azure 資源使用量單位成本會隨著時間間隔而改變， (秒、分鐘、小時和日) 。 一旦 App Service 開始使用，就會產生成本，而您可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到成本。

當您使用成本分析時，您會看到不同時間間隔的圖表和資料表 App Service 成本。 某些範例是依日期、目前和先前月份，以及年度。 您也可以根據預算和預測成本來查看成本。 在一段時間內切換至更長的觀點，可協助您找出花費趨勢。 您會看到超支可能發生的位置。 如果您已建立預算，也可以輕鬆地查看其已超出的位置。
    
若要查看成本分析中的 App Service 成本：

1. 登入 Azure 入口網站。
2. 開啟 Azure 入口網站中的範圍，然後選取功能表中的 [ **成本分析** ]。 例如，移至 [訂用帳戶 **]，從** 清單中選取訂用帳戶，然後選取功能表中的 [  **成本分析** ]。 選取 [ **範圍** ]，以切換至成本分析中的不同範圍。
3. 依預設，服務的成本會顯示在第一個環圈圖中。 選取標示 App Service 的圖表區域。

當您一開始開啟成本分析時，就會顯示實際的每月費用。 以下是顯示所有每月使用量成本的範例。

![顯示訂用帳戶累計成本的範例](media/overview-manage-costs/all-costs.png)

若要縮小單一服務的成本，例如 App Service，請選取 [ **新增篩選** ]，然後選取 [ **服務名稱**]。 然後，選取 [ **App Service**]。

以下範例顯示只 App Service 的成本。

![顯示 ServiceName 累計成本的範例](media/overview-manage-costs/service-specific-costs.png)

在上述範例中，您會看到服務的目前成本。 依資源群組的 Azure 區域 (位置) 和 App Service 成本的成本也會顯示。 您可以從這裡自行探索成本。

## <a name="create-budgets"></a>建立預算

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

您可以建立 [預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而會產生額外的費用。 如需有關建立預算時可用之篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行更多資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

## <a name="next-steps"></a>後續步驟

- 深入瞭解定價與 Azure 儲存體的運作方式。 請參閱 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/)。
- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->