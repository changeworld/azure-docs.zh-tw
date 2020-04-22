---
title: 商業市場支付摘要 |Azure 應用商店
description: "\"付款\"摘要可顯示有關您通過優惠賺取的資金的詳細資訊。 也可以讓您了解何時會收到付款與付款金額。"
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730212"
---
# <a name="payout-reporting"></a>付款報告

[**"付款"摘要**](https://docs.microsoft.com/windows/uwp/publish/payout-summary)顯示您在 Microsoft 賺取的資金的詳細資訊。 也可以讓您了解何時會收到付款與付款金額。

如果在 Azure 應用商店中銷售產品,您還將在 **「付款摘要**」中看到有關成功付款的資訊。 有關 Azure 應用商店付款的詳細資訊,請參閱[Microsoft Azure 應用商店參與策略](https://go.microsoft.com/fwlink/p/?LinkId=722436)和[Microsoft Azure 應用商店發行者協定](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

> [!NOTE]
> 要有資格獲得付款,您的收益必須達到 50 美元的[付款閾值](payment-thresholds-methods-timeframes.md)。 有關付款閾值的詳細資訊,請參閱此頁面並查看[Microsoft Azure 應用商店發佈商協定](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

- [存取支出報告的角色和權限](#roles-and-permission-to-access-the-payout-report)
- [付款報告:雲合作夥伴門戶和合作夥伴中心之間的差異](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [客戶類型](#customer-types)
- [付款和使用之間的共同關係](#corelation-between-payout-and-usage)
- [記錄記錄下載](#transaction-history-download-export)
- [計費問題和支援](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>存取支出報告的角色和權限

| 報告/頁面    | 帳戶擁有者    | Manager  | 開發人員 | 商務貢獻者 |  財務貢獻者 | 行銷人員 |
|------------------|------------------|----------|-----------|----|----|-----|
| 採集報告(包括近即時資料) | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 | 可檢視 | 沒有存取權 |
| 意見反應報告/回應 | 可檢視和傳送意見反應 | 可檢視和傳送意見反應 | 可檢視和傳送意見反應 | 沒有存取權 | 沒有存取權 | 可檢視和傳送意見反應 |
| 健康報告(包括近實時資料) | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 |
| 使用方式報告 | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 |
| 支付帳戶 | 可更新 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可更新 | 沒有存取權 |
| 稅金設定檔 | 可更新 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可更新 | 沒有存取權 |
| 支出摘要 | 可檢視 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可檢視 | 沒有存取權 |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>付款報告:雲合作夥伴門戶和合作夥伴中心之間的差異

| | 雲端合作夥伴入口網站 | 合作夥伴中心 |
|---------|---------|---------|
| 連結 | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)和[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| 導覽 | 在見解支付中提供的付款報告 | 合作夥伴中心提供的付款報告 – 付款圖示 |
| 影響範圍 | <ul> <li>每行交易記錄可見,用於正在進行的收集、收集和支付 </li> <li>報告 = 在創建採購訂單后顯示所有行專案,包括正在進行的收集和正在進行的計費,以及尚未有資格付款的集合狀態和行專案。 </li> </ul> | <ul> <li>一旦專案被視為合格收入,則顯示它們。</li> <li>客戶先向微軟付款,然後 ISV 可以看到開始付款報告。</li> <li>付款報告不會顯示正在進行的集合和正在進行的計費。  </li> </ul>  |
| 交易未準備好付款 | 正在進行的計費 | 下一個估計付款:付款狀態處於未處理狀態。  |
| 付款狀態 |  | 未處理: <br> 收入有資格獲得付款。 它在激勵計劃計劃指南中定義的冷卻期間保持此狀態。 <br> <br> 即將 到來: <br> 付款單生成的待定內部審核,然後處理付款。 <br> <br> 寄件日期： <br> 付款已發送到您的銀行。 |

## <a name="customer-types"></a>客戶類型

### <a name="enterprise-agreement"></a>企業協定

系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

### <a name="credit-cards-and-monthly-invoice"></a>信用卡和每月發票

客戶也可以使用信用卡和每月發票來付款。 在這種情況下,您的軟體許可費將按月計費。

### <a name="csp-and-direct-pay-users"></a>CSP 和直接付費使用者

例如,如果客戶使用信用卡購買。

## <a name="corelation-between-payout-and-usage"></a>付款和使用之間的共同關係

|描述    |    Date  | 訂單/使用方式  | 支付 |
|----------|----------|-----------|-------------|
|訂單期間   | 8 月 15, 2019 - 8 月 30, 2019 | **關聯屬性訂單** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用量** <br> <ul> <li>CustomerId </li> <li>客戶名稱</li> <li>(使用參考)採購記錄Id/行項目Id</li> <li> 估計延長費用 <br> 預估付款 (PC) </li> </ul> |  |
|期間結束 (月)   | 8 月 30, 2019 | | |
|計費日期 | 2019年9月1日 | | |
|客戶付款日期 | 2019年9月1日 | | |
|委付期間 (僅信用卡，30 天) | 九月 1, 2019 - 九月 30, 2019 | | **關聯屬性訂單:** <br> <ul><li>資產 Id</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **使用量** <br> <ul> <li>資產 Id</li> <li>CustomerId</li> <li>客戶名稱</li> <li>OrderId</li> <li>行項目Id</li> <li>交易金額</li> <li>收入金額 最後支付貨幣</li> </ul> <br> **付款狀態:** 未處理 |
|收帳期間開始 | 2019年9月1日 | | |
|收帳期間結束 (最大值，30 天) | 2019年9月30日 | | |
|付款計算日期 (每月第 15 日) | 2019年10月1日 | | **相關屬性** <br> <ul><li>資產 Id</li> <li>客戶識別碼</li> <li>客戶名稱</li> </ul> <br> **使用量** <br> <ul> <li>資產 Id</li> <li>CustomerId</li> <li>客戶名稱</li> <li>OrderId</li> <li>行項目Id</li> <li>交易金額</li> <li>收入金額 最後支付貨幣</li> </ul> <br> **付款狀態:** 即將 到來 |
|付款日期 | 2019年10月15日 | | **相關屬性** <br> <ul><li>資產 Id</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **使用量** <br> <ul> <li>資產 Id</li> <li>CustomerId</li> <li>客戶名稱</li> <li>OrderId</li> <li>行項目Id</li> <li>交易金額</li> <li>收入金額 最後支付貨幣</li> </ul> <br> **付款狀態:** 已送出付款 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>企業協定(季度/月度客戶)

| 描述 |    Date  | 使用量 | 支付 |
|----------|----------|---------|-----------|
|訂單期間 | 8 月 15, 2019 - 8 月 30, 2019 | **關聯屬性訂單** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用情況報告** <br> <ul> <li>CustomerId </li> <li>客戶名稱</li> <li>(使用參考)採購記錄Id/行項目Id</li> <li> 估計延長費用 <br> 預估付款 (PC) </li> </ul> | |
|期間結束 (季) | 2019年9月30日 | | |
|計費日期 | 2019年10月15日 | | |
|委付期間 (僅信用卡，30 天) | n/a | | |
|收帳期間開始 | 2019年10月15日 | | |
|僅限信用卡,30 天 | 2019年11月1日 - 2019年11月30日 | | |
|收帳期間結束 (最大值，90 天) | 2020年1月15日 | | |
|客戶付款日期 | 2019年12月30日 | | |
|付款計算 | 2020年1月15日 | | |
|付款日期 | 2 月 15, 2020 | | **對於基於季度的客戶** <br> <br> **訂單報告** <br> <ul><li>資產 Id</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **使用量** <br> <ul> <li>資產 Id</li> <li>CustomerId</li> <li>客戶名稱</li> <li>OrderId</li> <li>行項目Id</li> <li>交易金額</li> <li>收入金額 最後支付貨幣</li> </ul> <br> **付款狀態:** 已送出 |

## <a name="transaction-history-download-export"></a>交易歷程記錄下載匯出

此選項提供您在交易記錄頁中看到的每個收益行專案的下載、收入類型、日期、關聯交易金額、客戶、產品和其他適用於獎勵計劃的交易詳細資訊。

| 資料行名稱     | 描述    |
|-------------|-------------------------------|
| 盈利代碼                      | 每個收入的唯一識別碼                                                                                                       |
| 參與者Id                  | 根據計劃賺取的合作夥伴的主要身份                                                                            |
| 參與者 IdType              | 主要用於激勵計劃和商店計劃和 Azure 應用商店的賣家 IF 的程序 ID                                          |
| 參與者名稱                | 收入合作夥伴的姓名                                                                                                              |
| 合作夥伴國家代碼             | 賺取合作夥伴的位置/國家/地區                                                                                                  |
| 程式名稱                    | 獎勵/商店計劃名稱                                                                                                             |
| transactionId                  | 交易的唯一識別碼                                                                                                    |
| 交易貨幣            | 發生原始客戶交易記錄的貨幣(不是合作夥伴位置貨幣)                                     |
| 交易日期                | 交易記錄的日期。 適用於許多交易有助於一次收益的程式                                           |
| 交易匯率        | 用於顯示相應交易美元的匯率                                                                 |
| 交易金額              | 建基於生成賺取的原始交易幣種的交易金額                                              |
| 交易金額           | 以美元為單位的交易金額                                                                                                                |
| 槓桿                          | 指示收益的業務規則                                                                                                  |
| 賺取率                    | 對交易金額施加的激勵率以產生收益                                                                      |
| quantity                       | 因計劃而異。 指示交易程式的計費數量                                                            |
| 數量類型                   | 指示數量類型,例如:計費數量、MAU                                                                                     |
| 收入類型                    | 指示是否收費、返利、合作、銷售等。                                                                                          |
| 收入金額                  | 以原始交易貨幣賺取金額                                                                                      |
| 賺取金額               | 以美元計價的賺取金額                                                                                                                    |
| 賺取日期                    | 賺取日期                                                                                                                      |
| 計算日期                | 在系統中計算收入的日期                                                                                            |
| 賺取匯率            | 為顯示相應美元的匯率                                                                                  |
| exchangeRateDate               | 用於計算賺取金額美元的匯率日期                                                                                   |
| 支付金額WO稅             | 僅「已發送」付款的賺取金額(不含稅款) 以付款到貨幣                                                                 |
| 付款貨幣                | 支付合作夥伴在付款配置檔中選擇的貨幣。 只顯示已送出付款                                                   |
| 付款匯率            | 匯率用於計算使用匯率日期以支付貨幣計算付款金額                                            |
| 付款識別碼            | 付款的唯一標識碼。 此號碼在銀行對帳單中可見                                            |
| 付款狀態            | [付款狀態]                                            |
| 付款狀態描述            | 付款狀態的友好描述                                            |
| customerId                     | 將永遠為空                                                                                                                     |
| customerName                   | 將永遠為空                                                                                                                     |
| partNumber                     | 將永遠為空                                                                                                                     |
| productName                    | 連結到交易記錄的產品名稱                                                                                                       |
| productId                      | 唯一產品識別碼                                                                                                                |
| parentProductId                | 唯一的父系產品識別碼。 注意:如果交易記錄沒有父產品,則父產品 ID = 產品 ID。 |
| 父產品名稱              | 父系產品的名稱。 注意:如果事務沒有父產品,則父產品名稱 = 產品名稱。   |
| productType                    | 產品類型 (例如應用程式、附加元件、遊戲等等)                                                                                        |
| 發票編號                  | 發票編號(僅適用於 EA)                                                                                                  |
| 轉銷商Id                     | 轉銷商識別碼                                                                                                                      |
| 轉銷商名稱                   | 轉售商名稱                                                                                                                            |
| 交易類型                | 交易類型 (例如購買、退貨、作廢、退款等等)                                                               |
| 本地供應商賣者            | 本地供應商/記錄銷售商                                                                                                          |
| 稅務匯出                    | 免稅金額 (銷售、使用或 VAT/GST 稅額)。                                                                                   |
| 稅務分類模型                  | 負責代繳稅額之一方 (銷售、使用或 VAT/GST 稅額)。                                                                    |
| 商店費                       | Microsoft 保留的金額,作為在應用商店中提供應用或載入項的費用。                                            |
| 交易付款條件       | 交易使用的客戶付款方式 (例如，卡片、行動裝置電信業者帳單、PayPal 等)。                                |
| 特潘                           | 指示第三方廣告網路                                                                                                     |
| 客戶國家                | 客戶國家/地區                                                                                                                         |
| 客戶城市                   | 客戶城市                                                                                                                            |
| 客戶國                  | 客戶狀態                                                                                                                           |
| 客戶Zip                    | 客戶郵遞區編碼                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| 外部參考 Id            | 程式的唯一識別碼                                                                                                        |
| 外部參考IdLabel       | 唯一識別子標籤                                                                                                                  |
| 交易國家代碼       | 發生交易的國家/地區代碼                                                                                                                  |
| 稅務國家       | 銷售給客戶國家/地區                                                                                                                  |
| 稅務國家       | 已銷售到客戶狀態                                                                                                                  |
| 稅務城       | 出售給客戶城市                                                                                                                  |
| 稅務代碼       | 出售給客戶 Zip                                                                                                                  |
| 授權計劃名稱       |                                                                                                                   |
| 程式代碼       | 使用程式名稱映射的字串                                                                                                                   |
| 賺取金額 最後支付貨幣       | 以最後付款貨幣賺取金額(如果沒有支付預付款,欄位將為空)                                                                                                                   |
| 最後支付貨幣       | 上次付款貨幣(欄位將為空,如果未支付預付款)                                                                                                                   |
| 資產 Id       | 市場服務的客戶訂單的唯一標識符。  它表示已交易的採購行物料。 可以有多個資產。                                                                                                                   |
| OrderId       | 與客戶的發票有關                                                                                                                   |
| 行項目Id       | 客戶發票中的單列                                                                                                                   |
| 客戶國家/地區       | 客戶提供的國家/地區名稱。  這可能與客戶 Azure 訂閱中的國家/地區不同。                                                                                                                   |
| 客戶電子郵件地址       | 最終客戶提供的電子郵寄地址。  這可能與客戶 Azure 訂閱中的電子郵件位址不同。                                                                                                                   |
| SkuId       | 發佈期間定義的 SKU ID。 供應項目可以有多個 SKU，但 SKU 只能與單一供應項目相關聯。                                                                                                                   |

>[!Note]
>交易發佈選項的所有報告和見解均可通過合作夥伴中心的雲合作夥伴門戶或分析部分的「見解」部分獲得。

## <a name="billing-questions-and-support"></a>計費問題和支援

要取得有關計費問題,請聯絡[商業市場發佈商支援](https://partner.microsoft.com/support/v2/?stage=1)。
