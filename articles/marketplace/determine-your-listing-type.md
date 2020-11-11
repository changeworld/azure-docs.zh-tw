---
title: 決定您的發佈選項 - Microsoft 商業市集
description: 本文說明將供應項目發佈至 Microsoft AppSource 和 Azure Marketplace 的資格條件和需求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 09/04/2020
ms.openlocfilehash: 17b53d656d0344e4178b3034a972e26d6aa0bc15
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492101"
---
# <a name="determine-your-publishing-option"></a>決定您的發佈選項

您為供應項目選擇的發佈選項，與資格需求和商業市集 GTM 權益直接相關。 更重要的是，發佈選項和供應項目類型的選取項目，會定義使用者與您的商業市集供應項目互動的方式。

若要設定您的供應專案，您必須瞭解下列主要的商業 marketplace 概念：發佈選項、供應專案類型和設定，以及可管理您的供應專案在商業 marketplace 線上商店中的顯示方式和位置的清單選項。

在本文中，您將了解：

- 如何為您的解決方案判斷適當的線上商店。
- 每個線上商店都有提供哪些發佈選項和清單選項。
- 每個發佈選項都有提供哪些供應專案類型。

## <a name="commercial-marketplace-publishing-options"></a>商業市集發佈選項

下表顯示供應項目類型在 Microsoft AppSource 和 Azure Marketplace 中的發佈選項。

|   | **清單 (連絡人)**  | **清單 (試用版)**  | **免費** | **BYOL** | **交易**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **虛擬機器** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure 應用程式 (多個 VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **容器映像** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge 模組** |  |  | Azure Marketplace | Azure Marketplace |   |
| **受控服務** |  |  |  | Azure Marketplace |   |
| **諮詢服務** | 這兩個線上商店 |  |  |  |   |
| **SaaS 應用程式** | 這兩個線上商店 | 這兩個線上商店 | 這兩個線上商店 |  | 兩個線上商店 * |
| **Microsoft 365 應用程式** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 增益集** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42;Microsoft AppSource 中的 SaaS 應用程式交易供應項目目前只能使用信用卡。

&#42;&#42;Microsoft 365 供應項目可免費安裝，並可透過 SaaS 供應項目作為授權服務來販售。 如需詳細資訊，請參閱 [透過 Microsoft 商業市場銷售 Microsoft 365 增益集](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)。

## <a name="choose-a-listing-option"></a>挑選清單選項

可用的清單選項提供差異化的客戶參與，同時讓您存取潛在客戶共用和 [商業 marketplace 的權益](./gtm-your-marketplace-benefits.md)。 請注意與發行選項對應的清單選項：

| **發行選項**    | **說明**  |
| :------------------- | :-------------------|
| **清單** | 簡單的應用程式或服務清單，可讓商業 marketplace 使用者要求您透過 **Contact Me** 清單選項與客戶聯繫。 |
| **試用版** | 使用商業市集來增強曝光度，並且將佈建解決方案的試用體驗自動化，讓潛在使用者在購買之前，在一段有限的時間內免費使用您的 SaaS、IaaS 或 Microsoft 應用程式內體驗。 試用版發行選項的清單選項可以是 **免費試用版****或試用** 產品。 |
| **BYOL** | 使用商業市集增強解決方案的能見度和自動佈建能力，並個別完成財務交易。 BYOL 供應項目類型適用於內部部署至雲端的移轉。 清單選項 **立即取得** 。
| **交易** | 交易供應項目會透過商業市集銷售。 Microsoft 負責計費和收費。 清單選項 **立即取得** 。|

> [!Note]
> 當使用交易發佈選項時，請務必了解定價、計費、發票處理和付款考量，然後再選取供應項目類型與建立供應項目。 若要深入了解，請參閱[商業市集交易功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)一文。

## <a name="selecting-an-online-store"></a>選取線上商店

每個線上商店都能針對商務和 IT 解決方案提供不同的客戶需求。 您的供應專案類型、交易功能和類別將會決定您供應專案的發佈位置。 類別和子類別會根據您發行的解決方案類型，對應至每個線上商店：

**Microsoft AppSource** 為 Dynamics 365、Microsoft 365 和 Power Platform 供應商務解決方案，例如產業解決方案和諮詢服務。

**Azure Marketplace** 提供專為 Azure 或 azure 提供的 IT 解決方案，以及可加速客戶使用 azure 的諮詢服務。

選取最符合您解決方案類型的類別和子類別。 例如，web 應用程式防火牆是 IT 解決方案，應發佈至 Azure Marketplace 的 [安全性] 類別之下。 合約管理應用程式是商務解決方案，應發佈至 Sales 類別下的 AppSource。 選取不正確的類別或子類別目錄，可能會導致您的供應專案發佈至錯誤的線上商店。

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>發佈至 (SaaS 提供的線上商店) 

SaaS 供應專案可以發佈至 Azure Marketplace 或 AppSource。 如果您的 SaaS 供應 *專案是 IT* 解決方案 (Azure Marketplace) 以及 (AppSource) 的商務解決方案，請選取適用于每個線上商店的類別及/或子類別。 發佈到兩個線上商店的供應專案，都應該有價值主張作為 IT 解決方案 *和* 商務解決方案。

> [!IMPORTANT]
> 具有 [計量計費](partner-center-portal/saas-metered-billing.md) 的 SaaS 供應專案可透過 Azure Marketplace 和 Azure 入口網站取得。 只有私人方案提供的 SaaS 供應專案可透過 Azure 入口網站取得。

| 計量付費 | 公用方案 | 私人方案 | 適用于： |
|---|---|---|---|
| 是             | 是         | 否           | Azure Marketplace 和 Azure 入口網站 |
| 是             | 是         | 是          | Azure Marketplace 和 Azure 入口網站 * |
| 是             | 否          | 是          | 僅 Azure 入口網站 |
| 否              | 否          | 是          | 僅 Azure 入口網站 |

&#42; 供應專案的私用方案將只能透過 Azure 入口網站取得

例如，使用計量付費帳單和私人方案的供應專案僅 (沒有公用方案) ，將由客戶在 Azure 入口網站中購買。 深入瞭解 [Microsoft 商業市集中的私人優惠](private-offers.md)。

### <a name="categories"></a>類別

類別和子類別會根據方案類型對應到每個線上商店。 選取最符合您解決方案的類別和子類別。 您可以選取：

- 至少一個和最多兩個類別。 您可以選擇主要和次要類別。
- 每個主要和/或次要類別最多可有兩個子類別。 如果您未選取任何子類別，則您的供應專案仍可在所選類別下找到。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>後續步驟

- 一旦您決定發佈選項，請參閱「 [依供應專案類型的發佈指南](./publisher-guide-by-offer-type.md)」。