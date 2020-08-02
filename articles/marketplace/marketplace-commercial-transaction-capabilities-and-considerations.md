---
title: Microsoft 商業市集交易功能
description: 此文章描述商業市集交易選項的定價、計費、發票開立和付款考量。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: e2ce35158e8f9a3a2ad9da2b3d67a3f35d5a8c80
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503560"
---
# <a name="commercial-marketplace-transact-capabilities"></a>商業市集交易功能

## <a name="transactions-by-publishing-option"></a>依發行選項的交易

發行者或 Microsoft 負責管理商業市集中供應項目的軟體授權交易。 您為供應項目選擇的發行選項會決定誰負責管理交易。 如需每個發行選項的可用性和說明，請參閱[決定您的發行選項](./determine-your-listing-type.md#choose-a-call-to-action)。

### <a name="list-trial-and-byol-publishing-options"></a>清單、試用版和 BYOL 發行選項

具有現有商務功能的發行者可以選擇清單、試用版和自備授權 (BYOL) 發行選項，以供促銷和使用者取得之用。 使用這些發行選項，Microsoft 不會在發行者的軟體授權交易中直接參與，且沒有相關的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。

### <a name="transact-publishing-option"></a>交易發行選項

交易發行選項會利用 Microsoft 商務功能，並提供從探索和評估到購買與實作的端對端體驗。 交易供應項目是依據現有的 Microsoft 訂用帳戶或信用卡來計費，讓 Microsoft 可以代表發行者主控雲端市集交易。

當您在合作夥伴中心建立新供應項目時，請選擇交易選項。 在 [設定詳細資料] 下的 [供應項目設定] 頁面上，選取 [是，我想要透過 Microsoft 銷售，並且讓 Microsoft 代表我主控交易]。 只有在您的供應項目類型可使用交易時，才會顯示此選項。

## <a name="transact-overview"></a>交易概觀

當使用交易發行選項時，Microsoft 會啟用對客戶的 Azure 訂用帳戶進行第三方軟體銷售和一些供應項目類型的部署。 身為發行者的您在選取計費模型和供應項目類型時，必須考慮基礎結構費用的計費，以及您自己的軟體授權費用。

交易發行選項目前支援下列供應項目類型：

- 虛擬機器
- Azure 應用程式
- SaaS 應用程式

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

針對**虛擬機器**和**azure 應用程式**，azure 基礎結構使用量費用會向客戶的 Azure 訂用帳戶收費。 基礎結構的使用費用和軟體提供者的授權費用，在客戶的發票上會分開計價及呈現。

對於 **SaaS 應用程式**，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。  其以一般費用向客戶呈現。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

視使用的交易選項而定，軟體授權費用如下所示：

- **免費** - 軟體授權不收費。
- **自備授權** (BYOL) - 軟體授權任何適用的費用，在發行者與客戶之間直接管理。 Microsoft 僅處理 Azure 基礎結構費用。 這僅適用于虛擬機器和 Azure 應用程式。
- **隨用隨付** - 軟體授權費用會依使用的 Azure 基礎結構，呈現為每小時、每核心 (vCPU) 費率。 這僅適用于虛擬機器和 Azure 應用程式。
- **訂用帳戶定價** - 軟體授權費用會以每月或每年為單位呈現，以固定費率或每一基座的週期性費用計費。 這適用于 SaaS 應用程式（每月或每年），以及 Azure 應用程式管理的應用程式（每月）。
- **軟體免費試用版** - 30 天或 90 天軟體授權不收費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

在發行隨用隨付或月租方案交易供應項目的情況下，Microsoft 會提供處理軟體授權購買、退貨及退款的技術和服務。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 讓客戶能訂購、授權和使用您的軟體，客戶必須遵守 Microsoft 商業市集和您的使用者授權合約的條款與條件。 建立供應項目時，您必須提供您自己的使用者授權合約，或選取[標準合約](./standard-contract.md)。

### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，您可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用供應項目類型和計費模型來創造供應項目的獲利，您也可以交易私人供應項目，按照交涉、交易特定的定價或自訂設定來完成。 所有 3 個交易發行選項都支援私人供應項目。

此選項允許高於或低於公開提供供應項目的定價。 私人供應項目可用於為供應項目折扣或加入進階功能。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。

### <a name="examples"></a>範例

**隨用隨付** 

隨用隨付具有下列成本結構：

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

已符合「優惠市集服務費用」資格的合作夥伴，將會看到從 2019 年 5 月至 2020 年 6 月的 SaaS 供應項目優惠交易費用。

在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $90.00 給發行者：

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \* Microsoft 針對任何符合資格的 SaaS 應用程式支付 90% 授權成本給您   |   每月 $80.00 <br> \* 每月 $90.00    |
|Microsoft 保留授權成本的 20% <br> \* Microsoft 針對任何符合資格的 SaaS 應用程式保留 10% 授權成本。  |  每月 $20.00 <br> \* $10.00     |

對於您在商業 marketplace 上發佈的特定供應專案，Microsoft 會將其 Marketplace 服務費用從20% （如 Microsoft 發行者合約中所述）降到10%。 若您的供應專案符合資格，Microsoft 必須將您的供應專案指定為 Azure IP 共同銷售受到激勵。 在每個日曆月份結束前，至少必須符合五（5）個工作天的資格，才能收到該月份的縮減 Marketplace 服務費用。 降低的 Marketplace 服務費用適用于 Azure IP 共同銷售受到激勵 SaaS、Vm、受管理的應用程式，以及透過商業 Marketplace 提供的任何其他合格 transactable IaaS 供應專案。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**開立發票和付款** - 您可以使用客戶慣用的發票開立方法來傳遞月租方案或 PAYGO 軟體授權費用。

**Enterprise 合約** - 如果客戶慣用的發票開立方法是 Microsoft Enterprise 合約，您的軟體授權費用會使用此發票開立方法來計費，作為分項成本獨立於任何 Azure 特定的使用量成本。

**信用卡和每月發票** - 客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

**免費點數與金錢履約承諾** - 有些客戶選擇要使用金錢履約承諾，在 Enterprise 合約中預付給 Azure，或已收到用於 Azure 的免費點數。 雖然可以使用這些點數來支付 Azure 使用量，但不能用它們支付發行者軟體授權費用。

**計費和收款** - 發行者軟體授權計費是使用客戶所選取的發票開立方法來呈現，並遵循發票開立時間軸。 系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

在選取月租方案或隨用隨付定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

客戶通常會使用 Enterprise 合約或以信用卡付款的隨用隨付合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE]
>交易發行選項的所有報告和深入解析，都可透過合作夥伴中心的 [分析] 區段取得。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱[發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx) (可在合作夥伴中心取得)。

如需帳單問題的說明，請連絡[商業市集發行者支援](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應項目類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

- 交易發行選項需要 Microsoft 帳戶和財務資訊，不論該供應項目的定價模型為何。
- 必要的財務資訊包括付款帳戶和稅務設定檔。

如需有關設定這些帳戶的詳細資訊，請參閱[在合作夥伴中心管理您的商業 marketplace 帳戶](partner-center-portal/manage-account.md)。

### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型：

- **虛擬機器**–從免費、自備授權或隨用隨付定價模型中選取，並以供應專案層級定義的方案形式呈現。 在客戶的 Azure 帳單上，Microsoft 會將發行者軟體授權費用與基礎 Azure 基礎結構費用分開呈現。 Azure 基礎結構費用是由使用發行者軟體而產生。

- **Azure 應用程式：解決方案範本或受管理的應用**程式–必須布建一或多部虛擬機器，並提取虛擬機器定價的總和。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在某些情況下，Azure 基礎結構使用費用會與軟體授權費用分開傳遞給客戶，但是在相同的帳單上。 不過，如果您為 ISV 基礎結構費用設定受控應用程式供應專案，Azure 資源會向發行者收費，而客戶會收到包含基礎結構、軟體授權和管理服務成本的固定費用。

- **Saas 應用程式**-必須是多租使用者解決方案，使用[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)進行驗證，並與[SaaS 履行 api](partner-center-portal/pc-saas-fulfillment-api-v2.md)整合。 Azure 基礎結構使用量是直接由您（合作夥伴）管理和計費，因此您必須將 Azure 基礎結構使用費用和軟體授權費用視為單一成本專案來加以考慮。 如需詳細指引，請參閱[在商業 marketplace 中建立新的 SaaS 供應](partner-center-portal/create-new-saas-offer.md)專案。

## <a name="next-steps"></a>後續步驟

- 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
- 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
