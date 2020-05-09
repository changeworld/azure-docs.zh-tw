---
title: 商業 marketplace 交易功能
description: 本文說明 [商業 marketplace] 交易選項的定價、計費、發票處理和支出考慮。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 4aeae69dd50e8c233a1903f6f2c7bd7795b8d7b9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857227"
---
# <a name="commercial-marketplace-transact-capabilities"></a>商業 marketplace 交易功能

## <a name="transactions-by-publishing-option"></a>依發行選項的交易

發行者或 Microsoft 必須負責管理商業 marketplace 中供應專案的軟體授權交易。 您為供應專案選擇的發佈選項會決定誰負責管理交易。 如需每個發佈選項的可用性和說明，請參閱[決定發行選項](./determine-your-listing-type.md#choose-a-publishing-option)。

### <a name="list-trial-and-byol-publishing-options"></a>清單、試用版和 BYOL 發行選項

具有現有商務功能的發行者可以挑選清單、試用版和自備授權（BYOL）發行選項，以供促銷和使用者取得之用。 使用這些選項時，Microsoft 不會直接參與發行者的軟體授權交易，也不會有相關聯的交易費用。 發行者負責支援軟體授權交易的所有層面，包括但不限於訂單、履行、計量、計費、發票、付款和集合。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。

### <a name="transact-publishing-option"></a>交易發行選項

交易發行選項會利用 Microsoft 商務功能，並提供從探索和評估到購買與實施的端對端體驗。 交易供應專案是依據現有的 Microsoft 訂用帳戶或信用卡計費，讓 Microsoft 可以代表發行者裝載雲端 marketplace 交易。

當您在合作夥伴中心建立新供應專案時，請選擇 [交易] 選項。 在 [**供應專案設定**] 頁面的 [**設定詳細資料**] 底下，選取 [是，我想要透過 microsoft 銷售，並代表我進行 microsoft 主機交易]。 只有在您的供應專案類型可使用 [交易] 時，才會顯示此選項。

## <a name="transact-overview"></a>交易總覽

使用交易發行選項時，Microsoft 可銷售協力廠商軟體，並將某些供應專案類型部署到客戶的 Azure 訂用帳戶。 當您選取計費模型和供應專案類型時，發行者必須考慮基礎結構費用的計費和您自己的軟體授權費用。

目前支援下列供應專案類型的交易發行選項：

- 虛擬機器
- Azure 應用程式
- SaaS 應用程式

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

針對**虛擬機器和 Azure 應用程式**，azure 基礎結構使用量費用會向客戶的 Azure 訂用帳戶收費。 基礎結構使用量費用會與客戶發票上軟體提供者的授權費用分開計費。

針對**SaaS 應用程式**，發行者必須將 Azure 基礎結構使用費用和軟體授權費用視為單一成本專案。  其以一般費用表示給客戶。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

視使用的交易選項而定，軟體授權費用如下所示：

- **免費**–軟體授權免費。
- 自備**授權**（BYOL）–軟體授權的任何適用費用都會直接在發行者與客戶之間進行管理。 Microsoft 僅處理 Azure 基礎結構費用。 這僅適用于虛擬機器和 Azure 應用程式。
- **隨用隨付**–軟體授權費用會根據所使用的 Azure 基礎結構，以每小時、每一核心（vCPU）定價費率呈現。 這僅適用于虛擬機器和 Azure 應用程式。
- **訂**用帳戶定價–軟體授權費用以每月或每年為單位，以固定費率或每一基座計費。 這僅適用于 SaaS 應用程式和 Azure 應用程式管理的應用程式。
- **免費軟體試用**–30或90天的軟體授權免費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

在發行隨用隨付或月租方案交易供應項目的情況下，Microsoft 會提供處理軟體授權購買、退貨及退款的技術和服務。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 可讓客戶訂購、授權及使用您的軟體，受限於 Microsoft 商業 marketplace 和您的使用者授權合約的條款及條件。 建立供應專案時，您必須提供您自己的使用者授權合約，或選取[標準合約](./standard-contract.md)。

### <a name="free-software-trials"></a>免費軟體試用

在交易發佈案例中，您可以讓軟體授權免費使用30或90天。 這項折扣功能不包括使用合作夥伴解決方案所驅動的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用供應專案類型和計費模式來銷售供應專案以外，您還可以交易私用供應專案，並透過談判、交易特定定價或自訂設定來完成。 所有三種交易發行選項都支援私用供應專案。

此選項允許的價格高於或低於公開提供的供應專案。 私用供應專案可用於折扣或將 premium 新增至供應專案。 私用供應專案可供一或多個客戶使用，方法是以白名單列出其 Azure 訂用帳戶。

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

**自備授權（BYOL）**

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

此選項必須設定為透過 Microsoft 銷售，而且可以依個別費率或每位使用者的每月或每年定價收費。 如果您啟用 SaaS 供應專案的 [**透過 Microsoft 銷售**] 選項，您會有下列成本結構：

| 授權成本       | 每月 $100.00  |
|--------------|---------|
| Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
| *由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 （發行者必須考慮授權費用中任何產生或通過的基礎結構成本）*  |
||

在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。

已符合 Marketplace 服務費用的合作夥伴，將會在2019年 6 2020 月30日前的 SaaS 供應專案上看到較少的交易費用。

在此案例中，Microsoft 會為您的軟體授權帳單 $100.00，並向發行者收取 $90.00：

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \*Microsoft 為您提供任何合格 SaaS 應用程式的90% 授權成本   |   每月 $80.00 <br> \*每月 $90.00    |
|Microsoft 保留授權成本的 20% <br> \*Microsoft 會為任何合格的 SaaS 應用程式保留10% 的授權成本。  |  每月 $20.00 <br> \*$10.00     |

對於您在我們的商業 Marketplace 上發佈的特定 SaaS 產品，Microsoft 會將其**Marketplace 服務費用**從20% （如 Microsoft 發行者合約中所述）降到10%。 為了讓您的供應專案符合資格，至少必須將您的其中一個供應專案指定為「IP 共同銷售準備就緒」或「IP 共同銷售優先」。 若要獲得此月份的縮減 Marketplace 服務費用，必須至少在上一個日曆月份結束前五個工作天符合資格。 降低的 Marketplace 服務費用不適用於 Vm、受管理的應用程式，或透過商業 Marketplace 提供的任何其他產品。 這項優惠費用將提供給合格的供應專案，而 Microsoft 在2019年5月1日到2020日之間收集了授權費用。 過了這段時間之後，費用就會回復到其正常金額。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**發票和付款**–您可以使用客戶慣用的發票方式來傳遞訂用帳戶或 PAYGO 軟體授權費用。

**Enterprise 合約**–如果客戶慣用的發票方法是 Microsoft Enterprise 合約，則您的軟體授權費用將會使用此發票支付方式計費，以與任何 Azure 特定的使用量成本分開。

**信用卡和每月發票**–客戶也可以使用信用卡和每月發票來支付費用。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

**免費點數和承諾**用量-某些客戶選擇預付 azure 承諾用量 Enterprise 合約或已提供免費信用額度以與 azure 搭配使用。 雖然可以使用這些點數來支付 Azure 使用量，但不能用它們支付發行者軟體授權費用。

**計費和集合**-發行者軟體授權計費會使用客戶選取的發票方法來顯示，並遵循發票上的時程表。 系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

在選取月租方案或隨用隨付定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

客戶通常會使用 Enterprise 合約或以信用卡付款的隨用隨付合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE]
>您可以透過合作夥伴中心的分析區段，取得交易發行選項的所有報告和深入解析。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱[發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)（可在合作夥伴中心取得）。

如需帳單問題的說明，請聯絡[商業 marketplace 發行者支援](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應專案類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

- 無論供應專案的定價模式為何，交易發行選項都需要 Microsoft 帳戶和財務資訊。
- 必要的財務資訊包括付款帳戶和稅務設定檔。

如需設定這些帳戶的詳細資訊，請參閱[在合作夥伴中心管理您的商業 marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)）。

### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型：

- **虛擬機器**–從免費、自備授權或隨用隨付定價模型中選取，並以供應專案層級所定義的 sku 形式呈現。 在客戶的 Azure 帳單上，Microsoft 會分別提供發行者軟體授權費用與基礎 Azure 基礎結構費用。 Azure 基礎結構費用是由使用發行者軟體而產生。
- **Azure 應用程式：解決方案範本或受管理的應用程式**–必須布建一或多部虛擬機器，並提取虛擬機器定價的總和。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在某些情況下，Azure 基礎結構使用量費用會與軟體授權費用分開傳遞給客戶，但在相同的帳單聲明上。 不過，如果您為 ISV 基礎結構費用設定受控應用程式供應專案，Azure 資源會向發行者收費，而客戶會收到包含基礎結構、軟體授權和管理服務成本的固定費用。

## <a name="next-steps"></a>後續步驟

- 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
- 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
