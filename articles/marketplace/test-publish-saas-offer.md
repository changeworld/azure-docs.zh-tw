---
title: 如何測試及發佈 SaaS 供應專案至 Microsoft 商業市場
description: 使用合作夥伴中心將您的 SaaS 供應專案提交給預覽版、預覽您的供應專案、進行測試，然後將其發佈至 Microsoft 商用 marketplace。
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: bef59ea9e7de77e7f9a80cc3950762ea70238b87
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380663"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>如何測試 SaaS 供應專案並將其發佈至商用 marketplace

本文說明如何使用合作夥伴中心來提交 SaaS 供應專案以供發佈、預覽您的供應專案、進行測試，然後將其即時發佈到商用 marketplace。 您必須已經建立想要發佈的供應專案。

## <a name="submit-your-offer-for-publishing"></a>提交您的供應專案以供發行

1. 登入 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)中的商用 marketplace 儀表板。
1. 在 [ **總覽** ] 頁面上，選取您要發佈的供應專案。
1. 選取入口網站右上角的 [ **審核併發布**]。
2. 請確定每個頁面的 [ **狀態** ] 資料行都顯示 [ **完成**]。 三種可能的狀態如下：

   - **未啟動** –頁面未完成。
   - **未完成** -頁面遺漏必要資訊，或有需要修正的錯誤。 您必須返回頁面，並加以更新。
   - **完成** –頁面已完成。 已提供所有必要的資料，而且沒有任何錯誤。

1. 如果有任何頁面的狀態不是 [ **完成**]，請選取頁面名稱，更正問題，儲存頁面，然後選取 [檢查]，然後再 **發佈** 一次，以返回此頁面。
1. 所有頁面都完成後，請在 [憑證 **注意事項** ] 方塊中提供測試指示給認證小組，以確保您的應用程式已正確測試。 提供可説明您瞭解應用程式的任何補充附注。
1. 若要啟動供應專案的發佈程式，請選取 [ **發佈**]。 [ **供應專案總覽** ] 頁面隨即出現，並顯示供應專案的 **發佈狀態**。

您供應專案的發佈狀態將會在整個發行流程中變更。 如需此程式的詳細資訊，請參閱 [驗證和發佈步驟](review-publish-offer.md#validation-and-publishing-steps)。

## <a name="preview-and-test-your-offer"></a>預覽並測試您的供應專案

當供應專案準備好進行登出時，我們會傳送一封電子郵件給您，要求您複習並核准您的供應專案預覽。 您也可以在瀏覽器中重新整理 **供應專案總覽** 頁面，以查看您的供應專案是否已達到發行者登出階段。 如果有的話，就可以使用 [ **上線** ] 按鈕和 [預覽] 連結。 視您在建立供應專案時所選擇的選項而定，會有 Microsoft AppSource preview、Azure Marketplace preview 或兩者的連結。 如果您選擇透過 Microsoft 銷售您的供應專案，任何已新增至預覽物件的人都可以測試您的供應專案取得和部署，以確保它符合您在此階段的需求。

下列螢幕擷取畫面顯示 SaaS 供應專案的 [供應專案] **總覽** 頁面，其中有兩個 [ **上線** ] 按鈕底下的預覽連結。 您將在此頁面上看到的驗證步驟，會根據您在建立供應專案時所做的選擇而有所不同。

![說明合作夥伴中心中供應專案的供應專案總覽頁面。 即會顯示 [上線] 按鈕和預覽連結。](media/publish-status-publisher-signoff.png)

使用下列步驟來預覽您的供應專案。

1. 在 [ **供應專案總覽** ] 頁面上，選取 [ **上線** ] 按鈕底下的預覽連結。 

1. 若要驗證端對端購買和設定流程，請在預覽期間購買您的供應專案。 首先，請向 Microsoft 通知 [支援票證](https://aka.ms/marketplacesupport) ，以確保我們不會處理費用。

1. 如果您的 SaaS 供應專案支援 [使用商用 marketplace 計量服務進行計量計費](./partner-center-portal/saas-metered-billing.md)，請參閱並遵循 [Marketplace 計量付費 api](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)中所述的測試最佳作法。

1. 請參閱 [Microsoft 商用 marketplace 中的 SaaS 履行 api 第2版中](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) 的測試指示，以確保您的供應專案已成功與 api 整合，然後再發佈您的供應專案。

1. 如果您需要在預覽和測試供應專案之後進行變更，您可以編輯並重新提交以發佈新的預覽。 如需詳細資訊，請參閱 [更新商業 marketplace 中的現有供應](./partner-center-portal/update-existing-offer.md)專案。

## <a name="publish-your-offer-live"></a>即時發佈您的供應專案

完成預覽的所有測試之後，請選取 [上線] **，將您** 的供應專案即時發佈到商業 marketplace。

   > [!TIP]
   > 如果您的供應專案已在商用市集中上線，您所做的任何更新都不會上線，除非 **您選取 [上線]**。

既然您已選擇讓供應專案在商業 marketplace 中提供，我們會執行一系列的最終驗證檢查，以確保即時供應專案的設定與預覽版本的供應專案一樣。 如需這些驗證檢查的詳細資訊，請參閱 [發行階段](review-publish-offer.md#publish-phase)。

完成這些驗證檢查之後，您的供應專案將會存留在 marketplace 中。

## <a name="next-step"></a>後續步驟

[在合作夥伴中心中存取商用 marketplace 的分析報表](./partner-center-portal/analytics.md)