---
title: Azure 宇宙 DB 中的預留容量以優化成本
description: 了解如何購買 Azure Cosmos DB 保留容量，以節省計算費用。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505962"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中獲得最低的保留容量費用

Azure Cosmos DB 保留容量可協助您透過保留一年或三年的 Azure Cosmos DB 資源來節省成本。 您可以利用 Azure Cosmos DB 保留容量來取得針對 Cosmos DB 資源佈建的輸送量折扣。 資源範例是資料庫和容器 (資料表、集合及圖表)。

Azure Cosmos DB 保留容量可以大幅降低 Cosmos DB 成本&mdash;預付一或三年期承諾用量費用，即可節省高達一般價格的 65%。 保留容量提供帳單折扣，而且不會影響 Azure Cosmos DB 資源的執行階段狀態。

Azure Cosmos DB 保留容量可涵蓋針對資源所佈建的輸送量。 它未涵蓋儲存體和網路費用。 購買保留容量後，符合保留屬性的輸送量費用就不會再按照預付型方案的費率來收費。 如需有關保留的詳細資訊，請參閱 [Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)一文。

您可以從 [Azure 入口網站](https://portal.azure.com)購買 Azure Cosmos DB 保留容量。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)。 若要購買保留容量：

* 您必須在至少一個企業或個人訂閱中處於"擁有者"角色，該訂閱具有即用即付費率。  
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代表或銷售代表可以購買 Azure Cosmos DB 保留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>購買之前，請決定所需的輸送量

預留容量購買的大小應基於現有或即將部署的 Azure Cosmos DB 資源每小時使用的輸送量總量。 例如：如果這是您一致的每小時使用模式，則購買 30，000 RU/s 預留容量。 在此示例中，任何超過 30，000 RU/s 的預配輸送量都將使用即用即付費率計費。 如果預配輸送量在一小時內低於 30，000 RU/s，則該小時的額外預留容量將浪費。

我們根據您的每小時使用模式計算購買建議。 分析了過去 7 天、30 天和 60 天的使用方式，建議購買可最大化您的節省的預留容量。 您可以使用以下步驟在 Azure 門戶中查看建議的預留大小：

1. 登錄到 Azure[門戶](https://portal.azure.com)。  

2. 選擇 **"所有服務** > **預訂添加** > **Add**"。

3. 在 **"購買預留"** 窗格中，選擇**Azure 宇宙資料庫**。

4. 選擇 **"推薦**"選項卡以查看建議的預訂：

您可以按以下屬性篩選建議：

- **期限**（1年或3年）
- **計費頻率**（每月或預付）
- **輸送量類型**（RU 與多主機 RU）

此外，您可以將建議範圍限定為單個資源組、單個訂閱或整個 Azure 註冊中。 

下面是一個示例建議：

![預留容量建議](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

購買 30，000 RU/s 預訂的建議表明，在 3 年預訂中，30，000 RU/s 的預訂規模將最大限度地節省。 在這種情況下，建議是根據過去 30 天的 Azure Cosmos DB 使用方式計算的。 如果此客戶希望過去 30 天的 Azure Cosmos DB 使用方式代表未來的使用，則通過購買 30，000 RU/s 預留，他們可最大限度地節省成本。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>購買 Azure Cosmos DB 保留容量

1. 登錄到 Azure[門戶](https://portal.azure.com)。  

2. 選擇 **"所有服務** > **預訂添加** > **Add**"。  

3. 在 **"購買預留"** 窗格中，選擇**Azure 宇宙 DB**以購買新預留。  

4. 請填寫必填欄位，如下表中所述：

   ![填寫保留容量表單](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |欄位  |描述  |
   |---------|---------|
   |影響範圍   |   控制多少訂用帳戶可以使用與保留容量相關聯的計費權益選項。 它也會控制保留容量套用至特定訂用帳戶的方式。 <br/><br/>  如果您選取 [共用]****，保留容量折扣會套用至計費內容內任何訂用帳戶中執行的 Azure Cosmos DB 執行個體。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 對於即用即付客戶，共用範圍是所有由帳戶管理員創建的按即用即付費率的單個訂閱。  <br/><br/>  如果您選取 [單一訂用帳戶]****，保留容量折扣會套用至所選訂用帳戶中的 Azure Cosmos DB 執行個體。 <br/><br/> 如果選擇 **"單個資源組**"，則預留折扣將應用於所選訂閱中的 Azure Cosmos DB 實例以及該訂閱中的選定資源組。 <br/><br/> 您可以在購買保留容量之後變更保留範圍。  |
   |訂用帳戶  |   用來支付 Azure Cosmos DB 保留容量費用的訂用帳戶。 所選訂閱的付款條件用於收取費用。 訂閱必須是以下類型之一： <br/><br/>  企業協定（優惠編號：MS-AZR-0017P 或 MS-AZR-0148P）：對於企業訂閱，費用從註冊的貨幣承諾餘額中扣除，或按超額收取。 <br/><br/> 具有即用即付費率的個人訂閱（優惠編號：MS-AZR-0003P 或 MS-AZR-0023P）：對於具有即用即付費率的個人訂閱，費用由訂閱上的信用卡或發票付款條件計費。    |
   | 資源群組 | 應用預留容量折扣的資源組。 |
   |詞彙  |   一年或三年。   |
   |輸送量類型   |  輸送量預配為請求單位。 您可以為兩個設置購買預配輸送量的預留 - 單個區域寫入以及多個區域寫入。 輸送量類型有兩個值可供選擇：每小時 100 RU/s 和每小時 100 個多主機 RU/s。|
   | 預留容量單位| 您想要保留的輸送量數量。 您可以藉由判斷每個區域所有 Cosmos DB 資源 (例如資料庫或容器) 所需的輸送量來計算此值。 然後，將其乘以您將與 Cosmos 資料庫關聯的區域數。 例如：如果您有五個區域，其中每個區域每秒為 1 百萬個要求單位，則購買保留容量時，請選擇每秒 5 百萬個要求單位。 |


5. 填寫表單後，計算購買預留容量所需的價格。 輸出還顯示所選選項的折扣百分比。 下一步按一下**選擇**

6. 在 **"購買預訂"** 窗格中，查看預訂的折扣和價格。 此保留容量價格適用於其輸送量針對所有區域而佈建的 Azure Cosmos DB 資源。  

   ![預留容量摘要](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. 選擇 **"審核 + 立即購買****"，然後立即購買**。 購買成功時，您會看到下列頁面：

購買保留容量之後，會立即套用至符合保留容量條件的任何現有 Azure Cosmos DB 資源。 如果您沒有任何現有的 Azure Cosmos DB 資源，保留容量將會在您部署符合保留容量條件的新 Cosmos DB 執行個體時套用。 在這兩種情況下，保留容量的期間都會在成功購買後立即生效。

當您的保留容量到期時，Azure Cosmos DB 執行個體將會繼續執行，而且會以一般隨用隨付費率計費。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>後續步驟

保留容量折扣會自動套用到符合保留容量範圍和屬性的 Azure Cosmos DB 資源中。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  若要了解如何將保留容量折扣套用至 Azure Cosmos DB，請參閱[了解 Azure 保留折扣](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)。

* 若要深入了解 Azure 保留項目，請參閱下列文章：

   * [什麼是 Azure 預留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [管理 Azure 保留項目](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [了解隨用隨付訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [合作夥伴中心 CSP 計劃中的 Azure 保留容量](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
