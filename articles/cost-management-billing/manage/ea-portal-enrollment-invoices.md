---
title: Azure 企業版註冊發票
description: 本文將說明如何管理 Azure 企業發票，並採取相關行動。
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 5c26af74a392e3e6d3bb5557960b81604de7cdf3
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807802"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 企業版註冊發票

本文將說明如何管理 Azure Enterprise 合約 (Azure EA) 發票，並採取相關行動。 您的發票即代表您的帳單。 請檢查其正確性。 您也應熟悉管理發票時可能需要的其他工作。

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>變更可即將超額的發票 PO 編號

如果企業系統管理員未在發票日期之前設定編號，則 Azure 企業版入口網站會自動產生預設的採購單 (PO) 號碼。 企業系統管理員可以在收到自動發票通知電子郵件後的七天內更新 PO 號碼。

### <a name="to-update-the-azure-services-purchase-order-number"></a>若要更新 Azure 服務採購單號碼：

1. 從 Azure 企業版入口網站中，選取 [報表]**** > [使用量摘要]****。
1. 選取右上角的 [編輯 PO 號碼]****。
1. 選取 [Azure 服務]**** 選項按鈕。
1. 從日期範圍下拉式功能表中選取 [發票期間]****。

   您可以在收到發票通知後的七天內編輯 PO 號碼，但必須在支付發票費用之前。
1. 在[PO 號碼]**** 欄位中輸入新的 PO 號碼。
1. 選取 [儲存]**** 以提交變更。

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>若要更新 Azure Marketplace 採購單號碼：

1. 從 Azure 企業版入口網站中，選取 [報表]**** > [使用量摘要]****。
1. 選取右上角的 [編輯 PO 號碼]****。
1. 選取 [Marketplace]**** 選項按鈕。
1. 從日期範圍下拉式功能表中選取 [發票期間]****。

   您可以在收到發票通知後的七天內編輯 PO 號碼，但必須在支付發票費用之前。
1. 在[PO 號碼]**** 欄位中輸入新的 PO 號碼。
1. 選取 [儲存]**** 以提交變更。

## <a name="azure-enterprise-billing-frequency"></a>Azure 企業帳單週期

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

### <a name="adjust-billing-frequency"></a>調整帳單週期

客戶的帳單週期是每年、每季或每月。 計費週期會在客戶簽署合約時決定。 每月計費是最小的計費間隔。

- 針對直接註冊，將計費週期從每年變更為每季時，需要企業系統管理員的**核准**。 間接註冊需要合作夥伴系統管理員的核准。 變更會在目前的帳單季結束時生效。
- 若要將每年或每季的計費週期變更為每月，則必須**修訂**合約。  對現有註冊計費週期所做的任何變更，都需要企業系統管理員或「帳單連絡人」的核准。
- **提交**您的核准至 [Azure 企業版入口網站支援](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 選擇問題類別：**計費和發票**。

變更會在目前的帳單季結束時生效。

如果修訂 M503 已簽署，您就可以將所有合約的任何頻率移至每月計費。 

### <a name="request-an-invoice-copy"></a>要求發票副本

若需要您的發票複本，請洽詢您的合作夥伴。

## <a name="credits-and-adjustments"></a>信用額度和調整

您可以在 [Azure 企業版入口網站](https://ea.azure.com)的 [報表]**** 區段中，查看套用至註冊的所有信用額度或調整項目。

若要檢視信用額度：

1. 在 [Azure 企業版入口網站](https://ea.azure.com)中，選取 [報表]**** 階段。
1. 選取 [使用量摘要]****。
1. 在右上角，將 **M** 變更為 **C** 檢視。
1. 擴大 Azure 服務承諾用量表格中的調整欄位。
1. 您會看到套用至您註冊的信用額度和簡短說明。 例如：服務等級協定點數。

## <a name="pay-your-overage-with-your-monetary-commitment"></a>使用您的預付金支付超額金額

若要將您的預付金套用至超額部分，您必須符合下列準則：

- 您有已產生的超額費用但尚未付費，並且在計費服務結束日期的一年內。
- 您的可用預付金金額應涵蓋產生的總費用，包括所有過去未付款的 Azure 發票。
- 必須完全關閉您要完成的計費期限。 計費會在每個月的第五天之後完全關閉。
- 必須完全關閉您要抵銷的計費期限。
- Azure 承諾用量折扣 (ACD) 是實際的新承諾用量減去為先前用量規劃的任何資金。 這項需求只適用於產生的超額費用。 這僅適用於使用預付金的服務，因此無法套用至 Azure Marketplace 費用。 Azure Marketplace 費用會個別計費。

若要完成超額抵銷，您或帳戶小組可以開啟支援要求。 這需要您企業系統管理員或帳單連絡人的電子郵件核准。

## <a name="move-charges-to-another-enrollment"></a>將費用移至另一個註冊

只有在追溯轉移時，才需移動使用量資料。 有兩個選項可將使用量資料從一個註冊移至另一個註冊：

- 將帳戶從一個註冊轉移至另一個註冊
- 將註冊從一個註冊轉移至另一個註冊

無論是哪一個選項，您都必須向 EA 支援小組提交[支援要求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)，以取得協助。 

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

在位於相同資料中心內的服務之間，資料輸出不會產生任何費用。 例如，Microsoft 365 和 Azure。

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

## <a name="what-to-expect-after-change-of-channel-partner"></a>變更通道合作夥伴後的預期事項

如果通道合作夥伴 (COCP) 的變更發生在當月月中，客戶會收到上一個相關聯合作夥伴使用量的發票，以及另一張新合作夥伴的發票。

發票會在計費週期結束後的下個月發出。 如果計費頻率是每月，則 9 月份的發票將於 10 月發行出，這兩個合作夥伴皆是如此。 如果計費週期為每季或每年，客戶會收到先前相關聯之合作夥伴在其期間內使用量的發票，其他使用量將會根據計費幅度向新的合作夥伴收取費用。

## <a name="next-steps"></a>後續步驟

- 下列 Excel 檔案提供 Azure 服務的詳細資料，並會在每個月的 6 號和 20 號更新：

   | Title | 描述 | 檔案名稱 |
   | --- | --- | --- |
   | [易記服務名稱](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 列出所有作用中的服務，並包括： <br>  <ul><li>服務類別</li>   <li>易記服務名稱</li>   <li>承諾用量名稱和組件編號</li> <li>使用量名稱和組件編號</li>   <li>測量單位</li>   <li>報表中使用量與顯示的企業版入口網站使用量之間的轉換因素</li></ul> | Friendly\_Service\_Names.xlsx |
   | [服務下載欄位](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 此試算表會列出「使用量下載報表」中的服務相關欄位所有的可能組合。 | Service\_Download\_Fields.xlsx |

- 如需了解發票和費用的相關資訊，請參閱[了解您的 Azure Enterprise 合約帳單](../understand/review-enterprise-agreement-bill.md)。
- 若要開始使用 Azure 企業版入口網站，請參閱[開始使用 Azure EA 入口網站](ea-portal-get-started.md)。
