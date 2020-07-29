---
title: 使用 marketplace 計量服務的計量付費 |Azure Marketplace
description: 本檔是 Isv 以彈性計費模式發佈 SaaS 供應專案的指南。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d6c46575ed497c5067b3ffc5c745e79f814bc212
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87304372"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>使用 marketplace 計量服務的計量付費計費

透過 Marketplace 計量服務，您可以建立依非標準單位收費的軟體即服務（SaaS）供應專案。  發佈此供應專案之前，您必須定義計費維度，例如已處理的頻寬、票證或電子郵件。  然後，客戶會根據這些維度的耗用量付費，讓您的系統透過 Marketplace 計量服務 API，在計費事件發生時向 Microsoft 通知。  

## <a name="prerequisites-for-metered-billing"></a>計量付費計費的必要條件

若要讓 SaaS 供應專案使用計量付費，必須先：

* 如[建立 SaaS 供應](./create-new-saas-offer.md)專案中所述，符合[透過 Microsoft 供應專案銷售](./create-new-saas-offer.md#sell-through-microsoft)的所有供應專案需求。
* 與[SaaS 履行 api](./pc-saas-fulfillment-api-v2.md)整合，可讓客戶布建及連線到您的供應專案。  
* 針對您的服務向客戶收費時，針對一般**費率**定價模型進行設定。  維度是一般費率定價模型的選擇性延伸。 
* 與[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)整合，以通知 Microsoft 可計費事件。

然後，SaaS 供應專案可以與[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)整合，以通知 Microsoft 可計費事件。

>[!Note]
>Marketplace 計量服務僅適用于一般費率計費模型，不適用於每個使用者計費模式。

## <a name="how-metered-billing-fits-in-with-pricing"></a>計量付費如何搭配定價

在定義供應專案及其計價模式時，瞭解供應專案階層很重要。

* 每個 SaaS 供應專案都設定為透過 Microsoft 銷售。  發行供應專案之後，就無法變更此選項。
* 設定為透過 Microsoft 銷售的每個 SaaS 供應專案都可以有一或多個方案。  使用者訂閱了 SaaS 供應專案，但在計畫的內容中是透過 Microsoft 購買。
* 每個方案都有相關聯的計價模式： [一般**費率**] 或 [**每位使用者**]。 供應專案中的所有計劃都必須與相同的計價模式相關聯。 例如，不能有一個供應專案具有平面定價模型的計畫，另一個則是每個使用者的計價模式。
* 在針對一般費率計費模型所設定的每個方案內，至少會包含一個週期性費用（可以是 $0）：
    * **每**月週期性費用：當使用者購買方案時，以每月週期預先支付的固定月費。
    * 週期性的**每年**費用：當使用者購買方案時，以年度週期預先支付的一般年度費用。
* 除了週期性費用以外，一般費率方案也可以包含選擇性的自訂維度，用來向客戶收取未包含在非固定費率的超額使用量。  每個維度都代表一個可計費單位，您的服務將使用[Marketplace 計量服務 API](./marketplace-metering-service-apis.md)來與 Microsoft 通訊。

## <a name="sample-offer"></a>範例供應專案

例如，Contoso 是具有稱為 Contoso Notification Services （CN）之 SaaS 服務的發行者。 CN 允許其客戶透過電子郵件或文字傳送通知。 Contoso 會在合作夥伴中心註冊為發行者，以供商業 marketplace 方案將 SaaS 供應專案發佈至 Azure 客戶。  有兩個與 CN 相關聯的計畫，如下所述：

* 基本方案
    * 傳送每月 $ 0 的10000電子郵件和1000文字（按月支付費用）
    * 超過10000封電子郵件，每個100的電子郵件須支付 $1
    * 超過1000文字，針對每個文字支付 $0.02

    [![基本方案定價](./media/saas-basic-pricing.png "按一下以放大視圖")](./media/saas-basic-pricing.png)

* 進階方案
    * 每年傳送50000封電子郵件和10000的文字，每月 $ 350/月份或5M 的電子郵件和1M 筆 $3500 文字
    * 超過50000封電子郵件，每個100的電子郵件須支付 $0。5
    * 超過10000文字，針對每個文字支付 $0.01

    [![Premium 方案定價](./media/saas-premium-pricing.png "按一下以放大視圖")](./media/saas-premium-pricing.png)

* 企業方案
    * 傳送不限數量的電子郵件和50000文字，適用于 $ 400/月
    * 超過50000的文字每個 txt 的費用 $0.005

    [![企業方案定價](./media/saas-enterprise-pricing.png "按一下以放大視圖")](./media/saas-enterprise-pricing.png)

根據選取的方案，對 CNS SaaS 供應專案的 Azure 客戶購買訂用帳戶，將能夠傳送每個訂用帳戶（月或年）中包含的文字和電子郵件數量，如訂用帳戶詳細資料-開始和結束日期中所示。  Contoso 會將使用量計算為已包含的數量，而不會將任何使用方式事件傳送給 Microsoft。 當客戶取用超過所包含的數量時，就不需要變更方案或執行任何不同的動作。  Contoso 會測量超過所包含數量的超額部分，並開始向 Microsoft 發出使用事件，以使用[Marketplace 計量服務 API](./marketplace-metering-service-apis.md)對超額使用量收費。  Microsoft 接著會向客戶收取自訂維度中發行者所指定之超額使用量的費用。 超額計費是在下一個計費週期進行（每月，但可以是部分客戶的每季或更早）。  針對每月的一般費率方案，會在發生超額部分的每個月進行超額計費。  針對每年的一般費率方案，一旦取用每年所包含的數量，自訂計量所發出的所有額外使用量都會以每個計費週期（每月）的超額部分計費，直到訂用帳戶的年度期限結束為止。

## <a name="billing-dimensions"></a>計費維度

每個計費維度都會定義 ISV 可以發出使用事件的自訂單位。  計費維度也會用來與客戶溝通，以瞭解如何使用軟體來計費。  其定義如下：

* **識別碼**：發出使用事件時參考的不可變維度識別碼。
* **顯示名稱**：與維度相關聯的顯示名稱，例如「已傳送的文字訊息」。
* **測量單位**：計費單位的描述，例如「每一文字訊息」或「每100封電子郵件」。
* **每單位價格（美元**）：維度一個單位的價格。  可以是0。 
* 每月**數量包含于基底**：每月包含的維度數量，適用于支付每月週期性費用的客戶，必須是整數。 可以是0或無限制。
* **包含在基底中的年度數量**：每年所包含的維度數量，適用于支付週期性年度費用的客戶，必須是整數。 可以是0或無限制。

計費維度會在供應專案的所有方案之間共用。  某些屬性會套用至所有計劃的維度，而其他屬性則會套用至特定計劃。

定義維度本身的屬性會在供應專案的所有計劃之間共用。  發行供應專案之前，從任何計畫內容對這些屬性所做的變更，將會影響所有計劃的維度定義。  發行供應專案之後，這些屬性將無法再編輯。  這些屬性包括：

* 識別碼
* 顯示名稱
* 測量單位

維度的其他屬性是每個方案特有的，而且可以有不同的值從計畫到計畫。  發行方案之前，您可以編輯這些值，而且只會影響此計畫。  一旦您發行計畫之後，這些屬性將無法再編輯。  這些屬性包括：

* 每單位價格（美元）
* 包含在基底中的每月數量  
* BaseIncluded 中包含的年度數量  

維度也有兩個特殊概念：「已啟用」和「無限」。。

* [**已啟用**] 表示此計畫參與此維度。  如果您要建立的新方案不會根據此維度傳送使用狀況事件，您可能會想要將此選項保留為未核取。  此外，在計畫首次發行後新增的任何新維度，在已發行的計畫中都會顯示為「未啟用」。  已停用的維度不會顯示在客戶所見計畫的任何維度清單中。
* **無限**符號 "∞" 表示此計畫參與此維度，但不會針對此維度發出使用方式。  如果您想要向客戶指出此維度所代表的功能已包含在方案中，但沒有使用限制。  具有無限使用量的維度會顯示在客戶所見計畫的維度清單中，並指出它將不會產生此計畫的費用。

>[!Note] 
>已明確支援下列案例： <br> -您可以將新的維度加入至新的方案。  所有已發行的方案都不會啟用新的維度。 <br> -您可以發行不含任何維度的一般**費率**方案，然後加入新的方案並為該方案設定新的維度。 新的維度不會針對已發行的方案啟用。

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>為每個支援的市場設定每個單位的維度價格

如同一般費率定價，您可以根據支援的國家或地區來設定計費維度價格。 發行者必須使用合作夥伴中心的定價資料匯入和匯出功能。

1. 首先定義所需的維度，並標示支援的市場。 
1. 然後將此資料匯出至檔案。
1. 為每個國家/地區新增正確的價格，並在合作夥伴中心匯入檔案。

計量的 UI 將會變更，以反映維度的價格只能出現在檔案中。

[![Marketplace 計量服務維度](./media/metering-service-dimentions.png "按一下以放大視圖")](./media/metering-service-dimentions.png)


### <a name="private-plan"></a>私用計畫

就像一般費率方案，具有維度的方案可以設定為私用方案，而且只能由方案定義的物件存取。

## <a name="constraints"></a>條件約束

### <a name="trial-behavior"></a>試用版行為

使用 marketplace 計量服務的計量付費計費與提供免費試用版不相容。  您無法將計畫設定為同時使用計量付費計費和免費試用版。

### <a name="locking-behavior"></a>鎖定行為

因為搭配 Marketplace 計量服務使用的維度代表瞭解客戶將如何支付服務費用，所以在您發佈之後，就無法再編輯維度的所有詳細資料。  在發行之前，您必須先為計畫完整定義維度。

一旦供應專案與維度一併發行，就無法再變更該維度的供應專案層級詳細資料：

* 識別碼
* 顯示名稱
* 測量單位

方案發佈之後，就無法再變更計畫層級的詳細資料：

* 每單位價格（美元）
* 包含在基底中的每月數量
* 包含在基底中的年度數量
* 是否為計畫啟用維度

### <a name="upper-limits"></a>上限

可以為單一供應專案設定的維度數目上限為18個唯一維度。

## <a name="get-support"></a>取得支援

如果您有下列其中一個問題，可以開啟支援票證。

* Marketplace 計量服務 API 的技術問題。
* 因為您的端發生錯誤或錯誤，而需要呈報的問題（例如 錯誤的使用事件）。
* 與計量付費計費相關的任何其他問題。

請遵循[合作夥伴中心的商業 marketplace 方案支援](./support.md)中的指示，以瞭解發行者支援選項，並向 Microsoft 開放支援票證。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[Marketplace 計量服務 api](./marketplace-metering-service-apis.md) 。
