---
title: 商業市場計費 |Azure 應用商店
description: 本文介紹商業市場與商業相關的主題。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279737"
---
# <a name="commercial-marketplace-billing"></a>商業市場計費

本文介紹商業市場與商業相關的主題：

- [Marketplace 發行選項](#marketplace-publishing-options)
- [交易一般概觀](#transact-general-overview)
- [交易計費模型](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Marketplace 發行選項

商業市場為出版商提供了多種出版選擇。

### <a name="list-and-trial-publishing-options"></a>清單和試用發佈選項

發佈商可以利用清單、試用和自帶許可證 （BYOL） 發佈選項進行促銷和使用者獲取。 使用這些選項，Microsoft 不會直接參與發行者的軟體許可證交易，並且沒有關聯的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。

### <a name="transact-publishing-option"></a>交易發行選項

除了清單和試用發佈選項外，發行商還可以使用交易發佈選項。 此選項利用了 Microsoft 的全球可用商務功能，並允許 Microsoft 代表發行者託管雲市場事務。

## <a name="transact-general-overview"></a>交易一般概觀

使用交易發佈選項時，Microsoft 允許銷售協力廠商軟體，並將某些產品/服務類型部署到客戶的 Azure 訂閱。 在選擇計費模型和產品/服務類型時，發行者必須考慮基礎結構費用的計費以及發行者自己的軟體許可費用。

當前支援以下產品類型（虛擬機器、Azure 應用程式和 SaaS 應用程式）的 Transact 發佈選項。

![在 Azure 應用商店中交易](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

#### <a name="for-virtual-machines-and-azure-applications"></a>虛擬機器和 Azure 應用程式

對於虛擬機器和 Azure 應用程式，Azure 基礎結構使用費將計費到客戶的 Azure 訂閱。 基礎設施使用費在客戶發票上與軟體供應商的許可費分開定價和列報。

#### <a name="for-saas-apps"></a>SaaS 應用程式

對於 SaaS 應用程式，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。 它表示為客戶收取固定費用。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

根據所使用的交易選項，發行者的軟體許可費用可以顯示如下：

- *免費*：軟體許可證免費。
- *自帶許可證 （BYOL）*： 軟體許可證的任何適用費用都直接由發佈商和客戶管理。 Microsoft 僅處理 Azure 基礎結構費用。 (僅限虛擬機器和 Azure 應用程式。)
- 即*用即付*：軟體許可證費用基於所使用的 Azure 基礎結構作為每小時每核 （vCPU） 定價費率顯示。 此模型僅適用于虛擬機器和 Azure 應用程式。
- *訂閱定價*：軟體許可費以每月或每年定期費用的形式提出，按統一費率或按席位計費。 此模型僅適用于 SaaS 應用和 Azure 應用程式 - 託管應用。
- *免費軟體試用*：軟體許可證30天或90天不收取任何費用。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

發佈即用即付或訂閱交易產品/服務時，Microsoft 提供處理軟體許可證購買、退貨和退款的技術和服務。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 使客戶能夠訂購、許可和使用發佈商軟體，但須遵守 Microsoft 商業應用商店和發佈商使用者授權合約的條款及條件。 發佈商必須在創建產品/服務時提供其使用者授權合約或選擇[標準合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。

### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，發行者可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用產品/服務類型和計費模型將產品/服務貨幣化外，發佈商還可以處理私人產品/服務，並完成協商和交易特定定價或自訂配置。 所有 3 個交易發行選項都支援私人供應項目。

此選項允許比公開產品更高或更低的定價。 私人優惠可用於折扣或為優惠添加溢價。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。

### <a name="examples"></a>範例

#### <a name="pay-as-you-go"></a>隨用隨付

* 如果您啟用 [隨用隨付] 選項，就會有下列成本結構。

|授權成本  | 每小時美金 $1.00 元  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $1.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $1.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $1.14 元  |
|---------|---------|
|Microsoft 向您支付授權成本的 80%|   每小時美金 $0.80 元     |
|Microsoft 保留授權成本的 20%  |  每小時美金 $0.20 元       |
|Microsoft 保留 Azure 使用量成本的 100% | 每小時美金 $0.14 元 |

### <a name="bring-your-own-license-byol"></a>自帶許可證 （BYOL）

* 如果您啟用 [BYOL] 選項，就會有下列成本結構。

|授權成本  | 由您協商和收取的授權費用  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $0.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $0.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $0.14 元  |
|---------|---------|
|Microsoft 保留 Azure 使用量成本    |   每小時美金 $0.14 元     |
|Microsoft 保留授權成本的 0%   |  每小時美金 $0.00 元       |

### <a name="saas-app-subscription"></a>SaaS 應用程式訂閱

此選項必須配置為通過 Microsoft 銷售，並且可以按固定費率定價，也可以按月或按年按每個使用者定價。

• 如果您為 SaaS 產品/服務啟用"通過 Microsoft 銷售"選項，則具有以下成本結構。

|授權成本       | 每月 $100.00  |
|--------------|---------|
|Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
|*由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (注意：發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |

* 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
* 符合減少市場服務費條件的合作夥伴將在 2019 年 5 月至 2020 年 6 月期間降低 SaaS 優惠的交易費用。 在這種情況下，Microsoft 會為您的軟體許可證支付 100.00 美元，然後向發行者支付 90.00 美元。

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \*Microsoft 為任何合格的 SaaS 應用支付 90% 的許可證成本   |   每月 $80.00 <br> \*$90.00 每月    |
|Microsoft 保留授權成本的 20% <br> \*對於任何合格的 SaaS 應用，Microsoft 保留 10% 的許可證成本。  |  每月 $20.00 <br> \*$100.00     |

**降低市場服務費：** 對於您在我們的商業市場上發佈的某些 SaaS 產品，Microsoft 將降低其市場服務費從 20%（如 Microsoft 發佈商協定中所述）降至 10%。  為了使您的產品符合資格，Microsoft 必須至少將其中一種產品指定為 IP 共同銷售就緒或 IP 共同銷售優先順序。 要獲得該月此降低的市場服務費，必須在上一個日曆月結束前至少五 （5） 個工作日滿足資格。 降低的市場服務費不適用於 VM、託管應用或通過我們的商業市場提供的任何其他產品。  此降低的市場服務費將提供給合格的產品/服務，Microsoft 將在 2019 年 5 月 1 日至 2020 年 6 月 30 日期間收取許可費。  之後，市場服務費將恢復到正常金額。
