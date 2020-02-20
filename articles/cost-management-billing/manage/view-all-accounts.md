---
title: 在 Azure 入口網站檢視計費帳戶 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視計費帳戶。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: banders
ms.openlocfilehash: 0117496642c17db97535eca74aad334f1517a665
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199461"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Azure 入口網站中的計費帳戶和範圍

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理您的發票、付款及追蹤成本。 您可擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人專案註冊 Azure。 但也可以透過貴組織的 Enterprise 合約或 Microsoft 客戶合約獲得存取權。 針對上述每個案例，您會有不同的計費帳戶。

Azure 入口網站支援下列計費帳戶類型：

- **Microsoft Online Services 方案**：當您透過 Azure 網站註冊 Azure 時，就會建立 Microsoft Online Services 方案的計費帳戶。 例如，當您註冊 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)、[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)，或以 [Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)身分註冊時。

- **Enterprise 合約**：當貴組織簽署 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 以使用 Azure 時，就會建立 Enterprise 合約的計費帳戶。

- **Microsoft 客戶合約**：當貴組織與 Microsoft 代表共同簽署 Microsoft 客戶合約時，就會建立 Microsoft 客戶合約的計費帳戶。 精選區域中某些透過 Azure 網站註冊[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)或 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)的客戶，可能也有 Microsoft 客戶合約的計費帳戶。 如需詳細資訊，請參閱[開始使用 Microsoft 客戶合約的計費帳戶](../understand/mca-overview.md)。

- **Microsoft 合作夥伴合約**：Microsoft 合作夥伴合約的計費帳戶是針對雲端解決方案提供者 (CSP) 合作夥伴所建立的，可供其在新的商務體驗中管理客戶。 合作夥伴必須至少有一個客戶具有 [Azure 方案](https://docs.microsoft.com/partner-center/purchase-azure-plan)，才能在 Azure 入口網站中管理其計費帳戶。 如需詳細資訊，請參閱[開始使用 Microsoft 合作夥伴合約的計費帳戶](../understand/mpa-overview.md)。

若要判斷您的計費帳戶類型，請參閱[檢查計費帳戶的類型](#check-the-type-of-your-account)。

## <a name="scopes-for-billing-accounts"></a>計費帳戶的範圍
範圍是計費帳戶內可供您用來檢視和管理計費的節點。 您可在此管理計費資料、付款、發票，以及進行一般帳戶管理。

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 方案

 ![顯示 MOSP 階層的螢幕擷取畫面](./media/view-all-accounts/mosp-hierarchy.png)

|影響範圍  |定義  |
|---------|---------|
|計費帳戶     | 代表客戶為了使用 Azure 所接受的合約。 此範圍中包含一或多個訂用帳戶。  |
|訂用帳戶     |  代表 Azure 資源的群組。 發票會在此範圍產生。 其他計費資訊 (例如，付款方式和使用地址) 會與此範圍相關聯。|

### <a name="enterprise-agreement"></a>Enterprise 合約

![顯示 EA 階層的螢幕擷取畫面](./media/view-all-accounts/ea-hierarchy.png)

|影響範圍  |定義  |
|---------|---------|
|計費帳戶    | 表示 Enterprise 合約註冊。 此範圍中包含一或多個部門和帳戶。 發票會在此範圍產生。 |
|department     |  選擇性的帳戶群組，用以將成本分割成邏輯群組並設定預算。     |
|帳戶     |  代表單一帳戶擁有者。 帳戶擁有者有權建立和管理向註冊計費的 Azure 訂用帳戶。 |

### <a name="microsoft-customer-agreement"></a>Microsoft 客戶合約

![顯示 MCA 階層的螢幕擷取畫面](./media/view-all-accounts/mca-hierarchy.png)

|影響範圍  |工作  |
|---------|---------|
|計費帳戶     |   代表客戶為了使用 Microsoft 產品和服務所接受的合約。 此範圍中包含一或多個帳單設定檔。 |
|帳單設定檔     |   代表發票和相關的計費資訊，例如付款方式和帳單地址。 此範圍中包含一或多個發票區段。 |
|發票區段     |   代表發票中的一組成本。 Azure 訂用帳戶和其他購買 (例如 Azure Marketplace 和應用程式來源產品) 會與此範圍相關聯。    |

### <a name="microsoft-partner-agreement"></a>Microsoft 合作夥伴合約

![顯示 MPA 階層的螢幕擷取畫面](./media/view-all-accounts/mpa-hierarchy.png)

|影響範圍  |工作  |
|---------|---------|
|計費帳戶     |   代表可在新的商務體驗中針對客戶管理其 Microsoft 產品和服務的合作夥伴合約。 此範圍中包含一或多個帳單設定檔和客戶。   |
|帳單設定檔     |   代表某種貨幣的發票。     |
|客戶    |   代表雲端解決方案提供者 (CSP) 合作夥伴的客戶。  Azure 訂用帳戶和其他購買 (例如 Azure Marketplace 和應用程式來源產品) 會與此範圍相關聯。  |
|Reseller    |   為客戶提供服務的轉銷商。 這是訂用帳戶的選擇性欄位，僅適用於 CSP 雙層式模型中的間接提供者。     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>在 Azure 入口網站中切換計費範圍

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. 在 [概觀] 頁面中，選取 [切換範圍]  。

   ![顯示計費範圍的螢幕擷取畫面](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > 如果您只有一個範圍的存取權，則不會看到 [切換範圍]。

4. 選取範圍以檢視詳細資料。

   ![顯示計費範圍的螢幕擷取畫面](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>檢查帳戶的類型
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解如何開始[分析成本](../costs/quick-acm-cost-analysis.md)。
