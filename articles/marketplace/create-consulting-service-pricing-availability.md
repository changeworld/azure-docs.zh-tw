---
title: 如何在 Microsoft 合作夥伴中心中設定您的諮詢服務定價和可用性
description: 瞭解如何使用合作夥伴中心，在 Microsoft 商用 marketplace 中設定您的諮詢服務供應專案價格詳細資料和市場可用性。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780224"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>如何設定您的諮詢服務定價和可用性

本文說明如何在 Microsoft 商業市場中定義您的諮詢服務供應專案的市場可用性和定價詳細資料。

> [!NOTE]
> 諮詢服務供應專案僅限清單。 所有交易都必須在您與您的客戶之間，于商業市場外部進行管理。

## <a name="markets"></a>市場

在 [ **市場** ] 區段中，您可以選取您的諮詢服務將可使用的國家或地區。

1. 在 [ **市場**] 下，選取 [ **編輯市場** ] 連結。
2. 在出現的對話方塊中，選取您要讓供應專案可供使用的市場位置。 您必須選取最少1個或最多141市場。
3. 選取 [ **儲存** ] 以關閉對話方塊。

## <a name="preview-audience"></a>預覽對象

當您發佈或更新您的諮詢服務供應專案時，合作夥伴中心會建立清單的預覽版本。 只有有 **隱藏金鑰** 的使用者才能看到此預覽。

在 [ **隱藏金鑰** ] 欄位中，輸入您將用來存取供應專案預覽版本的英數位元字串。

## <a name="pricing-informational-only"></a>定價 (僅供參考) 

在 [ **定價** ] 區段中，定義這是免費或付費供應專案。

針對付費供應專案，指定價格是固定的，還是預估價格。 如果預估價格，您的供應專案描述必須說明哪些因素會影響價格。

如果您在 [ **市場** ] 區段中選擇單一國家或地區，請提供該市場支援的貨幣價格，然後選取 [ **儲存草稿**]。 如需支援的貨幣清單，請參閱 [商用 marketplace 的地理可用性和貨幣支援](./marketplace-geo-availability-currencies.md) 。

如果您選擇 [ **市場** ] 區段中的多個國家或地區，請以「美國金額」 (美元) ，然後選取 [ **儲存草稿**]。 合作夥伴中心會使用您儲存草稿時可用的匯率，將該價格轉換為所有選定市場的當地貨幣。

若要驗證轉換或在個別市場中設定自訂價格，您需要匯出、修改，然後匯入定價試算表：

1. 在 [ **價格**] 下，選取 [ **匯出定價資料** ] 連結。 這會將檔案下載到您的裝置。
1. 在 Microsoft Excel 中開啟 exportedPrice.xlsx 檔案。
1. 在試算表中，您可以調整每個市場的價格和貨幣。 如需支援的貨幣清單，請參閱 [商用 marketplace 的地理可用性和貨幣支援](./marketplace-geo-availability-currencies.md) 。 當您完成時，請儲存檔案。
1. 在合作夥伴中心的 [ **定價**] 下，選取 [匯 **入定價資料** ] 連結。 匯入檔案將會覆寫先前的價格資訊。

> [!IMPORTANT]
> 您在合作夥伴中心中定義的價格是靜態的，而且不會遵循 exchange 費率的變化。 若要在發行後變更一或多個市場的價格，請在合作夥伴中心中更新並重新提交您的供應專案。

選取 [儲存草稿] 後再繼續。

## <a name="next-steps"></a>後續步驟

* [檢閱並發佈](review-publish-offer.md)