---
title: Azure 企業版註冊發票
description: 本文將說明如何管理 Azure 企業發票，並採取相關行動。
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 9fbc2ffcf1158f436c060817f8f25152f839c3e0
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037260"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 企業版註冊發票

本文將說明如何管理 Azure Enterprise 合約 (Azure EA) 發票，並採取相關行動。 您的發票即代表您的帳單。 請檢查其正確性。 您也應熟悉管理發票時可能需要的其他工作。

## <a name="change-a-po-number-for-an-overage-invoice"></a>變更超額發票的 PO 編號

如果企業系統管理員未在發票日期之前設定編號，則 Azure 企業版入口網站會自動產生預設的採購單 (PO) 號碼。 企業系統管理員可以在收到自動發票通知電子郵件後的七天內更新 PO 號碼。

### <a name="to-update-the-azure-services-purchase-order-number"></a>若要更新 Azure 服務採購單號碼：

1. 從 Azure 企業版入口網站中，選取 [報表]   > [使用量摘要]  。
1. 選取右上角的 [編輯 PO 號碼]  。
1. 選取 [Azure 服務]  選項按鈕。
1. 從日期範圍下拉式功能表中選取 [發票期間]  。

   您可以在收到發票通知後的七天內編輯 PO 號碼，但必須在支付發票費用之前。
1. 在[PO 號碼]  欄位中輸入新的 PO 號碼。
1. 選取 [儲存]  以提交變更。

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>若要更新 Azure Marketplace 採購單號碼：

1. 從 Azure 企業版入口網站中，選取 [報表]   > [使用量摘要]  。
1. 選取右上角的 [編輯 PO 號碼]  。
1. 選取 [Marketplace]  選項按鈕。
1. 從日期範圍下拉式功能表中選取 [發票期間]  。

   您可以在收到發票通知後的七天內編輯 PO 號碼，但必須在支付發票費用之前。
1. 在[PO 號碼]  欄位中輸入新的 PO 號碼。
1. 選取 [儲存]  以提交變更。

## <a name="cadence-of-azure-enterprise-billing"></a>Azure 企業版的計費步調

針對您購買的 Microsoft Azure 服務承諾用量，Microsoft 會在其每年的註冊生效日當天向您開立帳單。 針對使用量超過承諾用量的部分，Microsoft 會在之後收取費用。

- 承諾用量費用的報價基礎是每月費率，且會每年預先收取。
- 超額費用會每月計算，並於計費週期結束時收取。

### <a name="billing-intervals"></a>計費間隔

您的計費間隔取決於您選擇購買承諾的方式。 您的年度承諾用量與下列任一項相關：

- 您的註冊週年日
- 一年期修訂訂用帳戶的生效日期。

您收到超額發票的日期取決於您的註冊開始日期和設定：

- **開始日期早於 2018 年 5 月1 日的直接註冊**：
  - 如果您是直接 Enterprise 合約 (EA) 的客戶，Azure 服務會設定為年度計費週期 (不包括 Azure Marketplace 服務)。 您的計費週期會根據週年日 (合約生效的日期) 計算。
  - 如果您超過 EA 預付金 (MC) 閾值的 150%，則會自動轉換為以您周年日計算的季度計費週期。 您也會收到 Azure 服務超額發票。
  - 如果您未超過 MC 閾值的 150%，則您的註冊會維持在年度計費週期。 您將會在承諾用量年度結束時收到超額發票。

- **開始日期晚於 2018 年 5 月1 日的直接註冊**：
  - 您的 Azure 使用量和個別計費的費用發票則會採用每月計費週期。
  - 預付金中未涵蓋的任何費用都將計為超額款項。  

- **註冊開始日期早於 2018 年 5 月 1 日的間接註冊**：

  如果您是間接 Enterprise 合約 (EA) 客戶，而且開始日期在 2018 年 5 月1日之前，您的計費週期將會以季度來計算。 通路合作夥伴 (CP) 會直接向您開立發票。  

- **開始日期晚於 2018 年 5 月1 日的間接註冊**：

  您將使用每月計費週期。  

### <a name="increase-your-monetary-commitment"></a>增加您的預付金

您隨時都可以增加您的承諾用量。 您的費用會以該年度承諾期間的剩餘月份數目來計算。 例如，如果您註冊了一年期的修訂訂用帳戶，然後在第六個月增加承諾用量，我們就會針對該期間剩餘的六個月對您開立發票。 其後，您的承諾用量將會在承諾用量期間的後六個月進行更新。 這些新數量將用於判斷任何超額費用。

### <a name="overage"></a>超額部分

對於超額部分，您必須針對在計費週期內超出承諾用量的使用量或保留支付費用。 若要檢視個別項目超額數量的計算明細，請參閱使用量摘要報表，或連絡您的通路合作夥伴。

針對發票上的每個項目，您會看到：

- **應收金額**：費用總計
- **已使用的承諾用量**：用來涵蓋費用的承諾用量金額
- **淨額**：超出您承諾用量的費用

只有超過承諾用量的淨額，才會加上適用的稅額。

超額發票會自動開立。 發出通知和發票的時間，取決於您計費週期的結束日期。

- 超額通知通常會在計費結束日期後七天傳送。
- 超額發票會在發出通知後 7 到 9 天傳送。
- 您可以在發出超額通知到開立發票的七天內，檢查費用並更新系統產生的 PO 編號。

### <a name="azure-marketplace"></a>Azure Marketplace

自 2019 年 4 月的計費週期起，客戶已開始收到單一 Azure 發票，我們已將所有 Azure 和 Azure Marketplace 費用整合為單一發票，而不是兩張個別的發票。 這項變更對於澳洲、日本或新加坡的客戶並無影響。

在轉換為合併發票期間，您會收到一張個別的 Azure Marketplace 發票。 這張最後的個別發票會顯示您計費更新日期之前產生的 Azure Marketplace 費用。 該日期之後的 Azure Marketplace 費用將出現在您的 Azure 發票上。 在轉換期間之後，您將在合併發票上看到所有的 Azure 和 Azure Marketplace 費用。  

## <a name="adjust-billing-frequency"></a>調整帳單週期

客戶的帳單週期是每年、每季或每月。 計費週期會在客戶簽署合約時決定。 每月計費是最小的計費間隔。

- 針對直接註冊，將計費週期從每年變更為每季時，需要企業系統管理員的**核准**。 間接註冊需要合作夥伴系統管理員的核准。 變更會在目前的帳單季結束時生效。
- 若要將每年或每季的計費週期變更為每月，則必須**修訂**合約。  對現有註冊計費週期所做的任何變更，都需要企業系統管理員或「帳單連絡人」的核准。
- **提交**您的核准至 [Azure 企業版入口網站支援](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 選擇問題類別：**計費和發票**。

變更會在目前的帳單季結束時生效。

如果修訂 M503 已簽署，您就可以將所有合約的任何頻率移至每月計費。 

## <a name="credits-and-adjustments"></a>信用額度和調整

您可以在 [Azure 企業版入口網站](https://ea.azure.com)的 [報表]  區段中，查看套用至註冊的所有信用額度或調整項目。

若要檢視信用額度：

1. 在 [Azure 企業版入口網站](https://ea.azure.com)中，選取 [報表]  階段。
1. 選取 [使用量摘要]  。
1. 在右上角，將 **M** 變更為 **C** 檢視。
1. 擴大 Azure 服務承諾用量表格中的調整欄位。
1. 您會看到套用至您註冊的信用額度和簡短說明。 例如：服務等級協定點數。

## <a name="request-an-invoice-copy"></a>要求發票副本

若需要您的發票複本，請洽詢您的合作夥伴。

## <a name="overage-offset"></a>超額抵銷

若要將您的預付金套用至超額部分，您必須符合下列準則：

- 您有已產生的超額費用但尚未付費，並且在計費服務結束日期的一年內。
- 您的可用預付金金額應涵蓋產生的總費用，包括所有過去未付款的 Azure 發票。
- 必須完全關閉您要完成的計費期限。 計費會在每個月的第五天之後完全關閉。
- 必須完全關閉您要抵銷的計費期限。
- Azure 承諾用量折扣 (ACD) 是實際的新承諾用量減去為先前用量規劃的任何資金。 這項需求只適用於產生的超額費用。 這僅適用於使用預付金的服務，因此無法套用至 Azure Marketplace 費用。 Azure Marketplace 費用會個別計費。

若要完成超額抵銷，您或帳戶小組可以開啟支援要求。 這需要您企業系統管理員或帳單連絡人的電子郵件核准。

## <a name="view-price-sheet-information"></a>檢視價位表資訊

企業系統管理員可以檢視與他們 Azure 服務註冊相關聯的價格清單。

若要檢視目前的價位表：

1. 在 Azure 企業版入口網站中選取 [報表]  ，然後選取 [價位表]  。
2. 檢視價位表，或選取 [下載]  。

![顯示價位表資訊的範例](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

若要下載價格記錄清單：

1. 在 Azure 企業版入口網站中選取 [報表]  ，然後選取 [下載使用量]  。
2. 下載價位表。

![顯示如何下載較舊價位表的範例](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

價格差異的一些原因：

- 先前註冊和新註冊之間的價格可能已變更。 發生價格變更的原因是，價格是在合約開始日期到結束日期之間，針對特定註冊制定的。
- 當您轉移到新的註冊時，價格因為新合約而產生變更。 價格是由價位表所定義，新註冊中的價格可能較高。
- 如果註冊進入延長的期限，則價格也會變更。 價格會變更為隨用隨付費率。

## <a name="request-detailed-usage-information"></a>要求詳細的使用量資訊

企業系統管理員可以在 Azure 企業版入口網站中檢視其使用量資料的摘要、已使用的承諾用量金額，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

若要檢視特定帳戶的詳細使用量，請移至 [報表]   > [下載使用量]  ，來下載使用量詳細資料報表。

> [!NOTE]
> 使用量詳細資料報告不包含任何適用的稅金。 從使用量發生到使用量在報表上反映時，可能會有最多 8 小時的延遲時間。

針對間接註冊，您的合作夥伴必須啟用標記功能，才能看到任何成本相關資訊。

## <a name="reports"></a>報表

企業系統管理員可以在 Azure 企業版入口網站中檢視其使用量資料的摘要、已使用的承諾用量金額，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

### <a name="azure-enterprise-reports"></a>Azure 企業版報告

- 使用量摘要和圖表
- 服務使用量報表
- 餘額和費用報表
- 使用量詳細資料報表
- Azure Marketplace 費用報表
- 價位表
- 進階報表下載
- CSV 報表格式

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>若要檢視使用量摘要報告和圖表：

1. 前往 Azure 企業版入口網站。
1. 在左窗格中選取 [報告]  。
1. 選取 [使用量摘要]  索引標籤。
1. 從左上方的日期範圍功能表中選取承諾用量期間。
1. 選取圖表上的期間或月份來檢視其他詳細資料。
1. 在此索引標籤上，您可以：
   - 檢視每月使用量的圖表，其中包含使用量明細、服務的超額部分、個別計費的費用和 Azure Marketplace 費用。
   - 依圖表下方的部門、帳戶和訂用帳戶進行篩選。
   - 在 [依服務收費]  明細和 [依階層收費]  明細之間做切換。
   - 檢視 Azure 服務、個別計費費用及 Azure Marketplace 費用的詳細資料。

## <a name="service-usage-report"></a>服務使用量報表

[服務使用量報表] 頁面可讓企業系統管理員檢視其服務使用量資料的摘要。 使用量會顯示在所有帳戶和訂用帳戶的摘要層級上。 若要檢視詳細的使用量，您可以依據帳戶或訂用帳戶來篩選報表。

> [!NOTE]
> 產生使用量的日期與使用量反映在此報表中的時間，可能會有最多五天的延遲。

### <a name="to-view-the-report"></a>若要檢視報表：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]  。
1. 選取 [使用量摘要]  索引標籤。
1. 選取日期範圍。
1. 選擇要檢視的帳戶或訂用帳戶。
1. 您可以選擇性地執行下列動作：
   - 將檢視變更為 [依服務收費]  或 [依階層收費]  ，以顯示不同的明細。
   - 檢視服務名稱、測量單位、使用單位、生效費率和延伸成本的詳細資料。

## <a name="download-csv-reports"></a>下載 CSV 報表

企業系統管理員可以使用每月報表下載頁面，將數個報表下載為 CSV 檔案。 可下載的報表包括：

- 餘額和費用報表
- 使用量詳細資料報表
- Azure Marketplace 費用報表
- 價位表

### <a name="to-download-reports"></a>若要下載報表：

1. 在 Azure 企業版入口網站中，選取 [報表]  。
1. 從頂端功能區選取 [使用量下載]  。
1. 選取適當月份報表旁的 [下載]  。

### <a name="csv-report-formatting-issues"></a>CSV 報表格式問題

在 Azure 企業版入口網站中以歐元檢視 CSV 報表的客戶，可能會遇到關於逗點和句點的格式問題。

例如，您可能會看到：

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

您應該會看到：

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

發生此格式問題的原因是 Excel 匯入功能中的預設設定。 Excel 會將所有欄位匯入為「一般」文字，並假設數字會以數學標準分隔。 例如："1,000.00"。

如果歐洲貨幣以句點 (.) 作為千位分隔符號，並以逗點作為小數點分隔符號 (,)，將會無法正確顯示。 例如："1.000,00"。 此匯入結果可能會隨著您的區域語言設定而不同。

### <a name="to-import-the-csv-file-without-formatting-issues"></a>若要匯入 CSV 檔案而不發生格式問題：

1. 在 Microsoft Excel 中，移至 [檔案]   > [開啟]  。
   「文字匯入精靈」隨即出現。
1. 在 [原始資料類型]  底下，選擇 [分隔符號]  。  預設值為「固定寬度」  。
1. 選取 [下一步]  。
1. 在 [分隔符號] 底下，選取 [逗點]  的核取方塊。 如果已選取 [Tab]  ，請將其清除。
1. 選取 [下一步]  。
1. 捲動至 [ResourceRate]  和 [ExtendedCost]  資料行。
1. 選取 [ResourceRate]  資料行。 其會顯示為黑色。
1. 在 [資料行資料格式]  區段底下，選取 [文字]  (而非 [一般]  )。 資料行標頭會從 [一般]  變更為 [文字]  。
1. 針對 [延伸成本]  資料行重複步驟 8 和 9，然後選取 [完成]  。

> [!TIP]
> 如果您已設定為在 Excel 自動開啟 CSV 檔案，則必須在 Excel 中改用「開啟」  功能。 在 Excel 中，移至 [檔案]   > [開啟]  。

## <a name="balance-and-charge-report"></a>餘額和費用報表

「餘額與費用報表」可提供餘額、新購買、Azure Marketplace 服務費用、調整，以及超額部分費用的每月摘要資訊。

Azure 服務承諾用量摘要資料表中的所有資料列每個月都會保持不變。 您可以在 [新購買詳細資料]  和 [調整詳細資料]  區段下找到所有購買和調整的詳細資料。

### <a name="download-the-balance-and-charge-report"></a>下載餘額和費用報表

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 在左窗格中選取 [報告]  。
1. 選取 [報表下載]  索引標籤。
1. 在 [餘額和費用]  資料行底下選取適當的月份，然後選取以下載報表。

## <a name="usage-detail-report"></a>使用量詳細資料報表

使用量詳細資料報表會提供註冊所用服務和數量的每月摘要。 其中包括計量名稱、計量類型和已使用數量的資訊。

### <a name="download-the-usage-detail-report"></a>下載使用量詳細資料報表

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]  。
1. 選取 [下載使用量]  索引標籤。
1. 在 [使用量詳細資料]  資料行底下選取適當的月份，然後選取以下載報表。

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure 企業版入口網站報表中的 Azure Marketplace 費用

Azure Marketplace 費用有兩種類型：

- **依使用量：** 依交易單位測量產品。  例如，虛擬機器會按小時計費，Bing API 搜尋會依搜尋次數計費。
- **月費**：根據使用量或存取按月計費。

如需 Azure Marketplace 費用的詳細資訊，請參閱 [Azure Marketplace 常見問題集](https://azure.microsoft.com/marketplace/faq/)。

若要在 Azure 企業版入口網站中檢視不同的費用：

- **使用量摘要報表**：**同時**顯示依使用量和月費計算的 Azure Marketplace 費用。
- **Marketplace 費用報表**：**僅**顯示以使用量計算的 Azure Marketplace 費用。  一次性費用不會顯示。

> [!NOTE]
> Azure Marketplace 不適用於 MPSA 註冊。

## <a name="advanced-report-download"></a>進階報表下載

如需報告特定的日期範圍或帳戶，您可以使用進階報表下載。 自 2016 年 8 月 30 日起，輸出檔案的格式已變更為 CSV，可容納較大的記錄集。

1. 在 Azure 企業版入口網站中，選取 [進階報告下載]  。
1. 選取 [適當日期範圍]  。
1. 選取 [適當帳戶]  。
1. 選取 [要求使用量資料]  。
1. 選取 [重新整理]  按鈕，直到報表狀態更新為 [下載]  為止。
1. 下載報表。

## <a name="reporting-for-non-enterprise-administrators"></a>非企業系統管理員的報告

企業系統管理員可授與部門系統管理員 (DA) 和帳戶擁有者 (AO) 在註冊上檢視費用的權限。 具有存取權的帳戶擁有者可以下載其帳戶和訂用帳戶專屬的 CSV 報表。 他們也可以在 Azure 企業版入口網站的 [報表] 區段底下，透過視覺化方式查看資訊。

### <a name="to-enable-access"></a>若要啟用存取權：

 1. 以企業系統管理員身分登入 Azure 企業版入口網站。
 1. 選取左側導覽列上的 [管理]  。
 1. 選取 [註冊]  索引標籤。
 1. 在 [註冊詳細資料]  區段底下，選取 [DA 檢視費用]  或 [AO 檢視費用]  旁的鉛筆圖示。
 1. 選取 [啟用]  。
 1. 選取 [儲存]  。

### <a name="to-view-reports"></a>若要檢視報表：

1. 以部門系統管理員或帳戶擁有者的身分登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]  。
1. 選取 [使用量摘要]  索引標籤，以視覺化方式檢視帳戶和訂用帳戶的資訊。
1. 選取 [使用量下載]  以檢視 CSV 報表。

在使用「進階報表下載」  功能時，部門系統管理員和帳戶擁有者無法檢視費用。 成本會顯示為 $0。

可檢視費用的帳戶擁有者權限範圍包括帳戶擁有者，以及所有具有相關訂用帳戶權限的使用者。 如果您是間接客戶，則必須由通路合作夥伴為您啟用成本功能。

## <a name="move-usage-data-to-another-enrollment"></a>將使用量資料移至另一個註冊

只有在追溯轉移時，才需移動使用量資料。 有兩個選項可將使用量資料從一個註冊移至另一個註冊：

- 將帳戶從一個註冊轉移至另一個註冊
- 將註冊從一個註冊轉移至另一個註冊

無論是哪一個選項，您都必須向 EA 支援小組提交[支援要求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)，以取得協助。 

## <a name="azure-ea-pricing-overview"></a>Azure EA 定價概觀

本節提供如何計算使用量的詳細資料。 其中會針對計算較複雜的 Enterprise 合約，解答其中各種 Azure 服務費用的許多常見問題。

### <a name="price-protection"></a>價格保護

身為客戶或通路合作夥伴，我們保證您會收到客戶價位表 (CPS) 上顯示的價格 (或比其更低的價格)，或是 Azure 購買生效日上的價格。 此價格稱為基準價格。 就 Azure 購買後導入的服務而言，我們向您收取的價格會是服務首次導入時依適用折扣層級生效的價格。 此價格保護適用於承諾用量的持續期間，可能是一年或三年，視 Enterprise 合約而定。

### <a name="price-changes"></a>價格變更

Microsoft 可能會在註冊期間內，就個別的 Azure 服務調降目前的 Enterprise 合約價格。 我們會在較低的價格生效時，將這些調降的費率擴及現有的客戶。 如果之後這些費率有所提高，您的註冊服務價格將不會超過您在上面所定義的價格保護上限。 不過，若是相對於先前已降低的價格，費率可能會有所提高。 無論是哪一種情況，我們都會傳送電子郵件給註冊的相關企業和合作夥伴管理員，以通知客戶和間接通路合作夥伴即將進行價格變更。

### <a name="current-effective-pricing"></a>目前的有效定價

客戶和通路合作夥伴可藉由登入 [Azure 企業版入口網站](https://ea.azure.com/)並檢視該註冊的價位表頁面，來檢視其目前的註冊定價。 如果您是透過我們其中一個通路合作夥伴間接購買 Azure，且他們並未為您的註冊啟用顯示標記價格的功能，您就必須經由通路合作夥伴取得定價更新。

### <a name="introduction-of-new-azure-services"></a>導入新的 Azure 服務

我們除了持續強化 Azure 外，也會定期新增與現有服務分開定價的新服務。 有些預覽服務會自動啟用，有些則需由客戶在 [Azure 帳戶入口網站](https://account.windowsazure.com/PreviewFeatures)中操作，才可供使用。

某些服務在首次導入生效時會先以促銷價格提供，但日後價格有可能會調升。

當服務從預覽進入公開上市 (GA) 階段時，價格可能會隨著完整效能和可靠性 SLA 的套用而增加。 這類的價格提高並不受限於一般的基準價格保護。 這些服務的使用量會以提高的費率計費。 若要避免因為這些新服務而產生相關費用，您必須選擇不要使用這些服務。

### <a name="introduction-of-regional-pricing"></a>導入區域定價

除了導入新的服務以外，服務也會隨著服務的區域支援有所增加，而定期從單一全域基準變更為更具區域性的模式。

服務區域化首次導入時，這些新區域會套用基準價格保護。 不過，之後若導入這些相同服務的其他區域，這些服務將被視為新服務，並且會為其提供獨立於基準價格保護以外的個別費率。

### <a name="enterprise-devtest"></a>Enterprise 開發/測試

企業系統管理員可讓帳戶擁有者根據 Enterprise 開發/測試供應項目建立訂用帳戶。 帳戶擁有者必須為基礎訂閱者設定所需的 Enterprise 開發/測試訂用帳戶。 此設定可讓有效的 Visual Studio 訂閱者以特殊的 Enterprise 開發/測試費率在 Azure 上執行開發和測試工作負載。 如需詳細資訊，請參閱 [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)。

## <a name="enterprise-agreement-usage-calculations"></a>Enterprise 合約的使用量計算

請參閱 [Azure 服務](https://azure.microsoft.com/services/)和 [Azure 定價](https://azure.microsoft.com/pricing/)，以取得每項個別服務的基本公開定價資訊、測量單位、常見問題，以及使用量報告指導方針。 您可以在下列各節中找到關於 EA 計算的詳細資訊。

### <a name="enterprise-agreement-units-of-measure"></a>Enterprise 合約的測量單位

Enterprise 合約的測量單位常會與我們其他方案 (例如 Microsoft Online Services 合約方案 (MOSA)) 中顯示的不同。 此差異的意思是，許多服務的測量單位都會進行彙總，以提供標準化的定價。 在 Azure 企業版入口網站中，[使用量摘要] 檢視中顯示的測量單位一律為企業量值。 [易記服務名稱](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) Excel 檔案中提供了每項服務目前的測量單位和轉換的完整清單。

### <a name="rounding-rules"></a>進位規則

Azure 企業版入口網站會遵循 IEEE 標準銀行家進位法或高斯進位邏輯。 此邏輯會將半位數值進位至最接近的偶數位數值。 多數半進位的進位邏輯一律會將半位數值進位至下一個最高的數值。 相較於標準 Excel 邏輯，此 Azure 企業版入口網站方法實際上可提供比群組更精確的總和。

說明：當第一個捨入的數位值為 5，且其後沒有任何數位值，或其後的數位值為零時，則捨入至最接近的偶數位數值。 例如：2.315 和 2.325 都會捨入到最接近的百分位，變成 2.32。

下表供您參考，其中示範為用於進位和轉換的 Azure 企業版入口網站規則建立模型時，應使用的 Excel 公式：

| 狀況 | 銀行家邏輯公式 |
| --- | --- |
| 進位用法 | =MROUND({_source_}, 0.0002) |
| 進位定價 (2 個小數位數) | =MROUND({_source_}, 0.02) |
| 進位定價 (0 個小數位數) | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>使用量詳細資料包表和使用量摘要頁面之間的轉換

在 [下載使用量資料] 報表中，您可以看到原始資源使用量最多有六個小數位數。 不過，Azure 企業版入口網站中顯示的使用量資料會捨入至四個小數位數作為承諾用量單位，並將超額單位截斷為零個小數位數。 在轉換成 Azure 企業版入口網站所使用的單位之前，原始使用量資料會先捨入成四位數。 然後，轉換後的企業單位會重新捨入為四位數。 轉換前的實際使用時數只會顯示在下載使用量報表中，而不會顯示於 Azure 企業版入口網站。

例如：使用量詳細資料報表中報告了實際 SQL Server 時數為 694.533404。 這些單位會以 100 個計算時數為單位轉換為 6.94533404，然後再捨入至 6.9453 並顯示於 Azure 企業版入口網站中。

- 若要決定應收帳單金額，這些顯示的單位會乘以承諾用量單位價格，且結果會截斷為兩個小數位數。 針對日圓 (JPY) 和韓元 (KRW)，應收金額會捨入為零個小數位數。
- 針對超額部分，計費單位會截斷為六位數，然後乘以超額單位價格，以決定應收帳單金額。
- 針對受控服務提供者 (MSP) 的計費，所有與標示為 MSP 的部門相關聯的使用量在轉換為 EA 測量單位之後，都會截斷為零個小數位數。 因此，此使用量的總和可能會低於 Azure 企業版入口網站中報告的所有使用量總和。 這取決於 MSP 是在其預付金餘額內或已超額。

### <a name="graduated-pricing"></a>分級定價

Enterprise 合約定價目前不包含分級定價 (在分級定價中，每單位費用會隨著使用量的增加而降低)。 當您從具有分級定價的 MOSA 程式移至 Enterprise 合約時，您的價格會以該服務的基本層為準 (減去任何 Enterprise 合約適用的折扣調整)。

### <a name="partial-hour-billing"></a>零散時數計費

所有已計費的使用量都會根據轉換為零散時數的分鐘數來計算，而不是以一小時作為增量單位。 以小時為單位列出的 Enterprise 費率會套用至包含任何零散時數的總時數。

### <a name="average-daily-consumption"></a>平均每日使用量

某些服務會按月計費，但使用量報告會以日為單位。 在這些案例中，使用量會每天評估一次，然後除以 31，並以該計費月份的天數進行加總。 因此，任何月份的費率都絕不會超過預期，而少於 31 天的月份則會有略低的費率。

### <a name="compute-hours-conversion"></a>計算時數轉換

在 2016 年 1 月 28 日之前，A0、A2、A3 及 A4 的標準與基本虛擬機器和雲端服務的使用量，均以 A1 虛擬機器計量分鐘數呈現。 A0 VM 會計算為 A1 VM 分鐘的分數，而 A2、A3 及 A4 會轉換成倍數。 由於此原則對客戶造成了些許混淆，因此我們進行了變更，讓 A0、A2、A3 及 A4 計量有專屬的每分鐘使用量。

新的計量方式已在 2016 年 1 月 27 日到 1 月 28 日之間生效。 在顯示此轉換期間使用量的 CSV 下載上，您可以同時看到這兩者：

- 使用 A1 計量呈現的使用量
- 使用與部署大小相對應的新專屬計量呈現的使用量。

| **部署大小** | **至 2016 年 1 月 26 日止，使用量是以 A1 的倍數呈現** | **自 2016 年 1 月 27 日起，使用量改以專屬計量呈現** |
| --- | --- | --- |
| A0 | 0.25 倍的 A1 時數 | 1 倍的 A0 時數 |
| A2 | 2 倍的 A1 時數 | 1 倍的 A2 時數 |
| A3 | 4 倍的 A1 時數 | 1 倍的 A3 時數 |
| A4 | 8 倍的 A1 時數 | 1 倍的 A4 時數 |

### <a name="regions"></a>區域

針對區域會對定價產生影響的服務，下表顯示了地理位置和區域的對應：

| 地理區域 | 資料傳輸區域 | CDN 區域 |
| --- | --- | --- |
| 區域 1 | 北歐 <br> 西歐 <br> 美國西部 <br> 美國東部 <br> 美國中北部 <br> 美國中南部 <br> 美國東部 2 <br> 美國中部 | 北美洲 <br> 歐洲 |
| 區域 2 | 亞太地區東部 <br> 亞太地區東南部 <br> 日本東部 <br> 日本西部 <br> 澳大利亞東部 <br> 澳大利亞東南部 | 亞太地區 <br> 日本 <br> 拉丁美洲 <br> 中東/非洲 <br> 澳大利亞東部 <br> 澳大利亞東南部 |
| 區域 3 | 巴西南部 |   |

在位於相同資料中心內的服務之間，資料輸出不會產生任何費用。 例如，Office 365 和 Azure。

### <a name="monetary-commitment-and-unbilled-usage"></a>預付金和未開立帳單的使用量

Azure 預付金是針對 Azure 服務預付的金額。 使用服務時就會用到預付金。 第一方 Azure 服務會根據預付金來計費。 不過，有些費用會個別計費，而 Azure Marketplace 服務不會取用預付金。

### <a name="charges-billed-separately"></a>個別計費的費用

某些由第三方來源提供的產品和服務並不會使用 Azure 承諾用量金額。 這些項目會在標準計費週期的超額發票中個別計費。

我們已將所有 Azure 和 Azure Marketplace 費用整合為符合註冊計費週期的單一發票。 合併發票不適用於澳洲、日本或新加坡的 客戶。

合併發票會先顯示 Azure 使用量，然後接著顯示所有 Azure Marketplace 費用。 澳洲、日本或新加坡的客戶仍會在個別發票上看到 Azure Marketplace 費用。

如果標準計費週期結束時並無超額使用量，則個別計費的費用將會個別開立發票。 此程序適用於澳洲、日本或新加坡的客戶。

下列服務會個別計費：

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 的註冊使用者
- Openlogic
- 遠端存取權限 XenApp Essentials 已註冊的使用者
- Ubuntu Advantage
- Visual Studio Enterprise (每月)
- Visual Studio Enterprise (每年)
- Visual Studio Professional (每月)
- Visual Studio Professional (每年)

## <a name="azure-marketplace-for-ea-customers"></a>適用於 EA 客戶的 Azure Marketplace

直接客戶可以在 Azure 企業版入口網站上看見 Azure Marketplace 費用。 Azure Marketplace 的購買和使用都會在預付金以外按季或按月於期末計費。

間接客戶可以在 Azure 企業版入口網站的 [管理訂用帳戶]  頁面上找到其 Azure Marketplace 訂用帳戶，但價格將會隱藏。 客戶應該連絡其授權解決方案提供者 (LSP) 以了解相關的 Azure Marketplace 費用資訊。

新的每月或每年週期性 Azure Marketplace 購買，將在購買 Azure Marketplace 項目的期間內以完整金額計費。 這些項目會在原始購買下一個期間的同一天自動續約。

現有的每月週期性費用會繼續在每個日曆月份的第一天續約。 年度費用則會在購買屆滿一年的當天續約。

Azure Marketplace 上提供的某些第三方轉銷商服務會使用您 Enterprise 合約 (EA) 的預付金餘額。 這些服務原先在 EA 承諾用量範圍外計費，並另外開立發票。 Azure Marketplace 中這些服務的 EA 承諾用量金額有助於簡化客戶購買及款項管理。 如需目前使用預付金的完整服務清單，請參閱 [Azure 網站上的 2018 年 3 月 6 日更新](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。

### <a name="partners"></a>合作夥伴

LSP 可以從 Azure 企業版入口網站中的價位表頁面下載 Azure Marketplace 價目表。 選取右上角的 [Marketplace 價目表]  連結。 Azure Marketplace 價目表會顯示所有可用的服務及其價格。

若要下載價目表：

1. 在 Azure 企業版入口網站中，移至 [報表]   > [價位表]  。
1. 在右上角，找出您使用者名稱底下的 Azure Marketplace 價目表連結。
1. 以滑鼠右鍵按一下該連結，然後選取 [另存目標]  。
1. 在 [儲存]  視窗上，將文件的標題變更為 `AzureMarketplacePricelist.zip`，這會將檔案從 .xlsx 變更為 .zip 檔案。
1. 下載完成後，您將取得一個 zip 檔案，內含特定國家/地區的價目表。
1. LSP 應參考個別的國家/地區檔案，以了解特定國家/地區的定價。 LSP 可以使用 [通知]  索引標籤來了解新增或淘汰的 SKU。
1. 價格不常發生變更。 LSP 會在 30 天前收到價格調升和外匯 (FX) 變更的電子郵件通知。
1. LSP 的每個註冊每季會收到每個 ISV 的一張發票。

### <a name="enabling-azure-marketplace-purchases"></a>啟用 Azure Marketplace 購買

企業系統管理員能夠為註冊下的所有 Azure 訂用帳戶「停用」或「啟用」Azure Marketplace 購買。 如果企業系統管理員停用購買，而某些 Azure 訂用帳戶已有 Azure Marketplace 訂用帳戶，這些 Azure Marketplace 訂用帳戶將不會取消或受到影響。

雖然客戶可在 Azure 企業版入口網站中將其直接 Azure 訂用帳戶與註冊產生關聯，藉以將訂用帳戶轉換至 Azure EA，但此動作並不會自動轉換子系訂用帳戶。

若要啟用 Azure Marketplace 購買：

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 移至 [管理]  。
1. 在 [註冊詳細資料]  底下，選取 **Azure Marketplace** 明細項目旁的鉛筆圖示。
1. 視需要切換 [啟用/停用]  或 [僅限免費 BYOL SKU]  。
1. 選取 [儲存]  。

> [!NOTE]
> [BYOL (自備授權)] 和 [僅限免費] 選項會限制只有 BYOL 和免費 SKU 可購買和取得 Azure Marketplace SKU。

深入了解 [Azure 企業版入口網站報表中的 Azure Marketplace 費用](#azure-marketplace-charges-in-azure-enterprise-portal-reports)。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA 的服務按小時計費

相較於 MOSP 中的每月費率，Enterprise 合約下的下列服務會按小時計費：

- 應用程式傳遞網路
- Web 應用程式防火牆

### <a name="azure-remoteapp"></a>Azure RemoteApp

如果您有 Enterprise 合約，您會根據 Enterprise 合約的價格等級支付 Azure RemoteApp 費用。 不會產生額外的費用。 標準價格包含最初的 40 小時。 無限制價格會涵蓋最初的 80 小時。 超過 80 小時後，遠端應用程式即停止發出使用量。

## <a name="azure-marketplace-faq"></a>Azure Marketplace 常見問題集

本節將說明您的 Azure 預付金可如何套用到 Azure Marketplace 中一些第三方轉銷商服務。

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Azure Marketplace 服務和 EA 預付金有哪些變更？

自 2018 年 3 月 1 日起，部分第三方轉銷商服務可取用 EA 預付金 (MC)。 除了 Azure 保留的 VM 執行個體 (RI) 以外，服務先前都會以 EA 預付金以外的費率計費，並且個別開立發票。

我們已擴充 MC 的適用性，以納入一些最常購買且由第三方發佈的 Azure Marketplace 服務。 Azure Marketplace 中這些服務的 EA 預付金有助於簡化您的購買及款項管理。

### <a name="why-did-we-make-this-change"></a>為什麼我們要進行此變更？

一直以來，客戶都希望能有其他方式可充分運用其預付的 MC 款項。 客戶經常要求這項變更，而且影響了大多數 Azure Marketplace 客戶。

### <a name="how-do-you-benefit"></a>您如何受益？

您可以獲得更簡單的計費體驗，並能夠更有效地使用 EA 預付金。 因為這些服務已包含在您的預付型 MC 中，所以您的 EA 預付金會變得更有價值。

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>哪些 Azure Marketplace 服務會使用 EA 預付金？我該如何得知？

當您購買可使用 MC 的服務時，Azure Marketplace 會提供免責聲明。 支援的項目有 Red Hat、SUSE、Autodesk 和 Oracle 發行的一些服務。 目前，由其他廠商發佈且名稱類似的服務並不會從 MC 中扣款。 此常見問題的結尾處提供了完整清單。

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>如果我的 EA 預付金用完了該怎麼辦？

如果您用完所有的 MC 且已產生超額部分時，這些服務的相關費用將連同任何其他取用服務顯示在下一張超額發票中。 在 2018 年 3 月 1 日進行變更之前，這些費用會與其他 Azure Marketplace 服務一起開立發票。

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>為什麼不是所有 Azure Marketplace 項目都能使用 EA 預付金？

我們一直都在設法提供 EA 預付金方面的最佳客戶體驗。 這項變更將使許多客戶受惠，並解決在 Azure Marketplace 中的總支出比例偏高的問題。 未來可能會新增其他服務。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>這對間接註冊和合作夥伴有何影響？

我們的間接註冊客戶或合作夥伴將不受影響。 這些服務將與其他取用服務一樣，受到相同合作夥伴標記功能的控管。 唯一的變更是，費用會顯示在不同的發票上，以及使用客戶的 EA 預付金來支付費用。

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>是否有使用 EA 預付金的 Azure Marketplace 服務清單？

特定 Azure Marketplace 供應項目可以使用預付金。 如需參與此方案的完整產品清單，請參閱[使用預付金的第三方服務](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。

## <a name="power-bi-reporting"></a>Power BI 報告

Power BI 報告適用於能夠檢視帳單資訊的 EA 直接、合作夥伴和間接客戶。

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro 已可供 EA 客戶使用。 您可以使用 Power BI Pro 來產生和共用報表，以有效率地管理成本資料。 此外，還有其他共同作業和資料重新整理功能可供使用。 Power BI Pro 提供了更高的資料容量和資料串流限制。

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>若要存取 Microsoft Azure 使用量見解：

1. 移至 [Microsoft Azure 使用量見解](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)。
1. 選取 [立即取得]  。
1. 提供註冊號碼和月份數，然後選取 [下一步]  。
1. 提供您的 API 存取金鑰以進行連線。 您可以在[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)中找到註冊的金鑰。
1. 選取 [登入]  以自動啟動匯入程序。
1. 完成時，新的儀表板、報表和模型將會出現在導覽窗格中。 選取儀表板即可檢視匯入的資料。

> [!TIP]
>
> - 若要了解如何為您的註冊產生 API 金鑰，請參閱[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)上的 API 報表說明檔。
> - 如需如何將 Power BI 連線到您 Azure 使用量的詳細資訊，請參閱 [Microsoft Azure 使用量見解](/power-bi/desktop-connect-azure-cost-management)。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>若要存取舊版 Power BI EA 內容套件：

1. 移至 [Power BI 網站](https://app.powerbi.com/getdata/services/azure-enterprise)。
1. 使用有效的公司或學校帳戶登入。

   公司或學校帳戶可以是您用於透過 Azure 企業版入口網站存取註冊的相同或不同帳戶。
1. 在服務的儀表板上選取 [Microsoft Azure 企業版]  ，然後選取 [連線]  。
1. 在 [連線到 Azure 企業版]  畫面上，填入下列欄位：
    - Azure 環境 URL：[https://ea.azure.com](https://ea.azure.com/)
    - 月份數：1 到 36
    - 註冊號碼：您的註冊號碼
1. 選取 [下一步]  。
1. 在 [驗證金鑰]  方塊上，輸入 API 金鑰。

    您可以在 Azure 企業版入口網站的 [下載使用量]  索引標籤下取得 API 金鑰。選取 [API 存取金鑰]  ，然後將金鑰貼到 [帳戶金鑰]  方塊中。
1. 資料需要約 5-30 分鐘的時間才能載入至 Power BI，視資料集的大小而定。

## <a name="reports-faq"></a>報表常見問題集

本節將說明有關報表的常見問題。

### <a name="why-is-my-cost-showing-as-0"></a>為何我的成本顯示為 $0？

針對**直接註冊**客戶，企業系統管理員可以將使用量報告成本/定價資訊的存取權提供給帳戶擁有者和部門系統管理員。 請遵循下列步驟：

1. 在 Azure 企業版入口網站中，選取左側導覽區中的 [管理]  。
1. 選取 DA (部門系統管理員) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用]  並儲存。
1. 選取 AO (帳戶擁有者) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用]  並儲存。

> [!NOTE]
> 如果您是帳戶擁有者或部門系統管理員，請洽詢企業系統管理員以啟用定價功能。

針對**間接註冊**的客戶，請向您的合作夥伴確認他們已為您啟用定價功能。 這只能由合作夥伴完成。 啟用之後，您就能以企業系統管理員的身分查看註冊的成本和價格。

想要為帳戶擁有者或部門系統管理員啟用 [檢視費用] 功能的合作夥伴，請遵循**直接註冊**底下的步驟。

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>使用量詳細資料報表上為什麼沒有任何 SKU 資訊？

使用量詳細資料報表不包含任何 SKU 資訊。 不過，此報表會包含使用量資訊，因此您可以下載價位表報表來取得 SKU 資訊。

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>為什麼 Azure Marketplace 的總金額不符合使用量摘要和詳細資料的報表？

Azure Marketplace 費用報表只會顯示依使用量計算的 Marketplace 費用。 一次性費用不會顯示。 請查看使用量摘要頁面，以取得依使用量計算的費用和一次性費用的最新使用量。

### <a name="why-is-there-no-information-on-my-api-report"></a>為什麼我的 API 報表上沒有任何資訊？

API 金鑰每六個月就會過期。 如果您遇到問題，企業系統管理員應該會產生新的 API 金鑰。 請記得遵循 API 報表常見問題集中的步驟。

### <a name="why-isnt-my-power-bi-report-working"></a>為什麼我的 Power BI 報表無法運作？

針對 Power BI 的問題，請向 [Power BI 支援小組](https://support.powerbi.com)登記票證。

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>為什麼我的資源標籤不會顯示在我的報表中

資源標籤可從 Azure 入口網站管理。 您可以在 [Azure 入口網站](https://portal.azure.com)中連絡 Azure 訂用帳戶小組。 請依照[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)一文中的步驟進行。

### <a name="why-does-my-resource-rate-change-every-day"></a>為何我的資源費率每天都會變更？

詳細使用量報告中所顯示的資源速率是計算值。 其代表服務收取的每月平均費率。 資源費率會以服務單位的每月承諾用量和每月超額費用的平均值來計算。 根據承諾用量和超額費率計費的使用量部分，將會持續變更到該月結束的當天為止。 因此，列出的資源費率也會在該月份期間變更。 資源費率會在當月結束後的第五天鎖定。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>資源費率計算程序的詞彙

- **原始單位總計：** 詳細使用量報表中的已使用數量。
- **每單位的 MOCP 資源：** 上游使用量系統會以不同的單位發出每項服務的使用量。 (預設)
- **每單位使用量：** Azure 企業版測量單位。 (預設)
- **價格：** Azure 企業版入口網站中的單價。
- **總成本：** 詳細使用量報表中的延伸成本，或 Azure 企業版入口網站中已使用的承諾用量加上超額部分。

### <a name="charges-calculations"></a>費用計算

- **轉換成已使用的 MOCP 資源** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **轉換成使用的單位** = `Consumed MOCP Resources / Consumption per Unit`
- **計算總成本** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>使用量計算邏輯中的邏輯

**資源費率** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

資源費率會根據您的費用來衍生。 可能不符合價位表中的實際單價。

在 [下載使用量資料] 報表中，您可以看到原始資源使用量最多有六個小數位數。 此資料用於計算超額費用。 不過，Azure 企業版入口網站中顯示的使用量資料會捨入至四個小數位數作為承諾用量單位，並將超額單位截斷為零個小數位數。 在 Azure 企業版入口網站底下，所有超額使用量都只會以完整單位收費。 您可能會發現，以超額部分或混合月份計費的使用量，在單價和資源費率之間有很大的差異。

## <a name="next-steps"></a>後續步驟

- 下列 Excel 檔案提供 Azure 服務的詳細資料，並會在每個月的 6 號和 20 號更新：

   | Title | 描述 | 檔案名稱 |
   | --- | --- | --- |
   | [易記服務名稱](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 列出所有作用中的服務，並包括： <br>  <ul><li>服務類別</li>   <li>易記服務名稱</li>   <li>承諾用量名稱和組件編號</li> <li>使用量名稱和組件編號</li>   <li>測量單位</li>   <li>報表中使用量與顯示的企業版入口網站使用量之間的轉換因素</li></ul> | Friendly\_Service\_Names.xlsx |
   | [服務下載欄位](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 此試算表會列出「使用量下載報表」中的服務相關欄位所有的可能組合。 | Service\_Download\_Fields.xlsx |

- 如需了解發票和費用的相關資訊，請參閱[了解您的 Azure Enterprise 合約帳單](../understand/review-enterprise-agreement-bill.md)。
- 若要開始使用 Azure 企業版入口網站，請參閱[開始使用 Azure EA 入口網站](ea-portal-get-started.md)。
