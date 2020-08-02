---
title: 決定您的發佈選項 - Microsoft 商業市集
description: 本文說明將供應項目發佈至 Microsoft AppSource 和 Azure Marketplace 的資格條件和需求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498067"
---
# <a name="determine-your-publishing-option"></a>決定您的發佈選項

您為供應項目選擇的發佈選項，與資格需求和商業市集 GTM 權益直接相關。 更重要的是，發佈選項和供應項目類型的選取項目，會定義使用者與您的商業市集供應項目互動的方式。

若要設定您的供應項目，您必須了解下列主要商業市集概念：發佈選項、供應項目類型和組態及呼籲行動，這些項目控制您的供應項目在商業市集店面展示的方式和位置。

在本文中，您將了解：

- 如何為解決方案決定適當的店面
- 每個店面中有哪些發佈選項和呼籲行動可用
- 每個發佈選項有哪些供應項目類型可用

## <a name="commercial-marketplace-publishing-options"></a>商業市集發佈選項

下表顯示供應項目類型在 Microsoft AppSource 和 Azure Marketplace 中的發佈選項。

|   | **清單 (連絡人)**  | **清單 (試用版)**  | **免費** | **BYOL** | **交易**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **虛擬機器** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure 應用程式 (多個 VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **容器映像** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge 模組** |  |  | Azure Marketplace | Azure Marketplace |   |
| **受控服務** |  |  |  | Azure Marketplace |   |
| **諮詢服務** | 兩個店面 |  |  |  |   |
| **SaaS 應用程式** | 兩個店面 | 兩個店面 | 兩個店面 |  | 兩個店面* |
| **Microsoft 365 應用程式** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 增益集** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42;Microsoft AppSource 中的 SaaS 應用程式交易供應項目目前只能使用信用卡。

&#42;&#42;Microsoft 365 供應項目可免費安裝，並可透過 SaaS 供應項目作為授權服務來販售。 如需詳細資訊，請參閱[透過 Microsoft 商業市集販售 Office 365 增益集](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)。

## <a name="choose-a-call-to-action"></a>選擇動作的呼叫

可用的發佈選項會提供差異化客戶參與，同時給予您潛在客戶共用和[商業市集權益](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)的存取權。 請注意與發佈選項對應的呼籲行動：

| **發行選項**    | **說明**  |
| :------------------- | :-------------------|
| **清單** | 應用程式或服務的簡易清單，可讓商業市集使用者向您要求透過**與我連絡**呼籲行動，與客戶聯繫。 |
| **試用版** | 使用商業市集來增強曝光度，並且將佈建解決方案的試用體驗自動化，讓潛在使用者在購買之前，在一段有限的時間內免費使用您的 SaaS、IaaS 或 Microsoft 應用程式內體驗。 用於試用發佈選項的呼籲行動有：**免費試用**或**試用產品**。 |
| **BYOL** | 使用商業市集增強解決方案的能見度和自動佈建能力，並個別完成財務交易。 BYOL 供應項目類型適用於內部部署至雲端的移轉。 呼籲行動是**立即取得**。
| **交易** | 交易供應項目會透過商業市集銷售。 Microsoft 負責計費和收費。 呼籲行動是**立即取得**。|

> [!Note]
> 當使用交易發佈選項時，請務必了解定價、計費、發票處理和付款考量，然後再選取供應項目類型與建立供應項目。 若要深入了解，請參閱[商業市集交易功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)一文。

## <a name="selecting-a-storefront"></a>選取店面

每個店面分別可滿足不同的客戶需求，並以特定對象為目標。 您的供應專案類型、交易功能和類別將會決定您的供應專案發佈位置。 類別和子類別會根據目標物件對應至每個店面：

**Microsoft AppSource**以商務使用者為目標，尋找企業營運或產業解決方案，以及 Dynamics 365、Microsoft 365 和 Power 平臺的諮詢服務。

**Azure Marketplace**的目標是 IT 專業人員和開發人員，尋找在 azure 上建立的解決方案，以及可加速使用 azure 的諮詢服務。

選取最符合目標物件的類別和子類別。 例如，web 應用程式防火牆應該發佈到安全性類別下的 Azure Marketplace，因為目標物件是 IT 專業人員。 合約管理應用程式應改為發行至 [Sales] 類別之下的 [AppSource]，因為目標物件是商務使用者。 選取不正確的類別或子類別可能會導致您的供應專案發佈至錯誤的店面。

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>發行至兩個店面（僅限 SaaS 提供）

SaaS 供應專案可以發佈至 Azure Marketplace 或 AppSource。 如果您的 SaaS 供應專案適用*于技術*物件（Azure Marketplace）和企業物件（AppSource），請選取適用于每個店面的類別及/或子類別。 發佈給這兩個店面的供應專案都應該有一個價值主張，可延伸至 IT 專業人員*和*商務使用者。

> [!IMPORTANT]
> 具有計量付費的 SaaS 供應專案可透過 Azure Marketplace 和 Azure 入口網站取得。 只有私人方案的 SaaS 供應專案可透過 Azure 入口網站取得。

| 計量付費計費 | 公用方案 | 私用計畫 | 適用于： |
|---|---|---|---|
| 是             | 是         | 否           | Azure Marketplace 和 Azure 入口網站 |
| 是             | 是         | 是          | Azure Marketplace 和 Azure 入口網站 * |
| 是             | 否          | 是          | 僅 Azure 入口網站 |
| 否              | 否          | 是          | 僅 Azure 入口網站 |

&#42; 供應專案的私用計畫將只能透過 Azure 入口網站

例如，只有計量付費的供應專案和私人方案（不公開方案），才會由 Azure 入口網站的客戶購買。 深入瞭解[Microsoft 商業 marketplace 中的私人](private-offers.md)供應專案。

### <a name="categories"></a>類別

類別和子類別會根據目標物件對應至每個店面。 選取最符合您供應專案和預定物件的類別和子類別。 您可以選取：

- 至少一個和最多兩個類別。 您可以選擇 [主要] 和 [次要] 類別。
- 針對每個主要和/或次要類別，最多可有兩個子類別。 如果您未選取任何子類別，則您的供應專案仍然可以在選取的類別下找到。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>後續步驟

- 一旦您決定發佈選項，就能夠選取要用於展示供應項目的[供應項目類型](./publisher-guide-by-offer-type.md)。
- 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
- 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
