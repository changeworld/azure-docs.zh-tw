---
title: Microsoft 商業 marketplace 中的 SaaS 履行 Api
description: 「履行 Api」的簡介，可讓您在 Microsoft AppSource 和 Azure Marketplace 中整合您的 SaaS 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037519"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 中的 SaaS 履行 Api

SaaS 履行 Api 可讓發行者（也稱為獨立軟體廠商） (Isv) ，以 Microsoft AppSource、Azure Marketplace 和 Azure 入口網站發佈和銷售其 SaaS 應用程式。 這些 Api 可讓 ISV 應用程式參與所有啟用商務功能的管道：直接、合作夥伴導向的 (轉銷商) ，以及現場 led。  與這些 Api 整合是在合作夥伴中心建立和發佈 transactable SaaS 供應專案的必要條件。

Isv 必須藉由將加入其 SaaS 服務程式代碼中，以維持與 Isv 和 Microsoft 相同的訂用帳戶狀態，來執行下列 API 流程：

* 登陸頁面流程： Microsoft 通知發行者發行者的 SaaS 供應專案已由 marketplace 中的客戶購買。
* 啟用流程：發行者通知 Microsoft，已在發行者端設定新購買的 SaaS 帳戶。
* 更新流程：變更已購買的方案和（或）購買的基座數目。
* 暫停和恢復流程：如果客戶的付款方法不再有效，則暫停購買的 SaaS 供應專案。 當付款條件的問題解決時，可以復原已暫停的供應專案。
* Webhook 流程： Microsoft 會通知發行者有關 SaaS 訂閱的變更，以及客戶從 Microsoft 端觸發的取消。

對於已購買的 SaaS 訂用帳戶的取消，整合是選擇性的，因為它可以由 Microsoft 端的客戶進行。

與 SaaS 履行 Api 的正確整合非常重要，這是為了確保

* 購買發行者 SaaS 供應專案的終端客戶，會由 Microsoft 正確計費。
* 終端客戶會獲得正確的使用者體驗，包括購買、設定、使用和管理在 marketplace 中購買的 SaaS 訂閱。

這些 Api 可讓發行者的供應專案參與所有啟用商務功能的頻道：

* 直接
* 合作夥伴導向的 (轉銷商、CSP) 
* 現場 led

在轉銷商 (CSP) 案例中，CSP 會代表終端客戶購買 SaaS 供應專案。 客戶應使用 SaaS 供應專案，但 CSP 是執行下列動作的實體：

* 向客戶收費
* 變更訂用帳戶方案/已購買基座數量
* 取消訂閱

在此案例中，不需要發行者以不同的方式來執行任何 API 呼叫流程。

如需 CSP 的詳細資訊，請參閱 https://partner.microsoft.com/licensing 。

>[!Warning]
>此 API 的目前版本是第2版，應用於所有新的 SaaS 供應專案。 API 第1版已被取代，並加以維護以支援現有的供應專案。

>[!Note]
>SaaS 履行 Api 僅適用于從發行者的後端服務呼叫。 不支援直接從發行者的網頁與 Api 整合。 只應使用服務對服務驗證流程。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請在[Azure 入口網站](https://ms.portal.azure.com)中註冊您的 SaaS 應用程式，如[註冊 Azure AD 應用程式](./pc-saas-registration.md)中所述。  之後，使用此介面的最新版本進行開發： [SaaS 履行 API 第2版](./pc-saas-fulfillment-api-v2.md)。
