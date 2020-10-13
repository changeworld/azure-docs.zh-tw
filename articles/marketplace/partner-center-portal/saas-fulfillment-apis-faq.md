---
title: SaaS 履行 API 常見問題-Microsoft 商業 marketplace
description: 深入瞭解 Microsoft 商用 marketplace 的幾項整合需求，讓 Azure 客戶能夠訂閱 SaaS 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4c5d8b438764fa9aa3838b2225c63d412afc519b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606797"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>SaaS 履行 Api 的相關常見問題

本文說明 Microsoft 商用 marketplace 的幾項整合需求，讓 Azure 客戶能夠訂閱 SaaS 供應專案。

## <a name="discovery-experience"></a>探索體驗

發佈 SaaS 供應專案後，Azure 使用者就可以在 Azure Marketplace 中探索。 您的客戶可以根據產品類型 (SaaS) 來篩選供應專案，並探索他們感興趣的 SaaS 服務。

## <a name="purchase-experience"></a>購買體驗

一旦使用者對特定 SaaS 服務感興趣，使用者就可以從 Azure Marketplace 訂閱。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 使用者在 Azure Marketplace 中訂閱 SaaS 供應專案的意義為何？

這表示使用者可以查看與 SaaS 服務相關聯的使用規定與隱私權聲明，並同意根據您所設定的帳單條款（即 SaaS 供應專案的發行者），在 Microsoft 的發票上支付費用。 使用者可以使用其在 Azure 中現有的付款設定檔來支付 SaaS 服務的耗用量。

這項功能在許多方面都很有用。 客戶現在可以使用 Microsoft 雲端平臺作為受信任的來源，在同一處進行探索和訂閱，而不需要審查其打算使用的每個 ISV 軟體。 此外，客戶可以使用其現有的付款設定檔，而不必個別明確地支付每個 ISV 軟體的費用。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>訂閱供應專案時，使用者是否會自動收費？

在訂閱 SaaS 供應專案時，使用者同意透過 Microsoft 平臺支付 SaaS 服務的耗用量。 不過，只有在使用供應專案時，才會開始收費。 使用者必須移至您的 SaaS 供應專案並確認帳戶建立，才能開始使用供應專案。 然後，您會通知 Microsoft 開始計費此客戶 SaaS 訂用帳戶。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>當使用者訂閱您的 SaaS 供應專案時，您會如何通知？

在訂閱供應專案之後，Azure 使用者即可在 Azure 中探索及管理其所有供應專案。 根據預設，新訂閱之 SaaS 供應專案的狀態會顯示為 [布建] **、[履行擱置**]。 在此狀態下，系統會提示 Azure 使用者 **設定帳戶**的動作，以流覽 Azure 入口網站中的 SaaS 訂用帳戶管理體驗。

當使用者選取 [ **設定帳戶**] 時，系統會將他們重新導向至 SaaS 服務網站。 發行者在發行供應專案時設定了 URL。 此頁面稱為發行者的登陸頁面。 Azure 使用者會根據其在 Azure 中現有的 AAD 認證，登入 SaaS 登陸頁面。

當 Azure 使用者重新導向至登陸頁面時，會將權杖新增至查詢 URL。 此權杖存留期很短，而且在24小時的時間內有效。 然後，您可以偵測此權杖是否存在，並呼叫 Microsoft 的 API 來取得更多與權杖相關聯的內容。

![客戶訂用帳戶流程](media/saas-metering-service-integration-flow-a.png)

如需在 SaaS 供應專案生命週期中處理交易案例之 API 合約的詳細資訊，請參閱 [saas 履行 API](pc-saas-fulfillment-api-v2.md)。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>您如何知道使用者在 Azure 中訂閱的 SaaS 供應專案？

API 的回應 `Resolve` 包含與 SaaS 訂用帳戶相關聯的供應專案和方案資訊。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 使用者可以如何變更與此 Azure 訂用帳戶相關聯的方案？

* Azure 使用者可以直接在 SaaS 體驗中或透過 Microsoft 平臺，變更與 SaaS 訂用帳戶相關聯的方案。

* 您可以在計費週期中隨時完成轉換。 系統會提示您確認任何轉換，這將會在認可之後生效。

* 預付型方案 (**每月** 或 **每年**) 費率依比例計算。 直到轉換時間為止發出的任何超額部分都會在下一張發票中收費。 新的超額部分會根據新的計畫發出。

>[!Note]
>如果您不想要支援特定的轉換路徑，您可以封鎖降級。

當 Azure 客戶在 SaaS 體驗中變更方案時，下列順序會捕捉流程：

![客戶方案變更流程](media/saas-metering-service-integration-flow-b.png)

當 Azure 客戶在 Microsoft 的線上商店變更方案時，下列順序會捕捉流程：

![客戶線上商店方案變更流程](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 使用者如何從與 Azure 訂用帳戶相關聯的方案取消訂閱？

Azure 使用者可以直接在 SaaS 體驗中或透過 Microsoft 平臺，取消訂閱購買的 SaaS 供應專案。 使用者取消訂閱後，就不會再向下一個計費週期收取費用。

當 Azure 客戶在 SaaS 體驗中取消訂閱 SaaS 供應專案時，下列順序會捕捉流程：

![客戶在 SaaS 體驗中取消訂閱](media/saas-metering-service-integration-flow-d.png)

當 Azure 使用者在 Microsoft 的線上商店取消訂閱時，下列順序會捕獲流程：

![Microsoft 線上商店中的客戶取消訂閱](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>接下來的步驟

[Marketplace 計量服務 API](./marketplace-metering-service-apis.md)
