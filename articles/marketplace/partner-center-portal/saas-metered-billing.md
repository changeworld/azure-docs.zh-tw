---
title: 使用 marketplace 計量服務的計量付費 |Azure Marketplace
description: 本檔是 Isv 以彈性計費模式發佈 SaaS 供應專案的指南。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80281318"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>使用 marketplace 計量服務的計量付費計費

透過 Marketplace 計量服務，您可以在商業 Marketplace 方案中建立軟體即服務（SaaS）供應專案，並根據非標準單位收費。  發佈此供應專案之前，您必須定義計費維度，例如已處理的頻寬、票證或電子郵件。  然後，客戶會根據這些維度的耗用量付費，讓您的系統透過 Marketplace 計量服務 API，在計費事件發生時向 Microsoft 通知。  

## <a name="prerequisites-for-metered-billing"></a>計量付費計費的必要條件

為了讓 SaaS 供應專案使用計量付費計費，它必須：

* 如[建立 SaaS 供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)專案中所述，符合[透過 Microsoft 供應專案銷售](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft)的所有供應專案需求。
* 與[SaaS 履行 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)整合，可讓客戶布建及連線到您的供應專案。  
* 針對您的服務向客戶收費的一般**費率**定價模式設定。  維度是一般費率定價模型的選擇性延伸。 
* 與[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)整合，以通知 Microsoft 可計費事件。

>[!Note]
>Marketplace 計量服務僅適用于一般費率計費模型，不適用於每個使用者計費模式。

## <a name="how-metered-billing-fits-in-with-pricing"></a>計量付費如何搭配定價

當您定義供應專案及其計價模式時，請務必瞭解供應專案階層。

* 每個 SaaS 供應專案都設定為透過 Microsoft 銷售。  發行供應專案之後，即無法變更此設定。
* 設定為透過 Microsoft 銷售的每個 SaaS 供應專案都可以有一或多個方案。 使用者訂閱了 SaaS 供應專案，但在計畫的內容中是透過 Microsoft 購買。
* 每個方案都有相關聯的計價模式： [一般**費率**] 或 [**每位使用者**]。 供應專案中的所有計劃都必須與相同的計價模式相關聯。 例如，不能有其中一個方案為「一般費率」定價模型的供應專案，另一個則是「每個使用者」計價模式。
* 在針對一般費率計費模型所設定的每個方案內，至少會包含一個週期性費用（可以是 $0）：
    * **每**月週期性費用：當使用者購買方案時，以每月週期預先支付的固定月費。
    * 週期性**年**費：當使用者購買方案時，以年度週期預先支付的一般年度費用。
* 除了週期性費用以外，方案也可以包含選擇性的維度，用於向客戶收取未包含在非固定費率的使用量。   每個維度都代表一個可計費單位，您的服務將使用[Marketplace 計量服務 API](./marketplace-metering-service-apis.md)來與 Microsoft 通訊。

## <a name="sample-offer"></a>範例供應專案

例如，Contoso 是具有稱為 Contoso Notification Services （CN）之 SaaS 服務的發行者。 透過電子郵件或文字，CN 可以讓客戶傳送通知。 Contoso 會在合作夥伴中心註冊為「發行者」，以供商業 marketplace 方案發佈 Azure 客戶的供應專案。  有兩個與 CN 相關聯的計畫，如下所述：

* 基本方案
    * 傳送每月 $ 0 的10000電子郵件和1000文字
    * 超過10000封電子郵件，每個100的電子郵件須支付 $1
    * 超過1000文字，針對每個文字支付 $0.02
* 進階方案
    * 傳送50000電子郵件和10000文字，每月 $ 350
    * 超過50000封電子郵件，每個100的電子郵件須支付 $0。5
    * 超過10000文字，針對每個文字支付 $0.01

訂閱 CNS 服務的 Azure 客戶將能夠根據選取的方案，每月傳送包含的文字和電子郵件數量。  Contoso 會測量所包含數量的使用量，而不會將任何使用方式事件傳送給 Microsoft。  當客戶取用超過所包含的數量時，就不需要變更方案或執行任何不同的動作。  Contoso 會測量超過所包含數量的超額部分，並開始使用[Marketplace 計量服務 API](./marketplace-metering-service-apis.md)，向 Microsoft 發出使用事件以取得額外的使用量。  Microsoft 接著會向客戶收取發行者所指定的額外使用量。

## <a name="billing-dimensions"></a>計費維度

計費維度是用來與客戶溝通，以瞭解如何使用軟體來計費，同時也會向 Microsoft 傳達使用事件。 其定義如下：

* **維度識別碼**：發出使用事件時參考的不彈性識別碼。
* **維度名稱**：與維度相關聯的顯示名稱，例如「已傳送的文字訊息」。
* **測量單位**：計費單位的描述，例如「每一文字訊息」或「每100封電子郵件」。
* **每單位價格**：維度的一個單位的價格。  
* **每月的包含數量**：每月包含的維度數量，如果客戶支付每月重複的費用，必須是整數。
* **年度詞彙的已包含數量**：每月包含的維度數量，如果客戶支付週期性的年度費用，必須是整數。

計費維度會在供應專案的所有方案之間共用。  某些屬性會套用至所有計劃的維度，而其他屬性則會套用至特定計劃。

定義維度本身的屬性會在供應專案的所有計劃之間共用。  發行供應專案之前，從任何計畫內容對這些屬性所做的變更，將會影響所有計劃的維度定義。  發行供應專案之後，這些屬性將無法再編輯。  這些屬性包括：

* 識別碼
* Name
* 測量單位

維度的其他屬性是每個方案特有的，而且可以有不同的值從計畫到計畫。  發行方案之前，您可以編輯這些值，而且只會影響此計畫。  一旦您發行計畫之後，這些屬性將無法再編輯。  這些屬性包括：

* 每一單位價格
* 每月客戶的內含數量 
* 年度客戶的內含數量 

維度也有兩個特殊概念：「已啟用」和「無限」。。

* [**已啟用**] 表示此計畫參與此維度。  如果您要建立的新方案不會根據此維度傳送使用狀況事件，您可能會想要取消核取此選項。  此外，在計畫首次發行後新增的任何新維度，在已發行的計畫中都會顯示為「未啟用」。  已停用的維度不會顯示在客戶所見計畫的任何維度清單中。
* **無限**的（以無限大符號 "∞" 表示）表示此計畫參與此維度，但不會針對此維度進行使用量計量。  如果您想要向客戶指出此維度所代表的功能已包含在方案中，但沒有使用限制。  具有無限使用量的維度會顯示在客戶所見計畫的維度清單中，並指出它將不會產生此計畫的費用。

>[!Note] 
>已明確支援下列案例： <br> -您可以將新的維度加入至新的方案。  所有已發行的方案都不會啟用新的維度。 <br> -您可以發行不含任何維度的一般**費率**方案，然後加入新的方案並為該方案設定新的維度。 新的維度不會針對已發行的方案啟用。

## <a name="constraints"></a>條件約束

### <a name="trial-behavior"></a>試用版行為

使用 marketplace 計量服務的計量付費計費與提供免費試用版不相容。  您無法將計畫設定為同時使用計量付費計費和免費試用版。

### <a name="locking-behavior"></a>鎖定行為

因為搭配 Marketplace 計量服務使用的維度代表瞭解客戶將如何支付服務費用，所以在您發佈之後，就無法再編輯維度的所有詳細資料。  在發行之前，您必須先為計畫完整定義維度。
  
一旦供應專案與維度一併發行，就無法再變更該維度的供應專案層級詳細資料：

* 識別碼
* Name
* 測量單位

方案發佈之後，就無法再變更計畫層級的詳細資料：

* 每一單位價格
* 每月詞彙的內含數量
* 年度詞彙的內含數量
* 是否為計畫啟用維度

### <a name="upper-limits"></a>上限

可以為單一供應專案設定的維度數目上限為18個唯一維度。

## <a name="get-support"></a>取得支援

如果您有下列其中一項，您可以開啟支援票證。

* Marketplace 計量服務 API 的技術問題。
* 因為您的端發生錯誤或錯誤，而需要呈報的問題（例如 錯誤的使用事件）。
* 與計量付費計費相關的任何其他問題。 

請遵循下列步驟來提交您的支援票證：

1. 移至[支援連結](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。 系統會為您自動填入前幾個下拉式功能表。 如需 Marketplace 支援，請將產品系列識別為**雲端和線上服務**，此產品為**marketplace 發行者**。  請勿變更預先填入的下拉式功能表選取專案。
2. 在 [選取產品版本] 底下，選取 [ **Live 供應專案管理**]。
3. 在 [選取最能描述問題的類別] 下，選擇 [ **SaaS 應用程式**]。
4. 在 [選取最能描述問題的問題] 底下，選取 [**計量付費**]。
5. 選取 [**下一步]** 按鈕，系統會將您導向至 [**問題詳細資料**] 頁面，您可以在其中輸入有關問題的詳細資料。

如需更多發行者支援選項，請參閱[合作夥伴中心的商業 marketplace 方案支援](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[Marketplace 計量服務 api](./marketplace-metering-service-apis.md) 。
