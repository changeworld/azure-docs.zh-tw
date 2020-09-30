---
title: 檢閱個別 Azure 帳單
description: 了解如何看懂您的帳單和資源使用量，以及確認個別 Azure 訂用帳戶的費用。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 0c93375abd57ff5e96906ef7befbbb546a3a1caf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270520"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>教學課程：檢閱個別 Azure 帳單

本文可協助您了解及檢閱您的 Azure 帳單。 通常，您會在電子郵件中收到每個計費週期的發票。 發票即代表您的 Azure 帳單。 Azure 入口網站中可以取得發票上的相同費用資訊。 在本教學課程中，您將比較包含詳細每日使用量檔案的發票與 Azure 入口網站中的成本分析。

本教學課程僅適用於具有個別訂用帳戶的 Azure 客戶。 一般個別訂用帳戶是指直接向 Azure 網站購買且採用隨用隨付費率的帳戶。

如果您需要協助以了解非預期的費用，請參閱[分析非預期的費用](analyze-unexpected-charges.md)。 或者，如果您需要取消 Azure 訂用帳戶，請參閱[取消您的 Azure 訂用帳戶](../manage/cancel-azure-subscription.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 比較發票費用與使用量檔案
> * 比較成本分析中的費用和使用量

## <a name="prerequisites"></a>必要條件

您必須擁有付費的「Microsoft Online Services 方案」** 計費帳戶。 當您透過 Azure 網站註冊 Azure 時所建立的帳戶。 例如，如果您有[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)或如果您是 [Visual Studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)。

[Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)的發票只會在您超過每月信用額度時建立。

您訂閱 Azure 的天數必須超過 30 天。 Azure 會在您的發票期間結束時向您收取費用。

## <a name="sign-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="compare-invoiced-charges-with-usage-file"></a>比較發票費用與使用量檔案

<a name="charges"></a>

比較使用量和成本的第一個步驟是下載發票和使用量檔案。 詳細使用量 CSV 檔案會顯示依計費期間和每日使用量的費用。 其不會包含任何稅務資訊。 您必須是帳戶管理員或具有擁有者角色，才能下載檔案。

在 Azure 入口網站的搜尋方塊中輸入「訂用帳戶」**，然後按一下[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

[![瀏覽至訂用帳戶](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

在訂用帳戶清單中按一下訂用帳戶。

在 [帳單]**** 底下，按一下 [發票]****。

在發票清單中，尋找您想要下載的發票，然後按一下下載符號。 您可能需要變更時間範圍來查看較舊的發票。 產生使用量詳細資料檔案和發票可能需要幾分鐘的時間。

![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/review-individual-bill/download-invoice.png)

在 [下載使用量 + 費用] 視窗中依序按一下 [下載 csv]**** 和 [下載發票]****。

![顯示 [下載發票和使用 量] 的螢幕擷取畫面](./media/review-individual-bill/usageandinvoice.png)

如果顯示**不適用**，則有幾個原因會導致您看不到使用量詳細資料或發票：

- 您的 Azure 訂閱不滿 30 天。
- 此計費週期沒有使用量。
- 尚未產生發票。 等到計費期間結束。
- 您沒有檢視發票的權限。 除非您是帳戶管理員，否則您可能看不到舊的發票。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](../manage/manage-billing-access.md)。
- 如果您的訂用帳戶有免費試用或每月信用額度，但您未超過，除非您有 Microsoft 客戶合約，否則不會收到發票。

接下來，您會檢閱費用。 您的發票會顯示稅金和使用費用的值。

![Azure 發票範例](./media/review-individual-bill/invoice-usage-charge.png)

開啟您所下載的 CSV 使用量檔案。 在檔案結尾，加總 [成本]** 資料行中所有項目的值。

![具有加總成本的使用量檔案範例](./media/review-individual-bill/usage-file-usage-charges.png)

 加總的「成本」** 值應該會精確地符合發票上的「使用費用」** 成本。

使用量費用會顯示在計量等級中。 下列字詞在發票和詳細使用量檔案代表相同意義。 例如，發票上的計費週期與詳細使用量檔案中所顯示的計費期間相同。

| 發票 (PDF) | 詳細使用量 (CSV)|
| --- | --- |
|計費週期 | BillingPeriodStartDate BillingPeriodEndDate |
|名稱 |計量類別 |
|類型 |計量子類別 |
|資源 |MeterName |
|區域 |MeterRegion |
|已耗用 | 數量 |
|已包括 |已包括的數量 |
|可計費 |超額數量 |
|費率 | EffectivePrice|
| 值 | 成本 |

您發票的 [使用費用]**** 區段會顯示您計費週期內所取用的每個計量總計值 (成本)。 例如，下列影像顯示 P10 Disks** 資源的 Azure 儲存體服務使用費用。

![發票使用量費用](./media/review-individual-bill/invoice-usage-charges.png)

在您的 CSV 使用量檔案中，根據發票上顯示的相對應資源篩選 MeterName**。 然後，加總資料行項目的「成本」** 值。 以下範例著重於與發票上的相同明細相對應的計量名稱 (P10 磁碟)。

![MeterName 的使用量檔案加總值](./media/review-individual-bill/usage-file-usage-charge-resource.png)

加總的「成本」** 值應該會精確地符合發票上對個別資源收取的「使用費用」** 成本。

如需詳細資訊，請參閱[了解您的 Azure 發票](understand-invoice.md)及[了解您的 Azure 詳細使用量](understand-usage.md)。

## <a name="compare-charges-and-usage-in-cost-analysis"></a>比較成本分析中的費用和使用量

Azure 入口網站中的成本分析也可協助您確認費用。 若要快速瀏覽已開立發票的使用量和費用，請從 Azure 入口網站的[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)頁面中選取您的訂用帳戶。 接下來，按一下 [成本分析]****，然後在檢視清單中按一下 [發票詳細資料]****。

![顯示發票詳細資料選取項目的範例](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

接下來，在日期範圍清單中，為您的發票選取一段時間。 加入發票編號的篩選，然後選取與發票上編號相符的 InvoiceNumber。 成本分析會針對已開立發票的項目顯示其成本詳細資料。

![在成本分析中針對已開立發票項目顯示成本詳細資料的範例](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

成本分析中顯示的成本值，應該會精確地符合發票上對個別資源收取的「使用費用」** 成本。

![發票使用量費用](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>外部服務會分開計費

外部服務 (或 Marketplace) 費用為協力廠商軟體廠商建立的資源費用。 您可以從 Azure Marketplace 取得並使用這些資源。 例如，Barracuda Firewall 是由協力廠商提供的 Azure Marketplace 資源。 適用於該防火牆和其相對應計量的所有費用都會顯示為外部服務費用。

外部服務費用會分開計費。 費用不會顯示在 Azure 發票上。 若要深入了解，請參閱[了解您的 Azure 外部服務費用](understand-azure-marketplace-charges.md)。

### <a name="resources-billed-by-usage-meters"></a>依使用量計量計費的資源

Azure 不會直接根據資源成本計費。 資源費用會使用一或多個計量來計算。 計量用於追蹤資源於存留期間內的使用量。 接著，這些計量會用來計算帳單金額。

當您建立單一 Azure 資源 (例如虛擬機器) 時，便會建立一或多個計量執行個體。 計量用於追蹤一段時間內的資源使用量。 每種計量都會發出使用量記錄，Azure 會使用這些記錄來計費。

例如，在 Azure 中建立的單一虛擬機器 (VM) 可能會建立下列計量來追蹤其使用量：

- 計算時數
- IP 位址時數
- 資料傳入
- 資料傳出
- 標準受控磁碟
- 標準受控磁碟作業
- 標準 IO 磁碟
- 標準 IO - 區塊 Blob 讀取
- 標準 IO - 區塊 Blob 寫入
- 標準 IO - 區塊 Blob 刪除

建立 VM 後，每個計量便會開始發出使用量記錄。 Azure 計量系統中會追蹤此使用量和計量的價格。

您可以在使用量 CSV 檔案中查看用來計算費用的計量，如先前範例所示。

## <a name="pay-your-bill"></a><a name="payment"></a>支付您的帳單

如果您設定信用卡作為付款方式，我們會在計費期間結束後的 10 天內自動扣款。 在您的信用卡對帳單上，明細項目的名稱會是 **MSFT Azure**。

若要變更用來支付費用的信用卡，請參閱[新增、更新或移除 Azure 的信用卡](../manage/change-credit-card.md)。

如果您透過[開立發票來付款](../manage/pay-by-invoice.md)，請將您的款項匯至發票底部所列的位置。

若要檢查付款狀態，請[建立支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 比較發票費用與使用量檔案
> * 比較成本分析中的費用和使用量

完成快速入門以開始使用成本分析。

> [!div class="nextstepaction"]
> [開始分析成本](../costs/quick-acm-cost-analysis.md)
