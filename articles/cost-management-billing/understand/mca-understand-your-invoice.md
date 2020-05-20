---
title: 了解 Azure 中的 Microsoft 客戶合約發票
description: 學習如何閱讀並了解 Azure 中的 Microsoft 客戶合約帳單
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 4c4b105bb4b67e855de27b6a57d2080fde068421
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79237899"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>您 Microsoft 客戶合約發票中的詞彙

本文適用於 Microsoft 客戶合約的 Azure 計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

發票會為您提供費用的摘要以及付款的指示。 您可以從 [Azure 入口網站](https://portal.azure.com/)下載可攜式文件格式 (.pdf) 的發票，或可透過電子郵件傳送。 如需詳細資訊，請參閱[檢視及下載您的 Microsoft Azure 發票](download-azure-invoice.md)。

## <a name="billing-period"></a>計費期間

您的發票會按月開立。 您可以在 [Azure 入口網站](https://portal.azure.com/)中查看帳單設定檔屬性下方的*發票日期*，以確認您會在一個月中的哪一天收到發票。 在計費期間結束和發票日期之間發生的費用，會包含在下個月的發票中，因為這些費用屬於下一個計費期間。 每張發票的計費期間開始和結束日期都會列在 [帳單摘要]  上方的發票 PDF 中。

## <a name="invoice-terms-and-descriptions"></a>發票詞彙和說明

以下幾節會列出您在發票上看到的重要詞彙，並提供每個詞彙的說明。

### <a name="invoice-summary"></a>發票摘要

[發票摘要]  位於第一頁的頂端，會顯示您的帳單設定檔和支付方式的相關資訊。

![發票摘要區段](./media/mca-understand-your-invoice/invoicesummary.png)

| 詞彙 | 描述 |
| --- | --- |
| 買方 |您的法律實體地址，可在計費帳戶屬性中找到|
| 帳單收件者 |接收發票之帳單設定檔的帳單地址，可在帳單設定檔屬性中找到|
| 帳單設定檔 |接收發票的帳單設定檔名稱 |
| 採購單 number |您選擇性指派以進行追蹤的訂購單號碼 |
| 發票號碼 |由 Microsoft 產生、供追蹤使用的唯一發票號碼 |
| 發票日期 |發票產生的日期，通常是計費週期結束後的 5 到 12 天。 您可以在帳單設定檔屬性中查看發票日期。|
| 付款條件 |您支付 Microsoft 帳單的方式。 *月結 30 天*表示您會在發票日期的 30 天內付費。 |

### <a name="billing-summary"></a>帳單摘要

[帳單摘要]  會根據帳單設定檔顯示自上一個計費期間起的費用、已套用的任何點數、稅金，以及應付金額總計。

![帳單摘要區段](./media/mca-understand-your-invoice/billingsummary.png)

| 詞彙 | 描述 |
| --- | --- |
| Charges|此帳單設定檔自上一個計費期間起的 Microsoft 費用總數 |
| 學分 |您因收益而獲得的點數 |
| 套用的 Azure 點數 | 在每個計費期間自動套用至 Azure 費用的 Azure 點數 |
| 小計 |稅前應付金額 |
| 稅金 |您依據帳單設定檔的國家/地區所支付的稅金類型和金額。 如果您不需要支付稅金，則不會在發票上看到稅額。 |
| 預估節費總計 |您藉由有效折扣省下的預估總金額。 如果適用的話，有效折扣率會列在 [依發票區段顯示詳細資料] 中的 [購買明細項目] 底下。 |

### <a name="invoice-sections"></a>發票區段

針對帳單設定檔下方的每個發票區段，您將會看到費用、已套用的 Azure 點數金額、稅金，以及應付金額總計。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>依發票區段顯示詳細資料

詳細資料會顯示依產品訂單細分的每個發票區段的成本。 在每個產品訂單中，成本會依服務類型細分。 您可以在 Azure 入口網站和 Azure 使用量和費用 CSV 中找到產品和服務的每日費用。 若要深入了解，請參閱[了解 Microsoft 客戶合約發票上的費用](review-customer-agreement-bill.md)。

每個服務系列的應付金額總計，可藉由將*點數/費用*減去 *Azure 點數*，再加上*稅金*計算得出：


![依發票區段顯示詳細資料](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| 詞彙 |描述 |
| --- | --- |
| 單位價格 | 服務的有效單位價格 (以定價貨幣計算)，用來計算使用量的費率。 每個產品、服務系列、計量及供應項目的單位價格都不同。 |
| 數量 | 計費期間所購買或使用的數量 |
| 費用/點數 | 套用點數/退款後的費用淨額 |
| Azure 點數 | 套用至費用/點數的 Azure 點數金額|
| 稅率 | 稅率視國家/地區而定 |
| 稅額 | 購買時根據稅率套用的稅額 |
| 總計 | 購買的應付金額總計 |

### <a name="how-to-pay"></a>支付方式

發票底部會顯示支付帳單的指示。 您可以使用支票、電匯或線上付款。 如果您在線上付費，可以使用信用卡或 Azure 點數 (如果適用)。

### <a name="publisher-information"></a>發行者資訊

如果您的帳單中有第三方服務，發票底部會列出每個發行者的名稱和地址。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [了解帳單設定檔發票上的費用](review-customer-agreement-bill.md)
- [如何取得您的 Azure 帳單發票和每日使用量資料](../manage/download-azure-invoice-daily-usage-date.md)
- [檢視組織的 Azure 定價](../manage/ea-pricing.md)
- [檢視帳單設定檔的稅務文件](mca-download-tax-document.md)
