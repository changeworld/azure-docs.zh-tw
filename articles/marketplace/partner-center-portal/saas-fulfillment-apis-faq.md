---
title: SaaS 履行 API - 常見問題 |Azure 應用商店
description: Azure 應用商店中 SaaS 產品的客戶發現和購買體驗。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275691"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS 履行 API - 常見問題集

列出了與 Azure 應用商店的集成要求，以使 Azure 客戶能夠訂閱 SaaS 產品/服務。

## <a name="discovery-experience"></a>發現體驗

發佈產品/服務後，Azure 使用者可以在 Azure 應用商店中發現 SaaS 產品/服務。 您的客戶將能夠根據產品類型 （SaaS） 篩選產品/服務，並瞭解他們感興趣的 SaaS 服務。

## <a name="purchase-experience"></a>購買體驗

使用者對特定的 SaaS 服務感興趣後，使用者可以從 Azure 應用商店訂閱該服務。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 使用者訂閱 Azure 應用商店中的 SaaS 產品/服務意味著什麼？

這意味著使用者可以查看與 SaaS 服務關聯的使用條款和隱私權聲明，並同意根據您（SaaS 產品發行者）在 Microsoft 發票上設置的計費條款付款。 使用者可以使用 Azure 中的現有付款設定檔來為 SaaS 服務消耗付費。

這有很多原因。 客戶現在可以使用 Microsoft 雲平臺作為受信任的來源在一個地方發現和訂閱，而無需審查它打算使用的每個 ISV 軟體。 此外，客戶可以使用其現有的付款設定檔，而無需單獨顯式支付每個 ISV 軟體。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>訂閱產品/服務時，使用者是否自動收費？

在訂閱 SaaS 優惠時，使用者已同意通過 Microsoft 平臺支付 SaaS 服務的使用費用。 但是，費用僅在消費產品/服務時開始。 使用者必須轉到您的 SaaS 產品/服務並確認帳戶創建才能開始使用產品/服務。 然後，您將通知 Microsoft 開始為此客戶 SaaS 訂閱計費。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>當使用者訂閱您的 SaaS 產品/服務時，您如何收到通知？

訂閱產品/服務後，Azure 使用者可以在 Azure 中發現和管理其所有產品/服務。 預設情況下，新訂閱的 SaaS 產品/服務的狀態為 **"預配，正在履行"。** 在此狀態下，將提示 Azure 使用者執行 **"配置帳戶"** 的操作，以便流覽到 Azure 門戶中的 SaaS 訂閱管理體驗。

當使用者按一下 **"配置帳戶"** 時，他們將重定向到 SaaS 服務網站。 導航到它們的 URL 由發行者在發佈產品/服務時提供。 此頁稱為發行者的著陸頁。 Azure 使用者應該能夠基於 Azure 中現有的 AAD 憑據登錄到 SaaS 登錄頁。

將 Azure 使用者重定向到登錄頁時，將標記添加到查詢 URL 中。 此權杖持續時間短，有效期為 24 小時。 然後，可以檢測此權杖的存在，並調用 Microsoft 的 API 以獲取有關權杖的更多上下文。

![客戶訂閱流](media/saas-metering-service-integration-flow-a.png)

有關在 SaaS 產品生命週期內處理交易方案的 API 合同的詳細資訊，請參閱[SaaS 履行 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)文檔。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>您如何知道使用者在 Azure 中訂閱的 SaaS 產品/服務？

對 API`Resolve`的回應包括與 SaaS 訂閱關聯的產品/服務並計畫資訊。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 使用者如何更改與此 Azure 訂閱關聯的計畫？

* Azure 使用者可以直接在 SaaS 體驗中或通過 Microsoft 平臺更改與 SaaS 訂閱關聯的計畫。

* 轉換可以在計費週期的任何時間完成。 您必須確認任何轉換，一旦確認，該轉換將生效。

* 預付費計畫（**每月**或**每年**）費率按比例計算。 在轉換時間前發出的任何超額費用將在下一張發票中收取。 將根據新計畫排放新的超額。

>[!Note]
>如果不想支援特定的轉換路徑，則可以阻止降級。

以下序列捕獲 Azure 客戶在 SaaS 體驗中更改計畫時的流：

![客戶計畫變更流程](media/saas-metering-service-integration-flow-b.png)

以下序列捕獲 Azure 客戶在 Microsoft 網店中更改計畫時的流

![客戶店面計畫變更流程](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 使用者如何取消訂閱與 Azure 訂閱關聯的計畫？

Azure 使用者可以直接在 SaaS 體驗中或通過 Microsoft 平臺取消訂閱已購買的 SaaS 產品。 使用者取消訂閱後，將不再從下一個計費週期中收取費用。

以下序列捕獲 Azure 客戶取消訂閱 SaaS 體驗中 SaaS 產品/服務的流：

![客戶在 SaaS 體驗中取消訂閱](media/saas-metering-service-integration-flow-d.png)

以下序列捕獲 Azure 使用者在 Microsoft 網店中取消訂閱時的流：

![客戶在微軟的網店取消訂閱](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>後續步驟

- 有關詳細資訊[，請參閱應用商店計量服務 API。](./marketplace-metering-service-apis.md)
