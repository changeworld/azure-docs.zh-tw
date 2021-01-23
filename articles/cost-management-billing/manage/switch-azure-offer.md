---
title: 變更 Azure 訂用帳戶供應項目
description: 瞭解如何變更您的 Azure 訂用帳戶，並切換至不同的供應專案。
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: banders
ms.openlocfilehash: dd8040effc5972d86e620793e437f5b185e12603
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685443"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>將 Azure 訂用帳戶變更為其他供應項目

當客戶使用隨用隨 [付訂](https://azure.microsoft.com/offers/ms-azr-0003p/) 用帳戶時，您可以將 Azure 訂用帳戶切換至 Azure 入口網站中的另一個供應專案。 例如，您可以使用這項功能，利用 [Visual Studio 訂閱者的每月信用額度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

**只想要從免費試用版升級？** 請參閱[升級您的訂用帳戶](upgrade-azure-subscription.md)。

## <a name="whats-supported"></a>支援的項目：

您可以從隨用隨付訂用帳戶切換為：

- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> 如需其他供應項目變更，請[連絡支援中心](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="switch-subscription-offer"></a>切換訂用帳戶供應項目

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 [訂用帳戶] **，然後選取** 您的隨用隨付訂用帳戶。
1. 在頁面頂端，選取 [ **切換供應** 專案]。 只有當您有隨用隨付訂用帳戶，且已完成第一個計費週期時，才能使用此選項。  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="ALTImage 顯示使用交換器供應專案的訂用帳戶詳細資料 optionTEXT" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. 從您的訂用帳戶可切換的供應專案清單中選取您想要的供應專案。 這份清單會根據您帳戶相關聯的會員資格而有所不同。 如果沒有清單，請檢查[您可切換的可用供應項目清單](#whats-supported)，並確定您有正確的會員資格。 然後選取 [下一步]。
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="選取您要切換的目標供應項目" lightbox="./media/switch-azure-offer/select-offer.png" :::
    根據您所切換的供應項目，您可能會看到有關切換所造成之影響的附註。 在您繼續進行之前，請先仔細瀏覽這份清單並依照指示操作。 您可能也需要驗證您的電話號碼。
1. 查看任何附注或驗證您的電話號碼之後，請選取 [ **切換供應** 專案]。
1. 您的訂用帳戶現在已經切換到新的供應項目。

## <a name="frequently-asked-questions"></a>常見問題集
下列各節可回答常見問題。

### <a name="what-is-an-azure-offer"></a>什麼是 Azure 供應項目？

Azure 供應項目是您擁有之 Azure 訂用帳戶的「類型」。 例如，[採用隨用隨付費率的訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) 和 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) 皆為 Azure 供應項目。 每個供應項目有不同的[條款](https://azure.microsoft.com/support/legal/offer-details/)，有些則有特殊優點。 訂用帳戶的供應專案會顯示在 [訂閱詳細資料] 頁面上。

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="顯示供應專案類型的訂用帳戶詳細資料頁面" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-button"></a>為什麼看不到該按鈕？

如果有下列情況，您可能看不到 [ **切換供應** 專案] 選項：

* 您沒有[採用隨用隨付費率的訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。 目前只有採用隨用隨付費率的訂用帳戶可以轉換成其他供應項目。
  * 如果您有[免費試用](https://azure.microsoft.com/free/)，請了解如何[升級至隨用隨付](upgrade-azure-subscription.md)。
  * 若要從不同的訂用帳戶切換供應項目，[請聯絡支援中心](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* 您仍在第一個計費期間；必須等到第一個計費期間完成後，才能切換供應項目。

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>為什麼我會看到「目前您的國家/地區沒有提供此供應項目」錯誤訊息？

* 您可能不符合任何改用供應項目的資格。 請查看[您可以切換的可用供應項目清單](#whats-supported)，並確定您已透過 Visual Studio 或 Bizspark 啟動適當的權益。
* 部分供應項目可能無法在所有國家/地區中使用。

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>切換 Azure 供應項目對我的服務和帳單有什麼好處？

以下是切換 Azure 供應專案時所發生狀況的詳細資料。

#### <a name="no-service-downtime"></a>沒有服務停機時間

與訂用帳戶相關聯的任何使用者均可如常使用服務。 不過，您所切換的供應項目可能會有所限制。 例如，部分供應項目禁止生產使用，因此您需要將生產資源移至其他訂用帳戶。

#### <a name="quota-increases-are-reset"></a>會重設配額增加

當您切換供應項目時，會重設任何[超過預設限制的限制或配額增加](../../azure-portal/supportability/resource-manager-core-quotas-request.md)。 沒有任何服務停機時間，即使您的資源超過預設的限制亦然。 例如，您在訂用帳戶上使用 200 個核心，然後切換供應項目會將核心配額重設回預設的 20 個核心。 使用 200 個核心的 VM 不會受到影響，而且會繼續執行。 不過，如果您不提出另一個配額增加的要求，就無法佈建更多的核心。

#### <a name="billing"></a>計費

在您切換當天，系統會為所有未付費用產生發票。 接下來，您的訂用帳戶將以新的供應項目價格條款計費。 您的訂用帳戶計費週年會變更為您變更供應項目的日期。 供應項目變更之前的使用量與計費資料將不會保留，因此我們建議您在切換之前先行下載這些資料。

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>我可以從採用隨用隨付費率的訂用帳戶遷移至雲端解決方案提供者 (CSP) 或 Enterprise 合約 (EA) 嗎？

* 要遷移至 CSP，請參閱[在訂閱者與 CSP 之間轉移 Azure 訂用帳戶](transfer-subscriptions-subscribers-csp.md)。
* 若要移轉至 EA，可請您的註冊系統管理員將您的帳戶新增至 EA。 依照邀請電子郵件中的指示，將您的訂用帳戶移至 EA 註冊下。

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>是否可以將資料與服務移轉至新的訂用帳戶？

* 您可以直接將資源移轉至新的訂用帳戶，請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。
* 若要將 Azure 訂用帳戶的擁有權和其中的所有內容轉送給其他人，請參閱[轉送 Azure 訂用帳戶的擁有權](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [開始分析成本](../costs/quick-acm-cost-analysis.md)
