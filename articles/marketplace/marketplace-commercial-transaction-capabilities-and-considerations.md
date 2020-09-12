---
title: Microsoft 商業市集交易功能
description: 此文章描述商業市集交易選項的定價、計費、發票開立和付款考量。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ca8f2e1721c99c79188917e5b44950b8db6895ce
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006392"
---
# <a name="commercial-marketplace-transact-capabilities"></a>商業市集交易功能

本文說明 Microsoft 商用 marketplace 的定價、計費、發票和付款考慮。 

## <a name="transactions-by-listing-option"></a>交易（依清單選項）

發行者或 Microsoft 負責管理商業市集中供應項目的軟體授權交易。 您為供應專案選擇的清單選項，會決定誰管理交易。 如需每個發行選項的可用性和說明，請參閱 [挑選清單選項](./determine-your-listing-type.md#choose-a-listing-option) 。

### <a name="contact-me-free-trial-and-byol-options"></a>聯絡我、免費試用和 BYOL 選項

發行者可以選擇「 _聯絡我_ 」和「 _免費試用_」選項，以供促銷和使用者取得之用。 針對某些供應專案類型，發行者可以選擇 [自備授權 (BYOL) ] 選項，讓客戶使用他們直接向您購買的授權購買供應專案的訂用帳戶。 使用這些發行選項，Microsoft 不會在發行者的軟體授權交易中直接參與，且沒有相關的交易費用。 

發行者負責支援軟體授權交易的所有層面。 這包括但不限於訂單、履行、計量、計費、發票開立、付款和收集。 使用 [Contact me] 清單選項，發行者會保留從客戶收集的發行者軟體授權費用100%。

### <a name="transact-publishing-option"></a>交易發行選項

選擇透過 Microsoft 銷售會利用 Microsoft 商務功能，並提供從探索和評估到購買和實行的端對端體驗。 其中一個可交易的供應專案，就是 Microsoft 可代表發行者來促進軟體授權交換的款項。 可交易供應專案會根據現有的 Microsoft 訂用帳戶或信用卡計費，讓 Microsoft 能夠代表發行者託管雲端 marketplace 交易。

當您在合作夥伴中心建立新供應項目時，請選擇交易選項。 只有在您的供應項目類型可使用交易時，才會顯示此選項。

## <a name="transact-overview"></a>交易概觀

使用「交易」選項時，Microsoft 會啟用協力廠商軟體的銷售，並將某些供應專案類型部署至客戶的 Azure 訂用帳戶。 當您選取供應專案的定價模式時，發行者必須考慮基礎結構費用和您自己的軟體授權費用。

交易發行選項目前支援下列供應項目類型：

- 虛擬機器
- Azure 應用程式
- SaaS 應用程式

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

針對 **虛擬機器** 和 **azure 應用程式**，azure 基礎結構使用費用會向客戶的 azure 訂用帳戶收取費用。 基礎結構的使用費用和軟體提供者的授權費用，在客戶的發票上會分開計價及呈現。

對於 **SaaS 應用程式**，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。  其以一般費用向客戶呈現。 Azure 基礎結構的使用方式會直接由發行者管理並計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 軟體授權費用不是計量付費或根據使用者的耗用量。

## <a name="pricing-models"></a>定價模式

根據所使用的交易選項，訂用帳戶費用如下：

- **立即取得 (免費) ** –軟體授權不收費。 使用免費供應專案時，不會向客戶收取 Azure Marketplace 費用。 免費優惠無法轉換成付費供應專案。 客戶必須訂購付費供應專案。
- **自備授權** (BYOL) - 軟體授權任何適用的費用，在發行者與客戶之間直接管理。 Microsoft 僅處理 Azure 基礎結構費用。 如果供應專案列在商業 marketplace 中，則在商業市場以外取得存取或使用供應專案的客戶不會收取商業 marketplace 費用。
- **訂** 用帳戶定價–軟體授權費用以每月或每年的週期性訂用帳戶費用顯示，以固定費率或每一基座計費。 針對長期客戶取消或未使用的服務，週期性的訂用帳戶費用不會按比例計算。 如果客戶在訂用帳戶期限內升級或降級其訂用帳戶，則週期性訂用帳戶費用可能會按比例計算。
- 以**使用量為基礎的定價**–針對 Azure 虛擬機器供應專案，客戶會根據其使用供應專案的程度向您收費。 針對虛擬機器映射，客戶需依發行者所設定的每小時 Azure Marketplace 費用計費，以使用從 VM 映射部署的虛擬機器。 每小時的費用依虛擬機器的大小而採均一價或變動價。 不到一小時以分鐘計。 方案會按月計費。
- **計量付費定價** –針對 Azure 應用程式供應專案和 SaaS 供應專案，發行者可以使用 [Marketplace 計量服務](./partner-center-portal/marketplace-metering-service-apis.md) ，根據所選的計量維度來收取耗用量的費用。 例如，已處理的頻寬、票證或電子郵件。 發行者可以為每個方案定義一個或多個計量維度。 發行者負責追蹤個別客戶的使用方式，以及供應專案中定義的每個計量。 事件在一小時內應回報給 Microsoft。 Microsoft 會根據發行者就適用計費期間報告的使用量資訊向客戶收費。
- **免費試用** –根據供應專案類型而定，從30天到六個月的軟體授權免費。 如果發行者在相同供應專案內的多個方案上提供免費試用，客戶可以切換到另一個方案的免費試用，但試用期不會重新開機。 針對虛擬機器供應專案，客戶會向 Azure 基礎結構收取在試用期間使用供應專案的費用。 在試用期到期後，除非客戶在試用期間結束前取消，否則會自動向客戶收取根據標準費率所嘗試的最後一個計畫。

> [!NOTE]
> 在使用解決方案之後根據使用量計費的供應項目不適合退款。

若發行者想要變更與供應專案相關聯的使用費用，應先從商業 marketplace 移除供應專案 (或供應專案) 內的特定方案。 移除應根據 [Microsoft 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560)的需求來完成。 然後，發行者可以在供應專案) 中發佈新的供應專案 (或方案，包括新的使用費用。 如需移除供應專案或方案的相關資訊，請參閱 [停止銷售供應專案或方案](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)。

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>免費、跟我，以及自備授權 (BYOL) 定價

當您使用來發佈供應專案時，請立即 (免費) 、Contact me 或 BYOL 選項，Microsoft 不會在促進軟體授權費用的銷售交易方面扮演角色。 就像清單和免費試用版發行選項一樣，發行者仍保有100% 的軟體授權費用。

### <a name="usage-based-and-subscription-pricing"></a>依使用量和訂用帳戶定價

當發行供應專案的使用者或訂閱交易時，Microsoft 會提供技術和服務來處理軟體授權購買、退貨及退款。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 讓客戶能訂購、授權和使用您的軟體，客戶必須遵守 Microsoft 商業市集和您的使用者授權合約的條款與條件。 建立供應項目時，您必須提供您自己的使用者授權合約，或選取[標準合約](./standard-contract.md)。

### <a name="free-software-trials"></a>免費軟體試用

針對交易發行案例，您可以根據訂用帳戶，讓軟體授權免費提供30到120天。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用供應項目類型和計費模型來創造供應項目的獲利，您也可以交易私人供應項目，按照交涉、交易特定的定價或自訂設定來完成。 所有 3 個交易發行選項都支援私人供應項目。

此選項允許高於或低於公開提供供應項目的定價。 私人供應項目可用於為供應項目折扣或加入進階功能。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。

### <a name="examples"></a>範例

**以使用量為基礎** 

以使用量為基礎的定價具有下列成本結構：

|授權成本  | 每小時美金 $1.00 元   |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $1.14 元*       |
||

在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $1.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $1.14 元  |
|---------|---------|
|Microsoft 向您支付授權成本的 80%|   每小時美金 $0.80 元     |
|Microsoft 保留授權成本的 20%  |  每小時美金 $0.20 元       |
|Microsoft 保留 Azure 使用量成本的 100% | 每小時美金 $0.14 元 |
||

**自備授權 (BYOL)**

BYOL 具有下列成本結構：

|授權成本  | 由您協商和收取的授權費用  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $0.14 元*       |
||

在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $0.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $0.14 元  |
|---------|---------|
|Microsoft 保留 Azure 使用量成本    |   每小時美金 $0.14 元     |
|Microsoft 保留授權成本的 0%   |  每小時美金 $0.00 元       |
||

**SaaS 應用程式訂用帳戶**

此選項必須設定為透過 Microsoft 銷售，而且可以依每月或每年基準，以固定費率或每一使用者價格收費。 如果您針對 SaaS 供應項目啟用 [透過 Microsoft 銷售] 選項，就會有下列成本結構：

| 授權成本       | 每月 $100.00  |
|--------------|---------|
| Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
| *由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |
||

在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。

在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $90.00 給發行者：

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \* Microsoft 針對任何符合資格的 SaaS 應用程式支付 90% 授權成本給您   |   每月 $80.00 <br> \* 每月 $90.00    |
|Microsoft 保留授權成本的 20% <br> \* Microsoft 針對任何符合資格的 SaaS 應用程式保留 10% 授權成本。  |  每月 $20.00 <br> \* $10.00     |

### <a name="reduced-service-fee"></a>降低服務費用

針對您在商業市集中發佈的特定供應專案，Microsoft 會將其 Marketplace 服務費用從 20% (降低，如 [Microsoft 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560)) 至10% 所述。 若您的供應專案 () 符合資格，則您的供應專案 (s) 必須由 Microsoft 指定為 Azure IP 共同銷售受到激勵。 在每個行事曆月份結束前至少必須符合五個工作天的資格，才能獲得每月減少的 Marketplace 服務費用。 降低的 Marketplace 服務費用適用于 Azure IP 共同銷售受到激勵 SaaS、Vm、受控應用程式，以及透過商業市場提供的任何其他合格可交易 IaaS 供應專案。 與一個 Microsoft 小組應用程式相關聯的付費 SaaS 供應專案，或至少兩個 Microsoft 365 增益集 (Excel、PowerPoint、Word、Outlook 和 SharePoint) ，並在 AppSource 上發佈也會收到這項折扣。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**開立發票和付款** - 您可以使用客戶慣用的發票開立方法來傳遞月租方案或 PAYGO 軟體授權費用。

**Enterprise 合約** - 如果客戶慣用的發票開立方法是 Microsoft Enterprise 合約，您的軟體授權費用會使用此發票開立方法來計費，作為分項成本獨立於任何 Azure 特定的使用量成本。

**信用卡和每月發票** - 客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

**免費點數與金錢履約承諾** - 有些客戶選擇要使用金錢履約承諾，在 Enterprise 合約中預付給 Azure，或已收到用於 Azure 的免費點數。 雖然可以使用這些點數來支付 Azure 使用量，但不能用它們支付發行者軟體授權費用。

**帳單和集合** –發行者軟體授權計費會使用客戶選取的發票方法來顯示，並遵循發票付費時間軸。 系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

在選取月租方案或隨用隨付定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

客戶通常會使用 Enterprise 合約或以信用卡付款的隨用隨付合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE]
>交易發行選項的所有報告和深入解析，都可透過合作夥伴中心的 [分析] 區段取得。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱合作夥伴中心) 提供的 [Microsoft 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560) (。

如需帳單問題的說明，請連絡[商業市集發行者支援](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應項目類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

- 交易發行選項需要 Microsoft 帳戶和財務資訊，不論該供應項目的定價模型為何。
- 必要的財務資訊包括付款帳戶和稅務設定檔。
- 發行者必須存留在支援的 [國家或地區](sell-from-countries.md)。

如需有關設定這些帳戶的詳細資訊，請參閱 [合作夥伴中心中的管理您的商業 marketplace 帳戶](partner-center-portal/manage-account.md)。

### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型：

- **Azure 虛擬機器** –從免費、自備授權或以使用量為基礎的定價模型中選取，並以供應專案層級定義的方案形式呈現。 在客戶的 Azure 帳單上，Microsoft 會將發行者軟體授權費用與基礎 Azure 基礎結構費用分開呈現。 Azure 基礎結構費用是由使用發行者軟體而產生。

- **Azure 應用程式：解決方案範本或受控應用** 程式–必須布建一或多部虛擬機器，並提取虛擬機器定價的總和。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在某些情況下，Azure 基礎結構使用費用會與軟體授權費用分開傳遞給客戶，但是在相同的帳單上。 但是，如果您針對 ISV 基礎結構費用設定受控應用程式供應專案，則會向「發行者」收取 Azure 資源的費用，而且客戶會收到包含基礎結構、軟體授權和管理服務成本的固定費用。

- **Saas 應用程式** -必須是多租使用者解決方案、使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 進行驗證，然後與 [SaaS 履行 api](partner-center-portal/pc-saas-fulfillment-api-v2.md)整合。 Azure 基礎結構使用量會直接由您 (合作夥伴) 來管理及計費，因此您必須將 Azure 基礎結構使用費用和軟體授權費用視為單一成本專案。 如需詳細指引，請參閱 [在商業市場中建立新的 SaaS 供應](partner-center-portal/create-new-saas-offer.md)專案。

## <a name="next-steps"></a>後續步驟

- 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
- 請參閱線上商店的發佈模式，以取得解決方案如何對應至供應專案類型和設定的範例。
