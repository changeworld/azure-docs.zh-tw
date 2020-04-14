---
title: 微軟商業市場常見問題解答
description: 有關 Azure 應用商店和微軟應用程式源的常見問題的解答
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/4/2019
ms.author: dsindona
ms.openlocfilehash: 6a142ce3c082f6f9a53287885ba347fc78f77718
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262021"
---
# <a name="microsoft-commercial-marketplace-faqs"></a>Microsoft 商業市集的常見問題集

回答有關商業市場的常見問題的解答。

## <a name="faq-for-customers"></a>客戶的常見問題集

### <a name="what-you-need-to-know-about-the-commercial-marketplace"></a>您需要瞭解的商業市場

**什麼是 Azure Marketplace？**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 提供來自 Microsoft 和合作夥伴的解決方案與服務的存取權和資訊。 客戶可以發現、嘗試或購買基於 Azure 或為 Azure 構建的雲端軟體解決方案。 我們的目錄陳列了 8,000 餘種品項，提供各種 Azure 建置組塊，例如虛擬機器 (VM)、API、Azure 應用程式、解決方案範本與受控應用程式、SaaS 應用程式、容器和諮詢服務等。

**誰是 Azure Marketplace 的客戶？**

Azure 應用商店專為對商業 IT 軟體和服務感興趣的 IT 專業人員和雲開發人員而設計。

**Azure Marketplace 目前提供哪些產品類型？**

Azure Marketplace 提供來自 Microsoft 與合作夥伴的技術解決方案和服務，其建置目的是為了擴充 Azure 產品和服務。 解決方案目錄跨越多個類別,包括:

* 基本作業系統
* 資料庫
* security
* 身分識別
* 網路功能
* 區塊鏈
* 開發人員工具

### <a name="azure-marketplace-for-customers"></a>適用於客戶的 Azure Marketplace

**如何開始使用 Azure Marketplace？**

您可以前往 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)，尋找經過認證及最佳化可在 Azure 上執行的各種企業應用程式和解決方案。 [**創建資源**](https://portal.azure.com/#create/hub)時,還可以通過[Azure 門戶]訪問 Azure 應用商店。

**Azure Marketplace 的主要優點為何？**

透過 Azure Marketplace，客戶可以探索專為 Azure 建置或建置在 Azure 之上的技術應用程式。 它將 Microsoft Azure 解決方案和服務的市場合併成單一整合平台，讓您只要按幾下，就能探索、試用、購買或部署解決方案。

**如何從 Azure Marketplace 購買產品？**

您可以透過下列途徑購買 Azure Marketplace 供應項目：

* [基於 Web 網店](https://azuremarketplace.microsoft.com/marketplace/apps)
* [Azure 門戶][Azure 門戶]
* [Azure 指令列介面 (CLI)](/cli/azure/?view=azure-cli-latest)

>[!Note]
>預付點數及其他形式的預付金無法用來支付軟體授權費用，但可用來支付相關聯的 Azure 使用費用。 列於 [Azure 預付金](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)的項目除外。

**我可以選擇部署 Azure 應用商店購買哪個 Azure 區域嗎?**

客戶可以選擇部署到他們啟用的任何 Azure 資料中心區域。 建議您選取最靠近服務的資料中心位置，以達到最佳效能並控管預算。

**如果我不小心刪除了 Azure 應用商店購買,是否可以*撤銷*該操作?**

否，刪除無法改變。 如果不小心刪除訂閱，可以重新購買。 任何未使用的功能或預付的服務都將遺失。

**如果我嘗試刪除某個應用程式正在使用的 Azure Marketplace 購買項目，是否會出現警告？**

否，Azure 在您刪除而購買項目時不會提供警告，即使該項目正在使用中或是有相依的應用程式。

**如果我的 Azure Marketplace 購買項目相依於其他資產 (例如 Azure 網站)，我必須管理這些相依性嗎？**

Azure Marketplace 供應項目的相依性不會自動受到管理。 在使用 Azure Marketplace 購買之前請先仔細檢閱其描述，以確認在部署之前是否有任何必要的相依性。

**我可以向 Azure 雲端解決方案提供者購買 Azure Marketplace 解決方案嗎？**

如果發行者已將其產品配置為透過雲端解決方案供應商 (CSP) 通路提供,則雲端解決方案供應商合作夥伴可以選擇轉售解決方案。

**支援哪些國家/地區購買通過 Azure 應用商店銷售/調配的應用程式和服務?**

Azure 應用商店可供[參與策略](/legal/marketplace/participation-policy)中列出的國家/地區的 Azure 客戶使用。

**Azure 應用商店支援哪些貨幣?**

交易方式如下:澳元、BRL、加元、瑞士法郎、丹麥克朗、歐元、英鎊、INR、日元、韓元、挪威克朗、紐西蘭元、盧布、瑞典克朗、新臺幣、美元、人民幣。

### <a name="deploying-a-solution-from-azure-marketplace"></a>從 Azure Marketplace 部署解決方案

**我已經將 Azure 應用商店虛擬機 (VM) 部署到訂閱,現在我想將訂閱從一個 Azure 帳戶遷移到另一個 Azure 帳戶。當前是否支援此功能?**

若要移轉 Azure 訂用帳戶 (包括 Azure Marketplace VM 和服務)，請先刪除或取消任何既有的 Azure 訂用帳戶，再關聯至新的 Azure 帳戶。 遷移完成後,將使用新註冊帳戶的付款方式對由此產生的使用費進行計費。

**我想將 Azure 應用商店虛擬機 (VM) 訂閱遷移到我的企業協定。當前是否支援此功能?**

若要將 Azure Marketplace 虛擬機器 (VM) 訂用帳戶移轉到 Enterprise 合約，請先停止或取消任何既有的訂用帳戶，再進行移轉。 完成 Azure 帳戶和相關訂閱的遷移後,可以重新購買 Azure 應用商店 VM 或服務。  產生的使用費用將會根據 Enterprise 合約按季計費。

### <a name="pricing-and-payment"></a>定價及付款

**Azure Marketplace 訂用帳戶如何計價？**

定價會因產品類型和發行者規格而有所不同。 軟體授權費用和 Azure 使用成本會透過 Azure 訂用帳戶分開收費。

*解除捆搭：*

+ *自帶許可證 (BYOL) 模型*:直接從發佈商或經銷商處獲取軟體許可證時,無需支付額外的軟體相關費用或費用。

*配套：*

Azure 訂閱包含在發佈者的獨立軟體供應商 (ISV) 解決方案定價中。

*指控:*

+ *免費:* 免費 SKU。 不收取軟體授權費用或使用供應項目的費用。

+ *免費軟體試用:* 優惠期限為有限。 在試用期內,發佈商的軟體許可費不收取任何費用。 到期後，則會根據發行者核定的標準費率自動轉換為付費供應項目。

+ *基於使用方式:* 房價根據產品使用範圍收費或計費。 虛擬機器映像會以每小時為基準收費。 對於開發人員服務和 API，則會按照供應項目定義的測量單位收費。

+ *固定費用:* SaaS 訂閱可以按月或按年計費的固定費用定價。 這還可以包括根據消耗量(例如頻寬、電子郵件或票證)收費的其他計費維度。 

+ *每個使用者:* SaaS 訂閱可以按使用者按月或按年計費定價。 

特定於優惠的定價詳細資訊可在 Azure 門戶上或[https://azure.microsoft.com/pricing/](https://azure.microsoft.com/pricing/)Azure[門戶]中的解決方案詳細資訊頁面上找到。

> [!Note]
> 除了月費外，所有定價模式都還會收取 Azure 使用費用 (除非另有指定)。

**我要如何提供 BYOL Marketplace 解決方案的軟體授權金鑰？Azure Marketplace 扮演什麼角色？**

取得與強制執行 BYOL 解決方案的授權認證，是發行者的責任。 對於虛擬機產品/服務,許可證密鑰的獲取通常在應用程式啟動後在發佈者的應用程序中進行。 使用透過 Azure 應用程式解決方案樣本部署的虛擬機器產品/服務時,可以將資源管理器範本配置為提示使用者輸入一系列輸入,包括許可證認證。

以下是每個供應項目類型最常見的選項：

*虛擬機器優惠:*

+ *選項 1:* 許可證金鑰的獲取通常在應用程式啟動後在發行者應用程式中進行。

+ *選項 2:* 在所選訂閱中部署 VM 產品/服務後,最終使用者(通過產品/服務提供的命令列/Web 介面)輸入許可證金鑰。 此授權可以是金鑰和/或檔案，由發行者決定。

*Azure 應用程式 (解決方案範本和受控應用程式)：*

+ *選項 1:* 可以配置資源管理器範本以提示輸入一系列輸入,包括許可證憑據。 此作業可在部署供應項目之前，在使用者訂用帳戶中藉由授權檔案 (檔案上傳) 或金鑰 (文字方塊輸入) 來完成。

+ *選項 2:* 您可以通過產品/產品/服務提供的命令列/Web界面輸入許可證密鑰。  在選取的訂用帳戶中部署 Azure 應用程式供應項目之後，即可執行此作業。 此授權可以是金鑰和/或檔案，由發行者決定。

**支援哪些類型的試驗?**

發佈商可以為付費 SaaS 優惠添加一個免費月,為 VM 映射添加一個或三個月的免費消費月。 免費試用優惠是帶有啟動試用請求的操作清單。 這些引導客戶訪問發佈者定義的網站以設置試用體驗。 試用版也可以添加到第一個月的免費付費產品中。 

**我需要登記付款方式 (例如信用卡) 才能部署免費層或自備授權 (BYOL) 供應項目嗎？**

否。 部署免費層或 BYOL 供應項目無需付款方式。 不過，免費試用供應項目則需要付款方式。 包含**立即取得**或是**免費試用軟體**按鈕的清單項目會部署到選取的 Azure 訂用帳戶中。  這些清單使用所選帳戶的註冊付款條件計費。 Azure 使用費用會與軟體授權費用分開計費。

**如果間接 Enterprise 合約 (EA) 客戶對於在 Azure Marketplace 上販售的供應項目有定價方面的問題，該與誰連絡？**

間接 Enterprise 合約 (EA) 客戶如有任何有關 Azure Marketplace 定價的問題，必須連絡其授權方案提供者 (LSP)。

**我可以控制員工對 Azure Marketplace 的存取權及其購買權限嗎？**

是，對於 Enterprise 合約 (EA) 客戶，註冊管理員可以在所有帳戶註冊時關閉購買權限，然後在一段足夠完成購買的時間內恢復此權限。

**商業市場購買支援哪些付款條件?**

客戶可以使用信用卡從商業市場購買產品。 如果您有現有的 Azure 訂閱,則從 Azure 應用商店購買將使用在帳戶上配置的付款條件,並在同一發票上顯示為單獨的行專案。 某些產品/服務會消耗 Azure 貨幣承諾,但大多數商業市場購買不會減少企業協議承諾,儘管 Azure 基礎結構消耗將。

**我可以運用帳戶上的 Azure 訂用帳戶點數或預付金來購買 Azure Marketplace 供應項目嗎？**

特定 Azure Marketplace 供應項目可以使用 Azure 訂用帳戶點數或預付金。 如需參與此方案的完整產品清單，請參閱 [Azure 預付金](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。 這些供應項目不包括 BYOL 或 BYOS 選項。 所有其他 Azure 應用商店產品/服務不能使用 Azure 訂閱積分或貨幣承諾:例如免費一個月試用積分、每月 MSDN 積分、Azure 促銷的信用額度、貨幣承諾餘額以及 Azure 提供的任何其他免費信用額度。

**大量授權折扣適用於 Azure Marketplace 購買嗎？**

否。 擁有 Azure Marketplace 所列解決方案的發行者可以設定價格。  標準 Microsoft 大量授權合約折扣不適用於 Azure Marketplace 購買。

**我可以在哪裡檢視 Azure Marketplace 訂閱詳細資料和帳單資訊？**

MOSP -[微軟線上訂閱計劃](https://azure.microsoft.com/support/legal/subscription-agreement/?country=us&language=en)(Web 直接)客戶可以在[Azure 門戶]的成本管理和計費部分的「發票」選項卡中查看應用商店購買詳細資訊。

企業協定 (EA) 客戶可以在[Azure 門戶]的成本管理 + 計費部分的「發票」選項卡中查看應用商店採購詳細資訊。

雲解決方案供應商 (CSP) 合作夥伴可以在「訂單歷史記錄」選項卡中選擇公司後,在「訂單歷史記錄」選項卡中查看合作夥伴中心中每個客戶的應用商店購買詳細資訊。

**如何取消 Azure VM 的 Azure Marketplace 附加元件？**

由於附加元件與 Azure 虛擬機器 (VM) 相關聯，因此您必須藉由刪除 VM 加以停止，才能取消 Azure Marketplace 購買。 如此即會停止 Azure Marketplace 購買的所有訂用帳戶使用量和收費。

**我的 Azure Marketplace 購買多久計費一次？**

不會從預付金當中扣除的所有 Azure Marketplace 供應項目，則會按月結算。 年度 SaaS 訂閱對全年服務收取一次費用。

MOSP - [Microsoft 線上訂閱計劃](https://azure.microsoft.com/support/legal/subscription-agreement/)(Web 直接)客戶每月根據其 Azure 訂閱配置檔存檔的同一信用卡收費。 年度 SaaS 訂閱對全年服務收取一次費用。

**我如何將 Azure Marketplace 購買從 MOSP 訂用帳戶移至直接 Enterprise 合約 (EA) 訂用帳戶？**

雖然大部分 Microsoft 訂用帳戶都可輕易轉換到 Enterprise 合約，但這些訂用帳戶內的 Azure Marketplace 購買不可轉換。

若要將購買自 Azure Marketplace 的其他服務移轉到 EA 訂用帳戶，請先在現有的 MOSP 訂用帳戶內取消應用程式，然後在 EA 訂用帳戶內重新購買這些應用程式。 這樣做之後，就可以對 Marketplace 服務訂閱之間可能重疊的月份範圍申請餘額退款 - 建立[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

**Azure 應用商店中虛擬機產品的成本結構*中的價格*、*軟體價格*和*總價*之間的差別是什麼?**

*價格*是指 Azure 虛擬機器運行軟體的成本。 *軟體價格*是指在 Azure 虛擬機器上運行的應用商店發行者軟體的成本。 *總價*是指在 Azure 虛擬機器上運行的 Azure 虛擬機器和應用商店發布者軟體的總成本。

**如何得知我已經使用多少 Azure Marketplace 購買？**

在[Azure 門戶]中可以找到估計的使用資訊。 此預估使用量資訊可能不含最近的活動，且可能以過去的使用量所推估的預測為準。 在公開預覽期間，此功能可能無法用於所有購買，且可能依產品類型而不同。

### <a name="customer-support"></a>客戶支援

**如有 Azure Marketplace 的一般支援問題，該與誰連絡？**

如需關於使用或疑難排解的一般應用程式支援，請直接連絡應用程式發行者。

如有關於 Azure Marketplace 購買的計費和訂用帳戶問題，請連絡 [Azure 支援](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)。

**對於 Azure Marketplace 上購買的解決方案，如需技術支援，該連絡誰？**

請連絡發行者以取得各種技術產品支援。 您可在 Azure Marketplace 的解決方案詳細資料頁面上找到發行者連絡資訊及 (或) 支援網站的連結。

**對於從 Marketplace 購買的第三方解決方案如需帳單支援或有問題，該與誰連絡？**

請經由 [Azure 支援](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)連絡 Microsoft 支援服務。

**如果我對 Azure Marketplace 上銷售的合作夥伴解決方案有定價或條款方面的疑問，該與誰連絡？**

請連絡發行者以取得各種技術產品支援。 您可在 Azure Marketplace 的各個解決方案詳細資料頁面上找到發行者連絡資訊及 (或) 支援網站的連結。

**如果不滿意，可以退貨嗎？**

無法退回從[Azure 應用商店](https://azuremarketplace.microsoft.com/)進行的採購,但可以取消/刪除。 基於消費的優惠按使用方式計費,因此當停止時,費用也停止。 訂閱將被取消,並且不會超過當前計費週期。 如果在購買后不久取消訂閱(每月24小時,每年14天),則提供全額退款。

客戶如有 Marketplace 服務或購買方面的任何技術性問題，必須直接與發行者連絡。 您可在 Azure Marketplace 的解決方案詳細資料頁面上找到發行者連絡資訊及 (或) 支援網站的連結。

**增加中期許可證時如何處理許可證添加?**

添加到現有訂閱的許可證在訂閱持續時間的剩餘時間內按比例評級。

**中期時如何處理許可證刪除?**

取消的許可證將受此常見問題解答中的退款政策的約束。  取消的所有許可證將立即從您的帳戶中刪除,並且將不再可供使用。

**支援基於消費的優惠退款嗎?**

任何基於消費的費用(無論是每小時 VM 還是自定義儀錶)均無法通過取消而退款。 消費一旦發生,費用將由 Microsoft 的商務平台處理,任何要求退款的服務品質爭議都將在發佈商和客戶之間的 Microsoft 系統之外直接處理。 支援統一費率加計量計費的優惠遵循統一費率費用的標準退款政策。

**是否可以在中期更改計劃?**

不,不可能在每月和每年之間轉換。

**客戶能否從同一優惠購買兩個計劃?**

是的,客戶可以同時擁有同一優惠中的兩個計劃。

**退款和計劃變更政策因網店而異嗎?**

不,整個商業市場的業務政策是一致的。 如果雲端解決方案提供計畫中的合作夥伴轉售,合作夥伴可能會為其客戶實施不同的策略。


## <a name="faq-for-publishers"></a>發行者的常見問題集

### <a name="what-you-need-to-know-about-the-commercial-marketplace"></a>您需要瞭解的商業市場

**什麼是 Azure Marketplace？**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 是線上應用程式和服務市集。 客戶 (大部分是 IT 專業人員和開發人員) 可以探索及購買以 Azure 建置或專為 Azure 建置的雲端軟體解決方案。 其目錄陳列了 8,000 餘種品項，例如虛擬機器 (VM)、API、解決方案範本、SaaS 應用程式和諮詢服務供應項目等 Azure 建置組塊。

Azure Marketplace 是與 Microsoft 共同舉辦各種 Go-To-Market 活動的起始點。  我們主要是要協助合作夥伴觸及更多客戶。 您可以發佈新的清單，也可以使用 Azure Marketplace 進行促銷及創造需求活動，與 Microsoft 共同執行聯合銷售/行銷活動。

**誰是 Azure Marketplace 的客戶？**

Azure Marketplace 適用於對商業 IT 軟體和服務感興趣的 IT 專業人員和雲端開發人員。

### <a name="azure-marketplace-for-publishers"></a>發佈商的 Azure 應用商店

**為何要在 Azure Marketplace 上發佈應用程式，這樣做對我有何好處？**

Azure Marketplace 提供了可讓 Microsoft 合作夥伴將產品與服務推廣並銷售給 Azure 客戶的市場。 發佈商可立即訪問 140 個全球市場、我們的 300,000 多個合作夥伴以及 Azure 的企業客戶網路。  該市場包括 90% 以上的財富 500 強公司和許多世界領先的開發人員。 Azure 應用商店中的新合作夥伴會自動獲得一套[無成本的上市權益](gtm-your-marketplace-benefits.md#list-trial-and-consulting-benefits),以説明提高在 Azure 市場中對其產品/服務的認識。

**Azure Marketplace 與 AppSource 之間的區別因素為何？**

Microsoft 合作夥伴可根據其目標受眾選擇發佈位置。

Microsoft 提供了兩個不同的雲市場網店 - Azure 應用商店和 AppSource。 這些店面可讓客戶尋找、試用及購買雲端應用程式和服務。 每個店面可滿足獨特的客戶需求，並且可讓 Microsoft 合作夥伴根據目標受眾決定其解決方案或服務的定位。

選取 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)，可將 IT 專業人員和開發人員或技術使用者作為目標受眾。

選取 [AppSource](https://appsource.microsoft.com/)，可將企業營運決策者和公司負責人作為目標受眾。

如需 Azure Marketplace 和 AppSource 的詳細資料和優點，請檢閱[發行者指南](marketplace-publishers-guide.md)。

**我必須成為 Microsoft 合作夥伴網路 (MPN) 的成員，才能在 Azure Marketplace 中陳列應用程式和服務嗎？**

是，必須是 MPN 才能在 Azure Marketplace 中發佈。 請前往 [Microsoft 合作夥伴網路](https://partner.microsoft.com/membership)開始進行。

**要在 Azure Marketplace 中發佈解決方案有哪些條件？**

若要在 Azure Marketplace 中發佈，合作夥伴必須證明其應用程式可在 Azure 上執行或加以擴充。 出版商必須為客戶提供[服務級別協定](https://azure.microsoft.com/support/legal/sla/)、[隱私政策](https://privacy.microsoft.com/privacystatement)以及電話和線上支援。 不同的工作負載會有其他需求。 如需進一步指引，請檢閱 [Azure Marketplace 參與原則](./marketplace-participation-policy.md)和[發行者指南](marketplace-publishers-guide.md)。

**在 Azure Marketplace 中發佈是否需要費用？**

透過 Azure Marketplace 上傳清單、試用版或 BYOL (自備授權) 時，不需要發佈費用。

**透過 Azure Marketplace 購買商品是否需要任何交易費用？**

透過 Azure Marketplace 購買解決方案授權時，軟體授權收益由發行者與 Microsoft 拆分。  收益拆分方式以 [Marketplace 發行者合約](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf)中的條款和條件為依據。 此外，BYOL (自備授權) 的發行者解決方案不會產生任何交易費用。

**什麼是標準合同?**

Microsoft 提供了發行者可以選擇使用的標準合同條款,以便客戶具有簡化的採購和法律審查流程。

**何處可以找到整合應用程式與 Azure Active Directory (AAD) 的相關指導方針？**

Microsoft 會使用 AAD 來驗證所有 Marketplace 使用者。  您可以直接佈建到試用版，而不需要額外的登入步驟。  例如，已驗證的使用者在點選 Marketplace 中列出的試用版後，即會重新導向至試用環境。

如需詳細資訊並開始啟用 AAD 的試用版，請造訪 [發行者指南中的 Azure Active Directory 小節](enable-appsource-marketplace-using-azure-ad.md)。

**如何開始進行開發人員中心註冊？**

開始之前，發行者應確認他們尚未註冊[開發人員中心帳戶](deprecated/register-dev-center.md) (以防止重複註冊)。 確認之後，接著即應使用 Microsoft 帳戶[登入](https://account.microsoft.com/account/)以進行註冊，此作業與開發人員帳戶有關。

如果您還沒有 Microsoft 帳號,則可以[建立一個帳戶](https://signup.live.com/)(例如contoso_marketplace@live.com: 。

**為何需要開發人員中心帳戶？**

必須要有開發人員中心帳戶，Microsoft 才能代表發行者向客戶收取交易清單類型的費用。 開發人員中心帳戶註冊可讓 Microsoft 驗證法律、稅務和銀行相關資訊。 如需詳細資訊，請參閱[在開發人員中心註冊](./partner-center-portal/create-account.md)。

**什麼是潛在客戶？作為 Marketplace 中的發行者，潛在客戶對我而言有何重要性？**

潛在客戶是從 Marketplace 部署您產品的客戶。 無論您的產品是列在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 還是 [AppSource](https://appsource.microsoft.com)，您都可以獲得對您的產品感興趣的潛在客戶。  您可以設定供應項目的潛在客戶目的地。 若要深入了解，請[成為雲端市集發行者](./partner-center-portal/create-account.md)。

**可以在哪裡取得設定我潛在客戶目的地的說明？**

在[合作夥伴中心獲取客戶線索](./cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md)文檔中瞭解更多資訊,或通過選擇產品/服務類型[https://aka.ms/marketplacepublishersupport](https://aka.ms/marketplacepublishersupport)和潛在 顧客管理提交支援票證。

**我是否必須設定潛在客戶目的地，才能在 Marketplace 上發佈供應項目？**

是，如果要發佈**與我連絡**、**SaaS 應用程式**或**諮詢服務**供應項目，您必須設定潛在客戶目的地。

**如何確認潛在客戶設定是否正確？**

完成報價並設置潛在顧客目的地後,可以在[合作夥伴中心](https://cloudpartner.azure.com/)正確發佈清單。 陳列商品上架之前，您可以驗證潛在客戶組態設定是否正確運作。  請將測試潛在客戶傳送至您在供應項目中設定的潛在客戶目的地。

**Azure Marketplace 中有哪些國家/地區可讓發行者銷售產品？**

位於以下國家/地區的發佈商目前可以在 Azure 應用商店中銷售:阿富汗、阿爾巴尼亞、阿爾及利亞、 安哥拉、安提瓜和巴布達、阿根廷、亞美尼亞、澳大利亞、奧地利、亞塞拜然、巴林、孟加拉國、白俄羅斯、比利時、貝南、玻利維亞、波士尼亞和黑塞哥 維那、波札那、巴西、保加利亞、布吉納法索、布隆迪、柬埔寨、喀麥隆、加拿大、中非共和國、查德、智利、哥倫比亞、科摩羅、剛果、剛果(金)、哥斯大黎加、象牙海岸、克羅埃西亞、塞普勒斯、捷克共和國、丹麥、多米尼克、多明尼加共和國、厄瓜多、埃及、薩爾瓦多 衣索比亞、斐濟群島、芬蘭、法國、格魯吉亞、德國、迦納、希臘、瓜地馬拉、幾內亞、海地、宏都拉斯、香港特別行政區、匈牙利、冰島、印度、印尼、伊拉克、愛爾蘭、以色列、義大利、牙買加 、日本、約旦、哈薩克、肯亞、韓國(南)、科威特、寮國、拉脫維亞、黎巴嫩、賴比瑞亞、列支敦斯登、立陶宛、盧森堡、馬達加斯加、馬拉威、馬來西亞、馬里、馬爾他、毛裡求斯、墨西哥、摩納哥、蒙古、黑山、摩洛哥、莫三比克、尼泊爾、荷蘭、紐西蘭、尼加拉瓜、尼日爾、奈及利亞、挪威、阿曼、 巴基斯坦、巴拿馬、巴拉圭、秘魯、菲律賓、波蘭、葡萄牙、卡達、羅馬尼亞、俄羅斯、盧安達、沙特阿拉伯、塞內加爾、塞爾維亞、塞拉利昂、新加坡、斯洛伐克、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、索馬里、斯洛伐克、斯洛維尼亞、索馬里、斯洛伐克、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索馬里、斯洛維尼亞、索 南非、西班牙、斯里蘭卡、瑞典、瑞士、塔吉克、坦尚尼亞、泰國、東帝汶、多哥、湯加、特立尼達和多巴哥、突尼西亞、土耳其、土庫曼、烏干達、烏克蘭、阿拉伯聯合大公國、聯合王國、美國、烏拉圭、烏茲別克、委內瑞拉、越南

**如何從 Azure Marketplace 刪除陳列的商品？**

*虛擬機器& Azure 應用:*

1. 登入[合作夥伴中心](https://cloudpartner.azure.com/)。
1. 從「**所有優惠**」選項卡中選擇優惠。
1. 在螢幕左側的窗格中,選擇**SKU**選項卡。
1. 選擇要刪除的 SKU,然後單擊該 SKU 的**刪除**按鈕。
1. 將供應項目[重新發佈至](./cloud-partner-portal/manage-offers/cpp-publish-offer.md) Azure Marketplace。

如需詳細資訊，請參閱[刪除供應項目](./cloud-partner-portal/manage-offers/cpp-delete-offer.md)。

*Web 應用(SaaS 應用、載入項)&咨询服务:*

1. 在合作夥伴中心中,選擇問號圖示,然後單擊"**支援**"。
1. 移至 <https://go.microsoft.com/fwlink/?linkid=844975> 。
1. 在支援頁面上，選取供應項目類型。
1. 選擇 **「刪除**已發佈產品/服務」。
1. 建立事件票證。
1. 提交。

*O365 應用:*

1.    <https://sellerdashboard.microsoft.com>使用開發帳戶登錄。
1.    撤回外接程式。

    > [!NOTE]
    > 應用在 90 天後從現有清單中消失。

**為什麼我的更改沒有反映在優惠中?**

只有在重新發佈產品/服務后,合作夥伴中心內所做的更改才會在系統和商店方面進行更新。 請確保您已提交發佈要約後進行任何修改。

### <a name="benefits-and-go-to-market-gtm-resources"></a>權益與 Go-To-Market (GTM) 資源

**在 Azure Marketplace 上刊登的發行者會有哪些 Go-To-Market 權益？**

Azure Marketplace 是與 Microsoft 共同舉辦 Go-To-Market 活動的起始點，也是建立「共同銷售準備就緒」合作關係的途徑。 Azure 應用商店中的所有新上市專案都自動提供一套[免費上市優惠](https://assetsprod.microsoft.com/mpn/marketplace-gtm-benefits.pdf),以説明提高 Microsoft 客戶對優惠的意識。 供應項目發佈後，Microsoft GTM 小組會與您連絡，並開始提供您的權益。

如需 GTM 權益及如何在市集中擴展業務的詳細資訊，請瀏覽 [Microsoft GTM 服務](https://partner.microsoft.com/reach-customers/gtm)。

**Azure Marketplace 解決方案會在 Microsoft 網頁內容的何處進行促銷？**

Azure 應用商店解決方案可在Microsoft [Azure門戶]和[Azure應用商店網站](https://azuremarketplace.microsoft.com/marketplace/)中提供。 使用 Azure 的雲端開發人員和 IT 專業人員每次登入時都會看到合作夥伴解決方案。 [Azure Marketplace 首頁](https://azuremarketplace.microsoft.com/marketplace)和 [Azure 解決方案頁面](https://azure.microsoft.com/solutions/)上也會輪流展示一部分的合作夥伴解決方案。

### <a name="billing-and-payments"></a>帳單及付款

**我將以何種方式收到 Azure Marketplace 銷售款項？**

所有來自 Microsoft 的款項都會按月透過 PayPal 或電匯 (EFT) 完成處理。 付款會在客戶使用服務當日起的兩個月內完成，但實際時間則取決於客戶的付款方式。 信用卡客戶有 45 天的委付期。

**所購買的虛擬機器解決方案若是依使用量計費，當客戶擴充或縮減基礎虛擬機器時，我的軟體授權價格是否會調整？**

是，將會立即以新價格計費。  當客戶變更虛擬機器大小，並在定價表中指定以虛擬機器大小為基礎的不同價格時，定價就會變更。

**按節點計費的方式是否適用於 Azure Marketplace？**

Azure Marketplace 目前不支援按虛擬機器節點計費。 發行者仍可根據 Microsoft VM 計費費率來確認按節點計費的費率。  計算 VM 數目乘以使用時數和每小時的費率，即可確認。

**如有帳單或供應項目管理的問題，該與誰連絡？**

請向 [Microsoft 支援服務](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=15635)登錄票證。

### <a name="publisher-support"></a>發行者支援

**如有 Azure Marketplace 的一般支援問題，該與誰連絡？**

有關可用性或故障排除的一般應用程式支援,請聯絡[合作夥伴中心支援](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636565784998876007)。

如有關於 Azure Marketplace 購買的計費和訂用帳戶問題，請連絡 [Azure 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

**如有發佈或供應項目管理的問題，該與誰連絡？**

有關常見問題的最新資源和文件,請造[訪商業市場發行者指南](marketplace-publishers-guide.md)。 此外,您還可以[在合作夥伴中心使用Microsoft支援](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)登錄票證。

### <a name="azure-marketplace-for-publishers"></a>發佈商的 Azure 應用商店

**如何定義我的地理位置可用性,以便在不同國家/地區進行銷售?**

1.  在合作夥伴中心中,導航到要向其添加新國家/地區 SKU。  在**SKU 詳細資訊**中,導航到**國家/地區可用性**,然後單擊 **「選擇區域**」。。

    ![選擇區域](media/marketplace-publishers-guide/FAQ-choose-geo.png)

1.  將彈出一個清單,所有可用的國家/地區將銷售到。  單擊要使此 SKU 可用的每個國家/地區旁邊的複選框。 按一下 [確定]  。

    ![選擇國家/地區清單](media/marketplace-publishers-guide/FAQ-select-countries.png)

1.  最後,要將更改應用於即時優惠,請單擊"**發佈**"。  

    > [!Note]
    > 更改生效需要24小時。

<!---    ![Publish offer](media/marketplace-publishers-guide/FAQ-publish-offer.png) -->

**發行者如何更改現有產品/服務的地理可用性?**

發行者可以編輯現有產品/服務,選擇新的國家/地區,並使用電子錶格下載/上傳功能設置定價。

**客戶可以在哪些國家/地區購買 Azure 應用商店產品?**

Azure 應用商店支援由客戶的帳單位址定義的 141 個從購買地。 有關國家/地區清單,請參閱[參與政策](/legal/marketplace/participation-policy)。

**Azure 應用商店支援哪些貨幣?**

交易方式如下:澳元、BRL、加元、瑞士法郎、丹麥克朗、歐元、英鎊、INR、日元、韓元、挪威克朗、紐西蘭元、盧布、瑞典克朗、新臺幣和美元。

### <a name="pricing-and-payment"></a>定價及付款

**免費層與免費試用軟體有何不同？**

「免費層」訂用帳戶供應項目是永久免費的。  「免費軟體試用」(「立即試用」) 供應項目是付費訂閱，只能在限定期間內免費使用。

**驗證端到端採購和預配流的流程是什麼?**

在發佈過程中,您將獲得對優惠預覽的訪問許可權。 訪問許可權僅限於您在「預覽」選項卡中指定的使用者,這是其他人看不到的即時優惠。 您可以購買此項並測試流程;但是,您將根據產品/服務配置向您收取全額費用。

為了以極低的價格完成購買,Microsoft 建議以您可以接受的價格為自己發佈一個私人計劃,作為測試成本。 零支援,但不會反映完整的體驗,如付費訂閱。

**微軟是否會在標準政策之外提供退款?**

是的,根據您的請求通過支援票證,Microsoft 將處理給您的客戶的積分,如果您認為合適。

## <a name="next-steps"></a>後續步驟

訪問[商業市場發佈者指南](/azure/marketplace/marketplace-publishers-guide)頁面。


[Azure 入口網站]: https://portal.azure.com
