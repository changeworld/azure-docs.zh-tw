---
title: 檢閱您的 Azure Enterprise 合約帳單
description: 學習如何閱讀並了解 Azure Enterprise 合約的使用量和帳單。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 017f617bff14eb0c031c40240ca6c1ac62c9c941
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598216"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解您的 Azure Enterprise 合約帳單

當 Azure Enterprise 合約客戶超過組織的信用額度，或使用信用額度未涵蓋的服務時，他們就會收到發票。

貴組織的信用額度包含您的承諾用量金額。 承諾用量金額是貴組織針對 Azure 服務使用量所預付的金額。 您可以連絡您的 Microsoft 客戶經理或轉銷商，在 Enterprise 合約中增加承諾用量金額的資金。

本教學課程僅適用於具有 Azure Enterprise 合約的 Azure 客戶。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢閱已開立發票的費用
> * 檢閱服務的超額費用
> * 檢閱 Marketplace 發票

## <a name="prerequisites"></a>Prerequisites

若要檢閱並確認發票上的費用，您必須是企業系統管理員。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](../manage/understand-ea-roles.md)。 如果您不知道誰是貴組織的企業系統管理員，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="review-invoiced-charges-for-most-customers"></a>檢閱多數客戶的已開立發票費用

本節不適用於澳洲、日本或新加坡的 Azure 客戶。

若在計費週期內發生下列任何事件，您會收到 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 無論點數餘額多寡，都會收到下列服務的發票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已註冊的使用者
    - Openlogic
    - 遠端存取權限 XenApp Essentials 已註冊的使用者
    - Ubuntu Advantage
    - Visual Studio Enterprise (每月)
    - Visual Studio Enterprise (每年)
    - Visual Studio Professional (每月)
    - Visual Studio Professional (每年)
- **Marketplace 費用**：貴組織的點數不會支付 Azure Marketplace 購買項目和使用量。 因此，無論點數餘額多寡，您都會收到 Marketplace 費用的發票。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買。

您的發票會先顯示 Azure 使用量費用以及相關的成本，再來列出所有 Marketplace 費用。 如果您尚有點數餘額，則會套用至 Azure 使用量，而發票會顯示 Azure 使用量和 Marketplace 使用量，而不會有任何費用。

您可以比較在企業版入口網站的 [報表]   > [使用量摘要]  中顯示的合計總金額與您的 Azure 發票。 [使用量摘要]  中的金額不含稅金。

登入 [Azure EA 入口網站](https://ea.azure.com)。 然後，選取 [報告]  。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 中顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

[總使用量]  與 [Azure Marketplace]  的合計金額應符合發票上的 [應收金額總計]  。 若要取得關於費用的詳細資訊，請移至 [下載使用量]  。  

![顯示 [下載使用量] 索引標籤的螢幕擷取畫面](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>檢閱其他客戶的已開立發票費用

本節僅適用於澳洲、日本或新加坡的 Azure 客戶。

發生下列任何事件時，您會收到一或多張 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 系統已為您開立下列服務的發票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已註冊的使用者
    - Openlogic
    - 遠端存取權限 XenApp Essentials 已註冊的使用者
    - Ubuntu Advantage
    - Visual Studio Enterprise (每月)
    - Visual Studio Enterprise (每年)
    - Visual Studio Professional (每月)
    - Visual Studio Professional (每年)
- **Marketplace 費用**：貴組織的點數不會支付 Azure Marketplace 購買項目和使用量，這兩者會個別計費。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買。

如果您在計費期間因服務超額而產生費用，且同時有個別計費的費用，您將會收到一張發票。 其中包含這兩種類型的費用。 Marketplace 費用的發票一律會單獨開立。

## <a name="review-service-overage-charges-for-other-customers"></a>檢閱其他客戶的服務超額費用

本節內容僅適用於澳洲、日本或新加坡的 Azure 客戶。

在企業版入口網站中，比較您在 [報表]   > [使用量摘要]  中顯示的總使用量金額與服務超額發票。 服務超額發票包含超過組織信用額度的使用量，和/或信用額度未涵蓋的服務。 **使用量摘要**的金額不含稅。

登入 [Azure EA 入口網站](https://ea.azure.com)，然後選取 [報告]  。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 中顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

[總使用量]  金額應該符合您服務超額發票上的 [應收金額總計]  。 若要取得費用的詳細資訊，請前往 [下載使用量]   > [進階報表下載]  。 此報表不包含保留的稅金或費用，或是 Marketplace 費用。  

![在 [下載使用量] 索引標籤上顯示 [進階報表下載] 的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

下表列出發票上和企業版入口網站中 [使用量摘要]  上所顯示的詞彙和說明：

|發票詞彙|使用量摘要詞彙|描述|
|---|---|---|
|應收金額總計|總使用量|套用信用額度之前針對特定期間的稅前使用量費用總計。|
|已使用的承諾用量|已使用的承諾用量|在該特定期間內套用的信用額度。|
|銷售總額|總超額|超過您信用額度金額的使用量費用總計。 此金額不含稅。|
|稅額|不適用|適用於特定期間之銷售總額的稅金。|
|總金額|不適用|在套用信用額度並增加稅金之後發票的應付金額。|

### <a name="review-marketplace-invoice"></a>檢閱 Marketplace 發票

本節內容僅適用於澳洲、日本或新加坡的 Azure 客戶。

在企業入口網站的 [報表]   > [使用量摘要]  上，比較您的 Azure Marketplace 總計和 Marketplace 發票。 Marketplace 發票僅適用於 Azure Marketplace 購買和使用量。 **使用量摘要**的金額不含稅。

登入 [Enterprise 入口網站](https://ea.azure.com)，然後選取 [報告]  。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 上顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Marketplace** 總計應該符合 Marketplace 發票上的**銷售總額**。 若要取得依使用量計算的費用詳細資訊，請前往 [下載使用量]  。 在 [Marketplace 費用]  下方，選取 [下載]  。 Marketplace 價格包含發行者所決定的稅額。 客戶不會收到來自發行者的個別發票，以收集交易的稅額。

![在 Marketplace 費用下方顯示下載選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢閱已開立發票的費用
> * 檢閱服務的超額費用
> * 檢閱 Marketplace 發票

請繼續閱讀下一篇文章，以深入了解如何使用 Azure EA 入口網站。

> [!div class="nextstepaction"]
> [開始使用 Azure EA 入口網站](../manage/ea-portal-get-started.md)
