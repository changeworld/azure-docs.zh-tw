---
title: 了解 Enterprise 合約的 Azure 保留使用量
description: 學習如何看懂使用量，以了解 Enterprise 註冊的 Azure 保留套用情形。
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 070d105785ee3ab9811188406bda56dbe6ceb558
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350886"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>取得 Enterprise 合約保留成本和使用量

保留成本和使用量資料適用於 Azure 入口網站和 REST API 中的 Enterprise 合約客戶。 本文可協助您：

- 取得保留購買資料
- 知道哪個訂用帳戶、資源群組或資源使用了保留
- 針對保留使用量進行退款
- 計算保留節省成本
- 取得保留的低使用量資料
- 攤銷保留成本

Marketplace 費用會合併在使用量資料中。 您可以從單一資料來源檢視第一方使用量、Marketplace 使用量和購買的費用。

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 使用量資料中的保留費用

資料分成兩個不同的資料集：「實際成本」  和「分攤成本」  。 這兩個資料集有何不同：

**實際成本** - 提供資料來調節您的每月帳單。 此資料具有保留購買成本和保留套用詳細資料。 透過這項資料，您可以知道哪個訂用帳戶或資源群組或資源在特定一天內收到保留折扣。 收到保留折扣之使用量的 EffectivePrice 為零。

**分攤成本** - 此資料集與實際成本資料集相似，不同之處在於取得保留折扣之使用量的 EffectivePrice 是按比例計算的保留成本 (而不為零)。 這可協助您知道訂用帳戶、資源群組或資源的保留使用量貨幣值，並可協助您在內部收取保留使用率的費用。 此資料集也有未使用的保留時數。 此資料集沒有保留購買記錄。

兩個資料集的比較：

| 資料 | 實際成本資料集 | 分攤成本資料集 |
| --- | --- | --- |
| 保留購買 | 可在此檢視中取得。<br><br>  若要取得此資料，請以 ChargeType = &quot;Purchase&quot; 篩選。 <br><br> 請參閱 ReservationID 或 ReservationName，以得知費用適用的保留。  | 不適用於此檢視。 <br><br> 分攤資料中不會提供購買成本。 |
| EffectivePrice | 對於取得保留折扣的使用量，此值為零。 | 此值為具有保留折扣之使用量的每小時依比例計算的保留成本。 |
| 未使用的保留 (提供一天內保留未使用的時數和浪費的貨幣值) | 不適用於此檢視。 | 可在此檢視中取得。<br><br> 若要取得此資料，請以 ChargeType = &quot;UnusedReservation&quot; 篩選。<br><br>  請參閱 ReservationID 或 ReservationName，以得知哪項保留未充分利用。 這就是當天浪費多少保留。  |
| UnitPrice (價位表中的資源價格) | 可用 | 可用 |

Azure 使用量資料中的其他可用資訊已變更：

- 產品和計量資訊 - Azure 不會如同先前一樣，以 ReservationId 和 ReservationName 取代原先取用的計量。
- ReservationId 和 ReservationName - 它們是自己在資料中的欄位。 先前，它只能在 AdditionalInfo 下取得。
- ProductOrderId - 保留訂單識別碼 (新增為其自己的欄位)。
- ProductOrderName - 所購買保留的產品名稱。
- 期限 - 12 個月或 36 個月。
- RINormalizationRatio - 可在 AdditionalInfo 下取得。 這是將保留套用至使用量記錄的比率。 如果已對您的保留啟用執行個體大小彈性，該功能即可套用至其他大小。 此值會顯示對使用量記錄套用的保留比率。

[請參閱欄位定義](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>使用 API 取得 Azure 使用量和保留使用量資料

您可以使用 API 來取得資料，或從 Azure 入口網站下載資料。

您可以呼叫[使用量詳細資料 API](/rest/api/consumption/usagedetails/list)來取得新資料。 如需術語的詳細資訊，請參閱[使用量詞彙](../understand/understand-usage.md)。 呼叫端應該是使用 [EA 入口網站](https://ea.azure.com)之 Enterprise 合約的企業系統管理員。 唯讀企業系統管理員也可以取得資料。

請注意，在[適用於 Enterprise 客戶的報告 API - 使用量詳細資料](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)中無法取得此資料。

以下是對使用量詳細資料 API 的呼叫範例：

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

如需有關 {enrollmentId} 和 {billingPeriodId} 的詳細資訊，請參閱[使用量詳細資料 – 清單](/rest/api/consumption/usagedetails/list) API 一文。

下表中關於計量和篩選的資訊有助於解決常見的保留問題。

| **API 資料的類型** | API 呼叫動作 |
| --- | --- |
| **所有費用 (使用量和購買)** | 以 ActualCost 取代 {metric} |
| **獲得保留折扣的使用量** | 以 ActualCost 取代 {metric}<br><br>將 {filter} 取代為：properties/reservationId%20ne%20 |
| **未取得保留折扣的使用量** | 以 ActualCost 取代 {metric}<br><br>將 {filter} 取代為：properties/reservationId%20eq%20 |
| **分攤費用 (使用量和購買)** | 以 AmortizedCost 取代 {metric} |
| **未使用的保留報表** | 以 AmortizedCost 取代 {metric}<br><br>將 {filter} 取代為：properties/ChargeType%20eq%20'UnusedReservation' |
| **保留購買** | 以 ActualCost 取代 {metric}<br><br>將 {filter} 取代為：properties/ChargeType%20eq%20'Purchase'  |
| **退費** | 以 ActualCost 取代 {metric}<br><br>將 {filter} 取代為：properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>下載含有新資料的使用量 CSV 檔案

如果您是 EA 管理員，您可以從 Azure 入口網站下載包含新使用量資料的 CSV 檔案。 這項資料無法從 EA 入口網站 (ea.azure.com) 取得，您必須從 Azure 入口網站 (portal.azure.com) 下載使用量檔案，才能看到新的資料。

在 Azure 入口網站中，瀏覽至[成本管理 + 帳單](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)。

1. 選取計費帳戶。
2. 按一下 [使用量 + 費用]  。
3. 按一下 [下載]  。  
![顯示如何在 Azure 入口網站中下載 CSV 使用量資料檔案的範例](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在 [下載使用量 +費用]  的 [使用量詳細資料第 2 版]  底下，選取 [所有費用 (使用量和購買)]  ，然後按 [下載]。 針對 [分攤費用 (使用量和購買)]  重複執行。

您下載的 CSV 檔案包含實際成本和分攤成本。

## <a name="common-cost-and-usage-tasks"></a>一般成本和使用量工作

下列各節是大部分人用來檢視其保留成本和使用量資料的一般工作。

### <a name="get-reservation-purchase-costs"></a>取得保留購買成本

在實際成本資料中可取得保留購買成本。 針對 _ChargeType = Purchase_ 篩選。 請參閱 ProductOrderID，以判斷購買的保留訂單。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>取得未充分利用的保留數量和成本

取得分攤成本資料，並對 _ChargeType_ _= UnusedReservation_ 進行篩選。 您可取得每日未使用的保留數量和成本。 您可以分別使用 _ReservationId_ 和 _ProductOrderId_ 欄位來篩選保留或保留訂單的資料。 如果保留已 100% 利用，則記錄的數量為 0。

### <a name="amortize-reservation-costs"></a>攤銷保留成本

取得分攤成本資料，並使用 _ProductOrderID_ 來篩選保留訂單，以取得保留的每日分攤成本。

### <a name="chargeback-for-a-reservation"></a>保留的退款

您可以依訂用帳戶、資源群組或標記，將保留使用退款給其他組織。 分攤成本資料提供下列資料類型的保留使用率貨幣值：

- 資源 (例如 VM)
- 資源群組
- Tags
- 訂用帳戶

### <a name="get-the-blended-rate-for-chargeback"></a>取得退款的混合費率

若要判斷混合費率，請取得分攤成本資料並彙整總成本。 針對 VM，您可以使用來自 AdditionalInfo JSON 資料的 MeterName 或 ServiceType 資訊。 將總成本除以用來取得混合費率的數量。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>稽核最佳保留使用以取得執行個體大小彈性

將數量乘以 AdditionalInfo 中的 _RINormalizationRatio_。 結果會指出有多少小時的保留使用套用至使用量記錄。

### <a name="determine-reservation-savings"></a>判斷保留節省成本

取得分攤成本資料並篩選保留執行個體的資料。 然後：

1. 取得預估的隨用隨付成本。 如果保留折扣並未套用至使用量，將 _UnitPrice_ 乘以 _Quantity_ 值，以取得預估的隨用隨付成本。
2. 取得保留成本。 將 _Cost_ 值加總，以取得您針對保留執行個體支付的貨幣值。 其中包含保留的已使用和未使用成本。
3. 從預估的隨用隨付成本中減去保留成本，以取得預估節省的成本。

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>成本分析中的保留購買和分攤

在[成本分析](https://aka.ms/costanalysis)中可取得保留成本。 根據預設，成本分析會顯示 [實際成本]  ，這就是您帳單上顯示成本的方式。 若要檢視已細分並與使用權益的資源相關聯的保留購買，請切換為 [分攤成本]  ：

![顯示在成本分析中何處選取分攤成本的範例](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

依費用類型分組，以查看使用量、購買和退費的細目；或依保留分組以取得保留和隨選成本的細目。 請記住，當您查看實際成本時，您會看到的唯一保留成本就是購買，但成本將會分攤給在查看分攤成本時使用權益的個別資源。 在查看分攤成本時，您也會看到新的 **UnusedReservation** 費用類型。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../../azure-sql/database/reserved-capacity-overview.md)
- [管理 Azure 保留項目](manage-reserved-vm-instance.md)
- [了解保留項目折扣的套用方式](../manage/understand-vm-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [Windows 軟體成本不包含在 Reservations 內](reserved-instance-windows-software-costs.md)