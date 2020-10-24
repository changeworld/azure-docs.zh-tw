---
title: Azure Cosmos DB 中的保留容量，以將成本優化
description: 了解如何購買 Azure Cosmos DB 保留容量，以節省計算費用。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 90717f329cc366dbb951b24da0ffc1b65881a865
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487512"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中獲得最低的保留容量費用

Azure Cosmos DB 保留容量可協助您透過保留一年或三年的 Azure Cosmos DB 資源來節省成本。 您可以利用 Azure Cosmos DB 保留容量來取得針對 Cosmos DB 資源佈建的輸送量折扣。 資源範例是資料庫和容器 (資料表、集合及圖表)。

Azure Cosmos DB 保留容量可以大幅降低 Cosmos DB 成本&mdash;預付一或三年期承諾用量費用，即可節省高達一般價格的 65%。 保留容量提供帳單折扣，而且不會影響 Azure Cosmos DB 資源的執行階段狀態。

Azure Cosmos DB 保留容量可涵蓋針對資源所佈建的輸送量。 它未涵蓋儲存體和網路費用。 購買保留容量後，符合保留屬性的輸送量費用就不會再按照預付型方案的費率來收費。 如需有關保留的詳細資訊，請參閱 [Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)一文。

您可以從 [Azure 入口網站](https://portal.azure.com)購買 Azure Cosmos DB 保留容量。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)。 若要購買保留容量：

* 您必須是至少一個具有隨用隨付費率的企業或個別訂用帳戶的擁有者角色。  
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代表或銷售代表可以購買 Azure Cosmos DB 保留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>購買之前，請決定所需的輸送量

所購買的保留容量大小應以現有或即將部署的 Azure Cosmos DB 資源在每小時會使用的總輸送量為基礎。 例如：購買 30000 RU/秒保留容量（如果這是您一致的每小時使用量模式）。 在此範例中，任何超過 30000 RU/秒的布建輸送量都會使用您的隨用隨付費率計費。 如果布建的輸送量低於每秒 30000 RU/秒，則該小時的額外保留容量將會浪費。

我們會根據您的每小時使用量模式來計算購買建議。 過去7、30和60天的使用量會進行分析，並保留容量購買，建議您節省最大的成本。 您可以使用下列步驟，在 Azure 入口網站中查看建議的保留大小：

1. 登入 [Azure 入口網站](https://portal.azure.com)。  

2. 選取 [**所有服務**  >  **保留**]  >  ** **。

3. 從 [ **購買保留** ] 窗格中，選擇 [ **Azure Cosmos DB**]。

4. 選取 [ **建議** ] 索引標籤以查看建議的保留：

您可以依照下列屬性來篩選建議：

-  (1 年或3年的**詞彙**) 
- 每月或前) 的**計費頻率** (
- **輸送量類型** (RU/秒與多重區域寫入 ru/秒) 

此外，您可以將建議範圍限定在單一資源群組、單一訂用帳戶或整個 Azure 註冊中。 

以下是範例建議：

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="保留容量建議":::

購買 30000 RU/秒保留的這項建議表示，在3年的保留期間，30000 RU/秒的保留大小將可省下最多費用。 在此情況下，會根據過去30天的 Azure Cosmos DB 使用量來計算建議。 如果客戶預期過去30天的 Azure Cosmos DB 使用量代表未來使用，則購買 30000 RU/秒的保留可讓您省下最多費用。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>購買 Azure Cosmos DB 保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。  

2. 選取 [**所有服務**  >  **保留**]  >  ** **。  

3. 從 [ **購買保留** ] 窗格中，選擇 [ **Azure Cosmos DB** 購買新的保留。  

4. 請填寫必填欄位，如下表中所述：

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="保留容量建議":::

   |欄位  |說明  |
   |---------|---------|
   |影響範圍   |   控制多少訂用帳戶可以使用與保留容量相關聯的計費權益選項。 它也會控制保留容量套用至特定訂用帳戶的方式。 <br/><br/>  如果您選取 [共用]****，保留容量折扣會套用至計費內容內任何訂用帳戶中執行的 Azure Cosmos DB 執行個體。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是由帳戶管理員所建立的所有個別訂用帳戶與隨用隨付費率。  <br/><br/>  如果您選取 [單一訂用帳戶]****，保留容量折扣會套用至所選訂用帳戶中的 Azure Cosmos DB 執行個體。 <br/><br/> 如果您選取 [ **單一資源群組**]，保留折扣會套用至所選訂用帳戶中的 Azure Cosmos DB 實例，以及該訂用帳戶內選取的資源群組。 <br/><br/> 您可以在購買保留容量之後變更保留範圍。  |
   |訂用帳戶  |   用來支付 Azure Cosmos DB 保留容量費用的訂用帳戶。 所選訂用帳戶上的付款方法會用來收費成本。 訂用帳戶必須是下列其中一種類型： <br/><br/>  Enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) ：針對 Enterprise 訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或以超額部分收費。 <br/><br/> 採用隨用隨付費率的個別訂用帳戶 (供應專案號碼： MS-AZR-0003P-Ms-azr-0003p 或 MS-AZR-0003P-Ms-azr-0023p) ：對於採用隨用隨付費率的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。    |
   | 資源群組 | 套用保留容量折扣的資源群組。 |
   |詞彙  |   一年或三年。   |
   |輸送量類型   |  輸送量會以要求單位的形式布建。 您可以針對布建的輸送量（單一區域寫入以及多個區域寫入）購買保留。 輸送量類型具有兩個可供選擇的值：每小時 100 RU/秒和100多區域寫入每小時 RU/秒。|
   | 保留容量單位| 您想要保留的輸送量數量。 您可以藉由判斷每個區域所有 Cosmos DB 資源 (例如資料庫或容器) 所需的輸送量來計算此值。 然後將它乘以您將與 Cosmos 資料庫建立關聯的區域數目。 例如：如果您有五個區域，其中每個區域每秒為 1 百萬個要求單位，則購買保留容量時，請選擇每秒 5 百萬個要求單位。 |


5. 填滿表單之後，就會計算購買保留容量所需的價格。 輸出也會顯示您選擇的選項所取得的折扣百分比。 接下來按一下 [**選取**]

6. 在 [ **購買保留** ] 窗格中，檢查保留的折扣和價格。 此保留容量價格適用於其輸送量針對所有區域而佈建的 Azure Cosmos DB 資源。  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="保留容量建議":::

7. 選取 [ **審核 + 購買** ]，然後 **立即購買**。 購買成功時，您會看到下列頁面：

購買保留容量之後，會立即套用至符合保留容量條件的任何現有 Azure Cosmos DB 資源。 如果您沒有任何現有的 Azure Cosmos DB 資源，保留容量將會在您部署符合保留容量條件的新 Cosmos DB 執行個體時套用。 在這兩種情況下，保留容量的期間都會在成功購買後立即生效。

當您的保留容量到期時，Azure Cosmos DB 執行個體將會繼續執行，而且會以一般隨用隨付費率計費。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>後續步驟

保留容量折扣會自動套用到符合保留容量範圍和屬性的 Azure Cosmos DB 資源中。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  若要了解如何將保留容量折扣套用至 Azure Cosmos DB，請參閱[了解 Azure 保留折扣](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)。

* 若要深入了解 Azure 保留，請參閱下列文章：

   * [什麼是 Azure 保留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [管理 Azure 保留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [了解隨用隨付訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [合作夥伴中心 CSP 計劃中的 Azure 保留容量](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。