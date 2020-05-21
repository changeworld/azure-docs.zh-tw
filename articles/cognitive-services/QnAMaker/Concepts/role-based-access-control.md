---
title: 與其他人共同作業-QnA Maker
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 080e6549579675e27486e6173d5907d92bbaad70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724916"
---
# <a name="collaborate-with-other-authors-and-editors"></a>與其他作者和編輯者共同作業

使用位於 QnA Maker 資源上的角色型存取控制（RBAC），與其他作者和編輯者共同作業。

## <a name="access-is-provided-on-the-qna-maker-resource"></a>QnA Maker 資源上提供存取權

擁有權限都是由 QnA Maker 資源上所設定的許可權所控制。 這些許可權會與讀取、寫入、發行和完整存取一致。

此 RBAC 功能包括：
* Azure Active Directory （AAD）的100% 回溯相容于擁有者和參與者的金鑰型驗證。 客戶可以在其要求中使用以金鑰為基礎的驗證或以 RBAC 為基礎的驗證。
* 快速將作者和編輯器加入資源中的所有知識庫，因為控制項是在資源層級，而不是在知識庫層級。

## <a name="access-is-provided-by-a-defined-role"></a>存取是由定義的角色所提供

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>驗證流程

下圖顯示從作者的觀點來登入 QnA Maker 入口網站，以及使用撰寫 Api 的流程。

> [!div class="mx-imgBorder"]
> ![下圖顯示從作者的觀點來登入 QnA Maker 入口網站，以及使用撰寫 Api 的流程。](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|步驟|描述|
|--|--|
|1|入口網站取得 QnA Maker 資源的權杖。|
|2|入口網站會呼叫適當的 QnA Maker 撰寫 API （APIM）來傳遞權杖，而不是金鑰。|
|3|QnA Maker API 會驗證權杖。|
|4 |QnA Maker API 會呼叫 QnAMaker 服務。|

如果您想要呼叫[撰寫 api](../How-To/collaborate-knowledge-base.md)，請深入瞭解如何設定驗證。

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker 入口網站進行驗證

如果您使用 QnA Maker 入口網站撰寫和共同作業，在[將適當的角色新增至](../How-To/collaborate-knowledge-base.md)共同作業者的資源之後，QnA Maker 入口網站會管理所有存取權限。

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker Api 和 Sdk 進行驗證

如果您使用 Api （透過 REST 或 Sdk）來撰寫和共同作業，則必須[建立服務主體](../../authentication.md#assign-a-role-to-a-service-principal)來管理驗證。

## <a name="next-step"></a>後續步驟

* 設計適用于[語言](design-language-culture.md)和[用戶端應用程式](integration-with-other-applications.md)的知識庫
