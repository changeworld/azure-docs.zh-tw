---
title: 使用 Azure 成本管理與計費來避免及分析非預期的費用
description: 了解如何避免您的 Azure 帳單上產生非預期的費用，並針對您的 Azure 帳戶使用成本追蹤和管理功能。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 3/30/2020
ms.author: banders
ms.openlocfilehash: 79af6f78e8e9bf93c49deafe79f6a421cbb77d1a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475271"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>使用 Azure 計費與成本管理避免非預期的費用

當您註冊 Azure 時，為了深入了解您的支出，有幾件事您可以做：

- 使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)、Azure 價位表新增服務前，或在 Azure 入口網站中新增服務時，先取得預估成本。
- 使用[預算](../costs/tutorial-acm-create-budgets.md)、[警示](../costs/cost-mgt-alerts-monitor-usage-spending.md)和[成本分析](../costs/quick-acm-cost-analysis.md)來監視成本。
- 藉由與[詳細的使用量檔案](download-azure-invoice-daily-usage-date.md)進行比較，以檢閱您發票的費用。
- 使用[計費](https://docs.microsoft.com/rest/api/billing/)和[使用量](https://docs.microsoft.com/rest/api/consumption/) API 來整合帳單和成本資料與您自己的報告系統。
- 使用 Enterprise 合約 (EA)、雲端解決方案提供者 (CSP) 和 Azure 贊助客戶的其他資源與工具。
- 免費使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)提供的[一些最常用的 Azure 服務 12 個月](create-free-services.md)。 除了下列建議之外，請參閱[避免產生免費帳戶的費用](avoid-charges-free-account.md)。

如果您需要取消 Azure 訂用帳戶，請參閱[取消您的 Azure 訂用帳戶](cancel-azure-subscription.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在新增 Azure 服務之前取得估計的成本

使用下列其中一個工具來預估使用 Azure 服務的成本：
- Azure 定價計算機
- Azure 價位表
- Azure 入口網站

下列各節中的影像顯示以美元為單位的定價範例。

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用價格計算機在線上評估成本

查看[價格計算機](https://azure.microsoft.com/pricing/calculator/)即可取得所要新增服務的每月評估成本。 您可以變更貨幣以取得當地貨幣的預估值。

![定價計算機功能表的螢幕擷取畫面](./media/getting-started/pricing-calc.png)

您可以檢視任何第一方 Azure 服務的預估成本。 例如，在下面的螢幕擷取畫面中，如果您讓 A1 Windows 虛擬機器 (VM) 持續運作，則運算時間的預估成本為每月 $66.96 美元：

![定價計算機的螢幕擷取畫面，其中顯示 A1 Windows 虛擬機器每月的預估成本](./media/getting-started/pricing-calcvm.png)

如需定價詳細資訊，請參閱[定價常見問題集](https://azure.microsoft.com/pricing/faq/)。 如果您想要與 Azure 銷售人員交談，請撥打常見問題集頁面頂端所顯示的電話號碼。

### <a name="view-and-download-azure-price-sheet"></a>檢視及下載 Azure 價位表

如果您可透過 Enterprise 合約 (EA) 或 Microsoft 客戶合約 (MCA) 存取 Azure，您可檢視及下載 Azure 帳戶的價位表。 價位表是一個 Excel 檔案，其中包含所有 Azure 服務的價格。 如需詳細資訊，請參閱[檢視及下載您的 Azure 定價](ea-pricing.md)。

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

在 Azure 入口網站中新增服務時，您可以檢視每個月的預估成本。 例如，當您選擇 Windows VM 的大小時，會看到計算時數的預估每月成本：

![範例︰A1 Windows VM 顯示每月預估成本](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>監視使用 Azure 服務時的成本
您可以使用下列工具來監視成本：

- 預算和成本警示
- 成本分析

### <a name="track-costs-with-budgets-and-cost-alerts"></a>使用預算和成本警示來追蹤成本

建立[預算](../costs/tutorial-acm-create-budgets.md)來管理成本，以及建立[警示](../costs/cost-mgt-alerts-monitor-usage-spending.md)，以在出現異常消費和超支時自動通知利害關係人。

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> 使用成本分析探索及分析成本

當您的 Azure 服務執行之後，請定期檢查成本以追蹤您的 Azure 費用。 您可以使用成本分析來了解您的 Azure 使用量所產生的成本。

1. 瀏覽 [Azure 入口網站中的 [成本管理 + 帳單] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade)。

2. 按一下畫面左側的 [成本分析]  ，以查看依各種樞紐 (例如服務、位置和訂用帳戶) 細分的目前成本。 新增服務或進行採購之後，請等候 24 小時以便顯示資料。 根據預設，成本分析會顯示您所在範圍的成本。 例如，下面的螢幕擷取畫面中會顯示 Contoso 計費帳戶的成本。 使用 [範圍] 框，以切換至成本分析中的不同範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](../costs/understand-work-scopes.md#scopes)。

    ![Azure 入口網站中成本分析檢視的螢幕擷取畫面](./media/getting-started/cost-analysis.png)

4. 您可以依各種屬性 (例如標籤、資源類型和時間範圍) 進行篩選。 按一下 [新增篩選條件]  以新增屬性的篩選條件，然後選取要篩選的值。 選取 [匯出]  ，將此檢視匯出為逗號分隔值 (.csv) 檔案。

5. 此外，您也可以按一下圖表的標籤，以查看該標籤的每日支出歷程記錄。 例如：在下面的螢幕擷取畫面中，按一下虛擬機器可顯示執行 VM 的每日成本。

    ![Azure 入口網站中消費歷程記錄檢視的螢幕擷取畫面](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>最佳化及降低成本
如果您不熟悉成本管理的原則，請閱讀[如何使用 Azure 成本管理將雲端投資最佳化](../costs/cost-mgt-best-practices.md)。

在 Azure 入口網站中，您也可以透過自動關閉 VM 和 Advisor 建議來優化和降低 Azure 成本。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>請考慮使用成本削減功能，例如 VM 自動關機

視您的案例而定，您可以在 Azure 入口網站中設定 VM 自動關機。 如需詳細資訊，請參閱[使用 Azure Resource Manager 的 VM 自動關機](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) \(英文\)。

![在入口網站中的自動關機選項的螢幕擷取畫面](./media/getting-started/auto-shutdown.png)

自動關機與您在 VM 中使用電源選項關閉不一樣。 自動關機會停止並解除配置您的 VM，以停止額外的使用費用。 如需詳細資訊，請參閱 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 的定價常見問題，以及 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)有關 VM 的狀態。

如需更多針對開發和測試環境的成本削減功能，請參閱 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>開啟並檢閱 Azure 建議程式的建議

[Azure 建議程式](../../advisor/advisor-overview.md)可協助您藉由找出低使用率的資源來降低成本。 在 Azure 入口網站中搜尋 **Advisor**：

![Azure 入口網站中 Azure 建議程式按鈕的螢幕擷取畫面](./media/getting-started/advisor-button.png)

從左側選取 [成本]  。 您會在 [成本]  索引標籤中看到可操作的建議：

![建議程式的成本建議範例的螢幕擷取畫面](./media/getting-started/advisor-action.png)

請參閱[透過建議最佳化成本](../costs/tutorial-acm-opt-recommendations.md)教學課程，以取得有關 Advisor 節省成本建議的引導式教學課程。


## <a name="integrate-with-billing-and-consumption-apis"></a>整合帳單與使用量 API

使用 Azure [帳單](https://docs.microsoft.com/rest/api/billing/)和[使用量](https://docs.microsoft.com/rest/api/consumption/) API，以程式設計方式取得帳單和成本資料。 RateCard API 與使用情況 API 一起使用即可取得您的計費使用量。 如需詳細資訊，請參閱[深入瞭解 Microsoft Azure 資源耗用量](usage-rate-card-overview.md)。

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> 其他資源和特殊案例

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和贊助客戶
若要開始使用，請連絡帳戶管理員或 Azure 。

| 供應項目 | 資源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise 合約 (EA) | [EA 入口網站](https://ea.azure.com/)、[協助文件](https://ea.azure.com/helpdocs)和 [Power BI 報告](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 雲端解決方案提供者 (CSP) | 與您的提供者討論 |
| Azure 贊助 | [贊助入口網站 (英文)](https://www.microsoftazuresponsorships.com/) |

如果您管理的是大型組織的 IT，我們建議您閱讀 [Azure 企業 Scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) 和[企業 IT 技術白皮書](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf 下載，僅提供英文版)。

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Azure 入口網站中的 Enterprise 合約成本檢視

企業成本檢視目前只提供公開預覽。 下列為需注意的事項：

- 訂閱費用是以使用情況計費，不包含預付金額、超額部分、包含的數量、調整內容和稅金。 系統會在申請時計算實際費用。
- 在 Azure 入口網站中顯示的數量可能與在企業版入口網站中顯示的不同。 在企業版入口網站中的更新可能需要幾分鐘之後才會顯示在 Azure 入口網站中。
- 如果您沒有看到成本，原因可能為下列其中之一：
    - 您沒有訂用帳戶層級的權限。 若要查看企業成本檢視，您必須是訂用帳戶層級上的帳單讀者、讀者、參與者或擁有者。
    - 您是帳戶擁有者且您的申請管理員已停用「AO 檢視費用」設定。  請連絡您的申請管理員以取得費用存取權。
    - 您是部門管理員且您的申請管理員已停用 [DA 檢視費用]  設定。  請連絡您的註冊管理員以取得存取權。
    - 您是透過通道合作夥伴購買 Azure，而該合作夥伴未釋出定價資訊。  
- 如果您在企業版入口網站中更新成本存取相關的設定，則變更可能需要幾分鐘才會在 Azure 入口網站中顯示。
- 消費限制和發票指導方針不適用於 EA 訂用帳戶。

### <a name="check-your-subscription-and-access"></a>檢查訂用帳戶和存取權

若要檢視成本，您需要成本或帳單資訊的帳戶或訂用帳戶層級存取權。 存取權會因計費帳戶類型而異。 若要深入了解計費帳戶及檢查您的計費帳戶類型，請參閱[在 Azure 入口網站中檢視計費帳戶](view-all-accounts.md)。

如果您可透過 Microsoft 線上服務方案 (MOSP) 計費帳戶存取 Azure，請參閱[管理對 Azure 帳單資訊的存取](manage-billing-access.md)。

如果您可透過 Enterprise 合約 (EA) 計費帳戶存取 Azure，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。

如果您可透過 Microsoft 客戶合約 (MCA) 計費帳戶存取 Azure，請參閱[了解 Azure 中的 Microsoft 客戶合約系統管理角色](understand-mca-roles.md)。

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>要求服務事件的服務等級協定點數

服務等級協定 (SLA) 會說明 Microsoft 對執行時間與連線能力的承諾。 當 Azure 服務遇到會影響執行時間或連線能力 (通常稱為「中斷」  ) 的問題時，系統就會回報服務事件。 如果我們未依照 SLA 中的說明來達到並維護每個服務的服務等級，則您可能有資格取得每月服務費用的信用額度。

若要要求點數：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 如果您有多個帳戶，請確定您使用受 Azure 停機時間影響的帳戶。
2. 建立新的支援要求。
3. 在 [問題類型]  底下，選取 [計費]  。
4. 在 [問題類型]  底下，選取 [退費要求]  。
5. 新增詳細資料以指定您要求 SLA 點數，並提及日期/時間/時區以及受影響的服務 (VM、網站等)
6. 請確認您的連絡人詳細資料，然後選取 [建立]  以提交您的要求。

SLA 閾值會因服務而異。 例如，SQL Web 層的 SLA 為 99.9%，VM 的 SLA 為 99.95%，而 SQL 標準層的 SLA 為 99.99%。

有些服務需套用 SLA 的必要條件。 例如，虛擬機器必須在相同的可用性設定組中部署兩個以上的執行個體。

如需詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)和 [Azure 服務的 SLA 摘要](https://azure.microsoft.com/support/legal/sla/summary/)文件。

## <a name="analyze-unexpected-charges"></a>分析非預期的費用

您針對貴組織建立的雲端資源基礎結構可能很複雜。 許多 Azure 資源類型可能有不同類型的費用。 Azure 資源可能由貴組織中的不同小組所擁有，且可能會有不同的計費模型類型適用於各種資源。 若要進一步了解費用，請使用下列各節中的一個或多個策略開始進行分析。

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>檢閱您的發票並識別負責收費的資源

您購買 Azure 服務的方式可協助您判斷您在識別與費用相關聯的資源時，可供您使用的方法和工具。 若要判斷您適用哪一種方法，請先[判斷您的 Azure 供應項目類型](../costs/understand-cost-mgt-data.md#determine-your-offer-type)。 然後，在[支援的 Azure 供應項目](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers)清單中識別您的客戶類別。

下列文章提供詳細的步驟，說明如何根據您的客戶類型來檢閱您的帳單。 在每篇文章中都有相關指示，說明如何下載 CSV 檔案，其中包含指定計費週期的使用量和成本詳細資料。

- [隨用隨付帳單檢閱流程](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Enterprise 合約帳單檢閱流程](../understand/review-enterprise-agreement-bill.md)
- [Microsoft 客戶合約檢閱流程](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft 合作夥伴合約檢閱流程](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

您的 Azure 帳單會根據每個_計量_來匯總當月費用。 計量是用來追蹤一段時間的資源使用量，並用來計算您的帳單。 當您建立單一 Azure 資源 (例如虛擬機器) 時，會針對該資源建立一或多個計量執行個體。

根據您所要分析帳單上顯示的 _MeterName_ 篩選使用量 CSV 檔案，以查看適用於計量表的所有明細項目。 明細項目的 _InstanceID_ 會對應到產生費用的實際 Azure 資源。

當您識別出有問題的資源時，可以在 Azure 成本管理中使用成本分析，進一步分析資源的相關成本。 若要深入了解如何使用成本分析，請參閱[開始分析成本](../costs/quick-acm-cost-analysis.md)。

### <a name="identify-spikes-in-cost-over-time"></a>識別一段時間的成本尖峰

有時候，您可能不知道哪些最近的成本導致計費費用上的變更。 若要了解變更的內容，您可以使用成本分析來[查看一段時間內每日或每月的成本明細](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)。 建立視圖之後，請依 [服務]  或 [資源]  將您的費用群組以識別變更。 您也可以將您的視圖變更為 [線條]  圖表，以更好的視覺化方式呈現資料。

![在成本分析中顯示一段時間的成本範例](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>判斷資源定價並了解其計費模型

單一資源可能會在多個 Azure 產品和服務之間產生費用。 如需深入了解每個 Azure 服務的定價，請參閱 [Azure 各項產品的定價](https://azure.microsoft.com/pricing/#product-pricing)頁面。 例如，在 Azure 中建立的單一虛擬機器 (VM) 可能會建立下列計量來追蹤其使用量。 每個都可能有不同的定價。

- 計算時數
- IP 位址時數
- 資料傳入
- 資料傳出
- 標準受控磁碟
- 標準受控磁碟作業
- 標準 IO 磁碟
- 標準 IO - 區塊 Blob 讀取
- 標準 IO - 區塊 Blob 寫入
- 標準 IO - 區塊 Blob 刪除

建立 VM 後，每個計量便會開始發出使用量記錄。 Azure 計量系統中會追蹤使用量和計量的價格。 您可以在使用量 CSV 檔案中查看用來計算費用的計量。

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>尋找負責資源的人員並與他們互動

通常，負責指定資源的小組會知道已對資源所做的變更。 與這些小組互動很有用，因為您可以識別可能出現費用的原因。 例如，主控小組最近可能已建立資源、更新其 SKU (因而變更資源費率)，或因為程式碼變更而增加資源的負載。 繼續閱讀下列各節，以了解判斷哪些人員擁有資源的更多技巧。

#### <a name="analyze-the-audit-logs-for-the-resource"></a>分析資源的稽核記錄

如果您擁有檢視資源的許可權，您應該能夠存取其稽核記錄。 請檢閱記錄，以尋找負責資源最新變更的使用者。 若要深入了解，請參閱[檢視及擷取 Azure 活動記錄事件](../../azure-monitor/platform/activity-log-view.md)。

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>分析使用者對資源父範圍的許可權

具有訂用帳戶或資源群組寫入存取權的人員，通常會有建立資源的相關資訊。 他們應該能夠說明資源的用途，或將您指引至知道的人員。 若要識別擁有訂用帳戶範圍許可權的人員，請參閱[檢視角色指派](../../role-based-access-control/check-access.md#view-role-assignments)。 您可以使用類似流程來處理資源群組。

### <a name="get-help-to-identify-charges"></a>取得識別費用的協助

如果您已使用上述策略，但仍無法了解為何會收到費用，或如果您需要帳單問題的其他協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解如何使用[消費限制](spending-limit.md)來避免超支。
- 開始[分析您的 Azure 成本](../costs/quick-acm-cost-analysis.md)。
