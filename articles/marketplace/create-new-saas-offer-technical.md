---
title: 如何在 Microsoft 合作夥伴中心中為您的 SaaS 供應專案新增技術詳細資料
description: 瞭解如何為 Microsoft 商業市場提供軟體即服務 (SaaS) 供應專案的技術詳細資料。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 78a81a2d392363ac45979fd28138f838653a2358
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380774"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>如何為您的 SaaS 供應專案新增技術詳細資料

本文說明如何輸入技術詳細資料，以協助 Microsoft 商業市場連接到您的解決方案。 如果客戶選擇取得和管理您的供應專案，此連線可讓我們為客戶布建供應專案。 如需這些設定的詳細資訊，請參閱 [技術資訊](plan-saas-offer.md#technical-information)。

> [!NOTE]
> 如果您選擇單獨處理交易，將不會看到此選項。 相反地，請跳至 [如何行銷 SaaS 供應](create-new-saas-offer-marketing.md)專案。

## <a name="technical-configuration"></a>技術設定

在 [ **技術** 設定] 索引標籤上，您將定義商業 marketplace 用來與您的 SaaS 應用程式或解決方案通訊的技術詳細資料。 

- **登陸頁面 URL** (必要的) –定義 SAAS 網站 url (例如： `https://contoso.com/signup` 從商業市場取得您的供應專案，以及從新建立的 SaaS 訂用帳戶觸發設定程式之後，客戶將居住的) 。

  > [!IMPORTANT]
  > 登陸頁面應該會啟動並執行24/7。 這是您在商業市集中購買 SaaS 供應專案的新資訊，或供應專案使用中訂用帳戶的設定要求時，您將收到通知的唯一方式。

- 連線**webhook** (必要的) –針對 Microsoft 需要傳送給您的所有非同步事件 (例如，已取消) SaaS 訂用帳戶，我們會要求您提供連線 webhook URL。 我們會呼叫此 URL 來通知您活動。

  > [!IMPORTANT]
  > 您的 webhook 應該啟動並執行24/7，因為這是您將透過商業 marketplace 購買的客戶 SaaS 訂用帳戶更新通知的唯一方式。

- **Azure Active Directory 租使用者識別碼** (必要) -若要尋找 Azure Active Directory (Azure AD 應用程式的租使用者識別碼，請移 [至) 中應用程式註冊的分頁](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 。 在 [ **顯示名稱** ] 欄中，選取應用程式。 然後尋找列出的 ** (租使用者) 識別碼** (例如) 的目錄 `50c464d3-4930-494c-963c-1e951d15360e` 。

- **Azure Active Directory 應用程式識別碼** (必要) –若要尋找您的 [應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，請移至 Azure Active Directory 中的 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 分頁。 在 [ **顯示名稱** ] 欄中，選取應用程式。 然後尋找列出的應用程式 (用戶端) 識別碼 (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [計畫總覽]。

## <a name="next-steps"></a>接下來的步驟

- [如何建立 SaaS 供應專案的方案](create-new-saas-offer-plans.md)。
