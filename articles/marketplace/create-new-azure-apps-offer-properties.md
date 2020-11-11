---
title: 如何設定您的 Azure 應用程式供應專案屬性
description: 瞭解如何在合作夥伴中心中設定 Azure 應用程式供應專案的屬性。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488514"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>如何設定您的 Azure 應用程式供應專案屬性

本文說明如何在商業市場中設定 Azure 應用程式供應專案的屬性。

在 [ **屬性** ] 頁面上，您將定義適用于您的供應專案和法律合約的類別。 請務必在此頁面上提供您的供應專案完整且正確的詳細資料，以便適當地顯示並提供給正確的一組客戶。

## <a name="select-a-category-for-your-offer"></a>選取供應專案的類別

在 [ **類別** ] 底下，選取 [ **類別** ] 連結，然後選擇至少一個和最多兩個類別，以將您的供應專案分組到適當的商業市場搜尋區域。 針對每個主要和次要類別，最多選取兩個子類別。 如果沒有任何子類別適用于您的供應專案，請選取 [ **不適用** ]。

## <a name="provide-terms-and-conditions"></a>提供條款及條件

在 [ **法律** ] 下，提供供應專案的條款及條件。 您有兩個選擇：

- [使用標準合約搭配選擇性的修正](#use-the-standard-contract)
- [使用您自己的條款及條件](#use-your-own-terms-and-conditions)

若要深入瞭解標準合約和選擇性的修訂，請參閱 [Microsoft 商業 marketplace 的標準合約](standard-contract.md)。 您可以下載 [標準合約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (確定您的快顯封鎖程式) 。

### <a name="use-the-standard-contract"></a>使用標準合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜性，Microsoft 提供了一種標準合約，可供您在商業市集中提供供應專案使用。 當您在標準合約下提供您的軟體時，客戶只需要讀取並接受一次，您就不需要建立自訂條款及條件。

1. 選取 [ **使用 Microsoft 的商業 marketplace 標準合約** ] 核取方塊。

   ![說明 [使用 Microsoft 的商業 marketplace 標準合約] 核取方塊。](partner-center-portal/media/use-standard-contract.png)

1. 在 **確認** 對話方塊中，選取 [ **接受** ]。 您可能必須向上查看才能看到它。
1. 選取 [儲存草稿] 後再繼續。

> [!NOTE]
> 使用商用 marketplace 的標準合約發佈供應專案之後，您就無法使用自己的自訂條款及條件。 您可以使用選擇性的修訂或您自己的條款及條件，在標準合約下提供您的解決方案。

### <a name="add-amendments-to-the-standard-contract-optional"></a>將修訂新增至標準合約 (選擇性) 

有兩種可用的改正： _通用_ 和 _自訂_ 。

#### <a name="add-universal-amendment-terms"></a>新增通用修訂條款

在 [ **Microsoft 商業 marketplace 標準合約] 的 [通用修訂條款** ] 方塊中，輸入您的通用修訂條款。 您可以在此方塊中輸入不限數目的字元。 客戶在探索和購買流程期間，會於 AppSource、Azure Marketplace 和/或 Azure 入口網站看到這些條款。

#### <a name="add-one-or-more-custom-amendments"></a>新增一或多個自訂修正

1. 在 **Microsoft 商業 marketplace 標準合約的自訂修訂條款** 底下，選取 [ **新增自訂修訂條款] ([最大 10])** 連結。
1. 在 [ **自訂修訂條款** ] 方塊中，輸入您的修訂條款。
1. 在 [ **租使用者識別碼** ] 方塊中，輸入租使用者識別碼。 只有與您為這些自訂條款指定的租使用者識別碼相關聯的客戶，才會在 Azure 入口網站的供應專案購買流程中看到這些客戶。

   > [!TIP]
   > 租使用者識別碼會識別您在 Azure 中的客戶。 您可以要求客戶提供此識別碼，並藉由前往 [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **屬性** 來尋找。 目錄識別碼值是租使用者識別碼 (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。 您也可以在[我的 Microsoft Azure 和 Office 365 租用戶識別碼是什麼？](https://www.whatismytenantid.com/) (英文) 中，使用其網域名稱 URL 來查詢客戶的組織租用戶識別碼。

1. 在 [ **描述** ] 方塊中，選擇性地輸入租使用者識別碼的易記描述。 此描述可協助您識別您以修訂為目標的客戶。
1. 若要新增另一個租使用者識別碼，請選取 [ **新增客戶的租使用者識別碼** ] 連結，然後重複步驟3和4。 您最多可以新增20個租使用者識別碼。
1. 若要新增其他修訂期限，請重複步驟1到5。 您可以為每個供應項目最多提供 10 個自訂增修條款。
1. 選取 [儲存草稿] 後再繼續。

### <a name="use-your-own-terms-and-conditions"></a>使用您自己的條款及條件

您可以選擇提供您自己的條款及條件，而不是標準合約。 客戶必須先接受這些條款，才能試用您的供應項目。

1. 在 [ **法律** ] 下，請確定已清除 [ **使用 Microsoft 的商業 marketplace 標準合約** ] 核取方塊。
1. 在 [ **條款及條件** ] 方塊中，輸入最多10000個字元的文字。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤：供應專案清單。

## <a name="next-steps"></a>後續步驟

- [如何設定您的 Azure 應用程式供應專案清單詳細資料](create-new-azure-apps-offer-listing.md)
