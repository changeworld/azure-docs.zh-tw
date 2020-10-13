---
title: Microsoft 商用 marketplace 中的 SaaS 履行 Api
description: 履行 Api 的簡介，可讓您在 Microsoft AppSource 與 Azure Marketplace 整合 SaaS 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037519"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 中的 SaaS 履行 Api

SaaS 履行 Api 可讓發行者（也稱為獨立軟體廠商 (Isv) ）在 Microsoft AppSource、Azure Marketplace 和 Azure 入口網站中發佈及銷售其 SaaS 應用程式。 這些 Api 可讓 ISV 應用程式參與所有啟用商務的管道：直接、合作夥伴導向的 (轉銷商) 和現場導向。  您必須在合作夥伴中心中建立和發佈可交易 SaaS 供應專案，才能與這些 Api 整合。

Isv 必須藉由新增至其 SaaS 服務程式代碼來執行下列 API 流程，以維護 Isv 和 Microsoft 的相同訂用帳戶狀態：

* 登陸頁面流程： Microsoft 會通知發行者，marketplace 中的客戶已購買發行者的 SaaS 供應專案。
* 啟用流程：發行者通知 Microsoft，已在發行者端設定新購買的 SaaS 帳戶。
* 更新流程：變更已購買的方案及/或購買的基座數目。
* 暫止和恢復流程：當客戶的付款條件不再有效時，暫停購買的 SaaS 供應專案。 當付款條件的問題解決時，已暫停的供應專案可以恢復。
* Webhook 流程： Microsoft 會通知發行者關於 SaaS 訂用帳戶的變更，以及客戶從 Microsoft 端觸發的取消。

若要取消已購買的 SaaS 訂用帳戶，整合是選擇性的，因為它可以由 Microsoft 端的客戶進行。

與 SaaS 履行 Api 的正確整合對於確保

* 購買發行者 SaaS 供應專案的終端客戶會由 Microsoft 正確地計費。
* 終端客戶會獲得正確的使用者體驗，包括購買、設定、使用及管理在 marketplace 中購買的 SaaS 訂閱。

這些 Api 可讓發行者的供應專案參與所有啟用商務的通道：

* 直接
* 合作夥伴導向的 (轉銷商、CSP) 
* 現場導向

在轉銷商 (CSP) 案例中，CSP 會代表終端客戶購買 SaaS 供應專案。 客戶應該使用 SaaS 供應專案，但 CSP 是執行下列動作的實體：

* 帳單給客戶
* 變更訂用帳戶方案/購買的基座數量
* 取消訂閱

在此案例中，發行者不需要用不同的方式來執行任何 API 呼叫流程。

如需 CSP 的詳細資訊，請參閱 https://partner.microsoft.com/licensing 。

>[!Warning]
>此 API 目前的版本是第2版，適用于所有新的 SaaS 供應專案。 第1版的 API 已淘汰，且正在維護以支援現有的供應專案。

>[!Note]
>SaaS 履行 Api 僅供從發行者的後端服務呼叫。 不支援直接從發行者的網頁與 Api 整合。 只應使用服務對服務驗證流程。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請在 [Azure 入口網站](https://ms.portal.azure.com) 中註冊您的 SaaS 應用程式，如 [註冊 Azure AD 應用程式](./pc-saas-registration.md)中所述。  之後，請使用此介面的最新版本進行開發： [SaaS 履行 API 第2版](./pc-saas-fulfillment-api-v2.md)。
