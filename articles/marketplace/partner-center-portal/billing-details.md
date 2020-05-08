---
title: Azure Marketplace 中的商用 marketplace 計費
description: 瞭解 marketplace 發行選項和商業 marketplace 的交易計費模型。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: deb4f3f1bb17bff0b09b2e4f79ceb967d4d7ff59
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744962"
---
# <a name="commercial-marketplace-billing-with-transact-billing-models"></a>使用交易計費模型的商用 marketplace 計費

本文涵蓋商業 marketplace 的商務相關主題：

- [Marketplace 發行選項](#marketplace-publishing-options)
- [交易一般概觀](#transact-general-overview)
- [交易計費模型](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Marketplace 發行選項

商用 marketplace 為發行者提供了數種發行選項。

### <a name="list-and-trial-publishing-options"></a>清單和試用版發行選項

發行者可以利用 [清單]、[試用] 和 [自備授權（BYOL）] 發行選項來進行促銷和使用者取得。 使用這些選項時，Microsoft 不會直接參與發行者的軟體授權交易，也不會有相關聯的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。

### <a name="transact-publishing-option"></a>交易發行選項

除了清單和試用版發行選項之外，發行者也提供交易發行選項。 此選項會利用 Microsoft 全球可用的商務功能，並允許 Microsoft 代表發行者裝載雲端 marketplace 交易。

## <a name="transact-general-overview"></a>交易一般概觀

使用交易發行選項時，Microsoft 會啟用協力廠商軟體的銷售，並將某些供應專案類型部署到客戶的 Azure 訂用帳戶。 選取計費模式和供應專案類型時，發行者必須考慮基礎結構費用的計費，以及發行者的軟體授權費用。

目前支援下列供應專案類型的交易發行選項：虛擬機器、Azure 應用程式和 SaaS 應用程式。

![Azure Marketplace 中的交易](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

#### <a name="for-virtual-machines-and-azure-applications"></a>適用于虛擬機器和 Azure 應用程式

針對虛擬機器和 Azure 應用程式，Azure 基礎結構使用量費用會向客戶的 Azure 訂用帳戶收費。 基礎結構使用量費用會與客戶發票上軟體提供者的授權費用分開計費。

#### <a name="for-saas-apps"></a>SaaS 應用程式

對於 SaaS 應用程式，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。 其以一般費用表示給客戶。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

視使用的交易選項而定，發行者的軟體授權費用可能如下所示：

- *免費*：軟體授權不收費。
- 自備*授權（BYOL）*：在發行者與客戶之間直接管理軟體授權適用的任何費用。 Microsoft 僅處理 Azure 基礎結構費用。 (僅限虛擬機器和 Azure 應用程式。)
- *隨用隨付*：軟體授權費用會根據所使用的 Azure 基礎結構，以每小時、每個核心（vCPU）定價費率呈現。 此模型僅適用于虛擬機器和 Azure 應用程式。
- *訂*用帳戶定價：軟體授權費用會以每月或每年為單位，以固定費率或每個基座計費的週期性費用呈現。 此模型僅適用于 SaaS 應用程式和 Azure 應用程式管理的應用程式。
- *免費軟體試用*：30天或90天的軟體授權免費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

當發佈預付型方案或訂用帳戶交易供應專案時，Microsoft 會提供技術和服務來處理軟體授權購買、退貨及收費。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 可讓客戶訂購、授權及使用發行者軟體，叢集 Microsoft 的商業 Marketplace 和發行者的使用者授權合約的條款及條件。 發行者必須提供其使用者授權合約，或在建立供應專案時選取[標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。

### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，發行者可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用供應專案類型和計費模式來銷售供應專案之外，發行者還可以交易私人供應專案，並以談判和交易特定的定價完成，或自訂設定。 所有 3 個交易發行選項都支援私人供應項目。

此選項允許的價格高於或低於公開提供的供應專案。 私用供應專案可用於折扣或新增供應專案的 premium。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。

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

### <a name="bring-your-own-license-byol"></a>自備授權（BYOL）

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

### <a name="saas-app-subscription"></a>SaaS 應用程式訂用帳戶

此選項必須設定為透過 Microsoft 銷售，而且可以依個別費率或每位使用者的每月或每年定價收費。

•如果您啟用 SaaS 供應專案的 [透過 Microsoft 銷售] 選項，則會有下列成本結構。

|授權成本       | 每月 $100.00  |
|--------------|---------|
|Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
|*由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (注意：發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |

* 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
* 已符合 Marketplace 服務費用的合作夥伴，將會在2019年 6 2020 月30日前的 SaaS 供應專案上看到較少的交易費用。 在此案例中，Microsoft 會為您的軟體授權帳單 $100.00，並向發行者收取 $90.00。

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \*Microsoft 為您提供任何合格 SaaS 應用程式的90% 授權成本   |   每月 $80.00 <br> \*每月 $90.00    |
|Microsoft 保留授權成本的 20% <br> \*Microsoft 會為任何合格的 SaaS 應用程式保留10% 的授權成本。  |  每月 $20.00 <br> \*$10.00     |

**減少 Marketplace 服務費用：** 對於您在我們的商業 Marketplace 上發佈的特定 SaaS 產品，Microsoft 會將其 Marketplace 服務費用從20% （如 Microsoft 發行者合約中所述）降到10%。  為了讓您的產品符合資格，至少必須將您的其中一項產品指定為「IP 共同銷售準備就緒」或「IP 共同銷售優先」。 若要獲得此月份的縮減 Marketplace 服務費用，必須至少符合上一個日曆月份結束前五（5）個工作天的資格。 降低 Marketplace 服務費用不適用於 Vm、受管理的應用程式或透過我們的商業 Marketplace 提供的任何其他產品。  這項優惠的 Marketplace 服務費用將提供給合格的供應專案，而 Microsoft 在2019年5月1日到2020日之間所收集的授權費用。  該時間過後，Marketplace 服務費用將會回到其正常金額。
