---
title: 下載 Azure 帳單發票和每日使用量資料
description: 說明如何下載或檢視您的 Azure 帳單發票和每日使用量資料。
keywords: 帳單發票,發票下載,azure 發票,azure 使用量
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 629ab8a0f2af789501e6365c94f4ce7f3fd3649c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693189"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>下載或檢視您的 Azure 帳單發票和每日使用量資料

針對大多數的訂用帳戶，您可以從 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下載您的發票，或透過電子郵件傳送它。 如果您是 Azure Enterprise 合約客戶 (EA 客戶)，您無法下載您的組織發票。 發票會傳送給已設定接收註冊發票的人員。

如果您是 EA 客戶或擁有 [Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)，您可以在 [Azure 入口網站](https://portal.azure.com/)中下載使用量。

只有某些角色有權取得帳單發票和使用量資訊 (例如帳戶管理員或企業系統管理員)。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](manage-billing-access.md)。

如果您有 Microsoft 客戶合約，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能檢視計費和使用量資訊。 若要深入了解 Microsoft 客戶合約的計費角色，請參閱[帳單設定檔角色和工作](understand-mca-roles.md#billing-profile-roles-and-tasks)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>下載您的 Azure 發票 (.pdf)

針對大多數的訂用帳戶，您可以從 Azure 入口網站下載您的發票。 如果您有 Microsoft 客戶合約，請參閱「下載帳單設定檔的發票」。

### <a name="download-invoices-for-an-individual-subscription"></a>下載個別訂用帳戶的發票

1. 從 Azure 入口網站的 [[訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)選取您的訂用帳戶作為[具有發票存取權的使用者](manage-billing-access.md)。

2. 選取 [發票]  。

    ![顯示 [帳單和使用量] 選項的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 按一下 [下載] 按鈕以下載 PDF 發票的複本，然後選取 [下載發票]  。 如果它顯示**無法使用**，請參閱[為什麼我沒有看到上期的發票？](#noinvoice)

    ![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. 您也可以按一下 [下載 csv]  ，下載已耗用數量的每日明細和預估費用。

    ![顯示 [下載發票和使用 量] 的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

如需發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](../understand/review-individual-bill.md)。 如需管理成本的說明，請參閱[使用 Azure 計費與成本管理避免非預期的成本](getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>下載 Microsoft 客戶合約的發票

系統會針對 Microsoft 客戶合約中的每個[帳單設定檔](../understand/mca-overview.md#billing-profiles)產生發票。 您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能從 Azure 入口網站下載發票。

1. 搜尋 [成本管理 + 帳單]  。
2. 選取帳單設定檔。
3. 選取 [發票]  。
4. 在發票方格中，尋找您要下載之發票的資料列。
5. 按一下資料列結尾的 [下載] 按鈕。
6. 在下載操作功能表中，選取 [發票]  。

如果您沒有看到上一個計費期間的發票，請參閱**其他資訊**。 <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a>為什麼我沒有看到上期的發票？

您沒有看到發票的可能原因如下︰

- 您的 Azure 訂閱不滿 30 天。

- 尚未產生發票。 等到計費期間結束。

- 您沒有檢視發票的權限。 如果您有 Microsoft 客戶合約，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員。 對於其他訂用帳戶，如果您不是帳戶管理員，則可能看不到舊發票。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](manage-billing-access.md)。

- 如果您的訂用帳戶有免費試用或每月信用額度，但您未超過，除非您有 Microsoft 客戶合約，否則不會收到發票。

## <a name="get-your-invoice-in-email-pdf"></a>透過電子郵件取得發票 (.pdf)

您可以選擇加入並設定其他收件者來透過電子郵件接收您的 Azure 發票。 這項功能可能無法用於支援供應項目、Enterprise 合約或 Azure in Open 等特定訂用帳戶。 如果您有 Microsoft 客戶合約，請參閱「以電子郵件取得您的帳單設定檔發票」。

### <a name="get-your-subscriptions-invoices-in-email"></a>以電子郵件取得您訂用帳戶的發票

1. 在[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中，選取您的訂用帳戶。 針對您擁有的每個訂用帳戶選擇加入。 依序按一下 [發票]  和 [以電子郵件寄送我的發票]  。

    ![顯示選擇加入流程的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. 按一下 [選擇加入]  並接受條款。

    ![顯示加入流程步驟 2 的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. 接受合約後，您就可以設定其他收件者。 移除收件者時，不會再儲存電子郵件地址。 如果您改變心意，則必須予以重新新增。

    ![顯示加入流程步驟 3 的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

如果在依照這些步驟執行之後並未收到電子郵件，請在[設定檔的通訊喜好設定](https://account.windowsazure.com/profile)中確定您的電子郵件地址是正確的。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>選擇不要以電子郵件取得您訂用帳戶的發票

您可以遵循上述步驟，然後按一下 [選擇不要以電子郵件傳送發票]  ，選擇不要透過電子郵件取得發票。 此選項會移除任何已設定為透過電子郵件接收發票的電子郵件地址。 如果您選擇重新加入，則可重新設定收件者。

 ![顯示退出流程的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>以電子郵件取得 Microsoft 客戶合約的發票

如果您有 Microsoft 客戶合約，您可以選擇要以電子郵件取得您的發票。 所有帳單設定檔擁有者、參與者、讀者和發票管理員都會透過電子郵件取得發票。 讀者無法更新電子郵件發票喜好設定。

1. 搜尋 [成本管理 + 帳單]  。
1. 選取帳單設定檔。
1. 在 [設定]  下方，選取 [屬性]  。
1. 在 [電子郵件發票]  底下，選取 [更新電子郵件發票喜好設定]  。
1. 選取 [加入]  。
1. 按一下 [更新]  。

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>選擇不要以電子郵件取得您的帳單設定檔發票

您可以遵循上述步驟，然後按一下 [退出]  ，選擇不要透過電子郵件取得發票。所有擁有者、參與者、讀者和發票管理員也都會選擇不要透過電子郵件取得發票。 如果您是讀者，則無法變更電子郵件發票喜好設定。

## <a name="download-usage-in-azure-portal"></a>在 Azure 入口網站中下載使用量

 針對大部分的訂用帳戶，請遵循下列步驟來尋找您的每日使用量：

1. 從 Azure 入口網站的 [[訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)選取您的訂用帳戶作為[具有發票存取權的使用者](manage-billing-access.md)。

2. 選取 [發票]  。

    ![顯示 [帳單和使用量] 選項的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 針對您要查看的發票期間按一下 [下載] 按鈕。

4. 按一下 [下載 csv]  ，可下載已耗用數量的每日明細和預估費用。  準備 csv 檔案可能需要幾分鐘的時間。

### <a name="download-usage-for-ea-customers"></a>下載 EA 客戶的使用量

若要檢視及下載 EA 客戶的使用量資料，您必須是企業系統管理員、帳戶擁有者或部門系統管理員，並且已啟用檢視費用原則。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 選取 [使用量 + 費用]  。
1. 針對您想要下載的月份，選取 [下載]  。

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>下載 Microsoft 客戶合約的使用量

若要檢視及下載帳單設定檔的使用量資料，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員。

#### <a name="download-usage-for-billed-charges"></a>下載計費費用的使用量

1. 搜尋 [成本管理 + 帳單]  。
2. 選取帳單設定檔。
3. 選取 [發票]  。
4. 在發票方格中，尋找對應至所要下載使用量的發票資料列。
5. 按一下資料列結尾處的省略符號 (`...`)。
6. 在下載操作功能表中，選取 [Azure 使用量和費用]  。

#### <a name="download-usage-for-open-charges"></a>下載未結費用的使用量

您也可以下載目前計費週期的當月至今使用量，這表示費用尚未計費。

1. 搜尋 [成本管理 + 帳單]  。
2. 選取帳單設定檔。
3. 在 [概觀]  刀鋒視窗中，按一下 [下載 Azure 使用量和費用]  。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和費用，請參閱：

- [了解 Microsoft Azure 帳單](../understand/review-individual-bill.md)
- [了解 Azure 發票上的詞彙](../understand/understand-invoice.md)
- [了解您 Microsoft Azure 詳細使用量上的字詞](../understand/understand-usage.md)
- [檢視組織的 Azure 定價](ea-pricing.md)

如果您有 Microsoft 客戶合約，請參閱：

- [了解帳單設定檔發票上的費用](../understand/review-customer-agreement-bill.md)
- [了解帳單設定檔發票上的詞彙](../understand/mca-understand-your-invoice.md)
- [了解帳單設定檔的 Azure 使用量和費用檔案](../understand/mca-understand-your-usage.md)
- [檢視及下載帳單設定檔的稅務文件](../understand/mca-download-tax-document.md)
- [檢視組織的 Azure 定價](ea-pricing.md)
