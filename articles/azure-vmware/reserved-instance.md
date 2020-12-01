---
title: 使用 Azure VMware 解決方案保留實例節省成本
description: 瞭解如何購買 Azure VMware 解決方案的保留實例。
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 80780e9718001893b6960220b5004c1c29b2fec5
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327392"
---
# <a name="save-costs-with-azure-vmware-solution"></a>使用 Azure VMware 解決方案節省成本

當您認可至 [Azure VMware 解決方案](introduction.md)的保留實例時，您可以節省成本。  保留折扣會自動套用至符合保留範圍和屬性的執行中 Azure VMware 解決方案主機。 購買的保留實例僅涵蓋您使用量的計算部分，並包含軟體授權成本。 

## <a name="purchase-restriction-considerations"></a>購買限制考慮

保留實例可供使用，但有一些例外狀況。

-   **Clouds** 雲端保留僅適用于 [[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)] 頁面所列的區域。

-   **配額不足** -訂用帳戶範圍為單一/共用訂用帳戶的保留範圍，必須在新的保留實例的訂用帳戶中有可用的主機配額。 您可以 [建立配額增加要求](enable-azure-vmware-solution.md) 以解決此問題。

-   **供應專案資格**-您將需要 [AZURE Enterprise 合約 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 的 Microsoft。

-   **容量限制** -在罕見的情況下，azure 會限制購買 Azure VMware 解決方案主機 sku 的新保留，因為區域中的低容量。

## <a name="buy-a-reservation"></a>購買保留項目

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中購買 Azure VMware 方案主機實例的保留實例。

您可以 [預先支付或使用每月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)來支付保留費用。

這些需求適用于購買保留的專用主機實例：

-   您必須是至少一個 EA 訂用帳戶的 *擁有* 者角色，或具有隨用隨付費率的訂用帳戶。

-   若為 EA 訂用帳戶，您必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [**新增保留實例**] 選項。 若已停用，您必須是訂用帳戶的 EA 系統管理員，才能啟用此訂用帳戶。

-   針對雲端解決方案提供者 (CSP) Azure 方案中的訂用帳戶，合作夥伴必須在客戶的 Azure 入口網站中購買保留實例。 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>購買 EA 訂用帳戶的保留實例

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [所有服務] > [保留]。

3. 選取 [ **立即購買** ]，然後選取 [ **Azure VMware 解決方案**]。

4. 輸入必要欄位。 符合執行中 Azure VMware 方案主機的選定屬性符合保留折扣的資格。  屬性包括 SKU、區域 (如果適用) 和範圍。 保留範圍會選取保留節省成本的適用之處。

   如果您有 EA 合約，可以使用 [ **新增更多] 選項** 來快速新增實例。 此選項不適用於其他訂用帳戶類型。

   | 欄位        |  描述 |
   | ------------ | ------------ |
   | 訂用帳戶 | 用來支付保留項目的訂用帳戶。 保留項目的費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 enterprise 合約 (供應專案號碼： MS-MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 、Microsoft 客戶合約，或是採用隨用隨付費率的個別訂用帳戶 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 費用會從承諾用量金額餘額 (如果有的話) 扣除或作為超額部分收費。 針對具有隨用隨付費率的訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。 |
   | 影響範圍        | 保留範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍) 。 如果您選取：<br><ul><li><b>單一資源群組範圍</b> -只會將保留折扣套用至所選資源群組中的相符資源。</li><li><b>單一訂</b> 用帳戶範圍-將保留折扣套用至所選訂用帳戶中的相符資源。</li><li><b>共用範圍</b> -將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 若是 EA 客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。</li></ul>       |
   | 區域       | 保留所涵蓋的 Azure 區域。   |
   | 主機大小    | AV36    |
   | 詞彙         | 一年或三年。  |
   | 數量     | 要在保留內購買的實例數目。 Quantity 是可取得帳單折扣的執行中 Azure VMware 解決方案主機數目。    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>購買 CSP 訂用帳戶的保留實例

想要為客戶購買保留實例的 Csp，必須從 [合作夥伴中心檔](/partner-center/azure-plan-manage)中代表 (AOBO) 程式使用系統 **管理員**。 如需詳細資訊，請參閱系統 [管理員代表 (AOBO) ](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 影片。

1. 登入[合作夥伴中心](https://partner.microsoft.com)。

2. 選取 [ **CSP** ] 以存取 [ **客戶** ] 區域。

3. 展開 [客戶詳細資料]，然後選取 [ **Microsoft Azure 管理入口網站**]。 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft 合作夥伴中心用戶端區域" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. 在 [Azure 入口網站中，選取 [**所有服務**  >  **保留**]。

5. 選取 [ **立即購買** ]，然後選取 [ **Azure VMware 解決方案**]。

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure 入口網站保留" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. 輸入必要欄位。 符合執行中 Azure VMware 方案主機的選定屬性符合保留折扣的資格。  屬性包括 SKU、區域 (如果適用) 和範圍。 保留範圍會選取保留節省成本的適用之處。

   | 欄位        |  描述 |
   | ------------ | ------------ |
   | 訂用帳戶 | 用來支付保留項目的訂用帳戶。 保留項目的費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是合格的，在此案例中為 CSP 訂用帳戶|
   | 影響範圍        | 保留範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍) 。 如果您選取：<br><ul><li><b>單一資源群組範圍</b> -只會將保留折扣套用至所選資源群組中的相符資源。</li><li><b>單一訂</b> 用帳戶範圍-將保留折扣套用至所選訂用帳戶中的相符資源。</li><li><b>共用範圍</b> -將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 若是 EA 客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。</li></ul>       |
   | 區域       | 保留所涵蓋的 Azure 區域。   |
   | 主機大小    | AV36    |
   | 詞彙         | 一年或三年。  |
   | 數量     | 要在保留內購買的實例數目。 Quantity 是可取得帳單折扣的執行中 Azure VMware 解決方案主機數目。     |

若要深入瞭解如何查看為您的客戶購買的保留，請參閱 [雲端解決方案提供者 (CSP 查看 Azure 保留) 一 ](../cost-management-billing/reservations/how-to-view-csp-reservations.md) 文。

## <a name="usage-data-and-reservation-usage"></a>使用量資料和保留使用量

取得保留折扣的使用量具有有效的價格（零）。 您可以查看哪個 Azure VMware 解決方案實例收到每個保留的保留折扣。

如需保留折扣如何出現在使用量資料中的詳細資訊：

- 若是 EA 客戶，請參閱 [瞭解您企業註冊的 Azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- 針對個別訂用帳戶，請參閱 [瞭解隨用隨付訂用帳戶的 Azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>在購買後變更保留

您可以在購買後對保留進行這些變更：

-   更新保留範圍

-   實例大小彈性 (（如果適用）) 

-   擁有權

您也可以將保留分割成較小的區塊或合併保留。 這些變更都不會造成新的商業交易或變更保留的結束日期。

如需 CSP 管理之保留的詳細資訊，請參閱 [使用合作夥伴中心、Azure 入口網站或 Api 銷售 Microsoft Azure 保留給客戶](/partner-center/azure-reservations)。



>[!NOTE]
>購買保留之後，您就無法直接進行這些類型的變更：
>
> - 現有的保留區域
> - SKU
> - 數量
> - 持續時間
>
>但是，如果您想要進行變更，您可以 *交換* 保留。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

Csp 可以取消、交換或退款保留，以及為其客戶購買的某些限制。 如需詳細資訊，請參閱 [管理、取消、交換或退款 Microsoft Azure 保留給客戶](/partner-center/azure-reservations-manage)。