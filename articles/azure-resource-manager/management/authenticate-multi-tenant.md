---
title: 跨租用戶進行驗證
description: 描述 Azure Resource Manager 處理跨租用戶驗證要求的方式。
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478821"
---
# <a name="authenticate-requests-across-tenants"></a>跨租用戶驗證要求

在建立多租用戶應用程式時，您可能需要處理不同租用戶中資源的驗證要求。 常見的案例是某個租用戶中的虛擬機器必須加入其他租用戶中的虛擬網路。 Azure Resource Manager 提供儲存輔助權杖的標頭值，以驗證對不同租用戶的要求。

## <a name="header-values-for-authentication"></a>驗證標頭值

要求具有下列驗證標頭值：

| 標頭名稱 | 描述 | 範例值 |
| ----------- | ----------- | ------------ |
| 授權 | 主要權杖 | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 輔助權杖 | 承載&lt;輔助權杖1，&gt;加密承載&lt;輔助權杖2，&gt;承載&lt;輔助權杖3&gt; |

輔助標頭最多可以持有三個輔助權杖。 

在您的多租用戶應用程式的程式碼中，取得其他租用戶的驗證權杖，並將它們儲存在輔助標頭中。 所有權杖都必須來自相同的使用者或應用程式。 使用者或應用程式必須已經受邀請為其他租用戶的來賓。

## <a name="processing-the-request"></a>處理要求

當您的應用程式傳送要求至 Resource Manager 時，該要求會在主要權杖的身分識別下執行。 主要權杖必須是有效且未到期的。 此權杖必須是來自可以管理訂用帳戶的租用戶。

當要求參考來自其他租用戶的資源時，Resource Manager 會檢查輔助權杖，以判斷是否能處理該要求。 標頭中的所有輔助權杖必須都是有效且未到期的。 如果任何權杖是過期的，則 Resource Manager 會傳回 401 回應碼。 回應包含的用戶端識別碼和租用戶識別碼不是來自有效權杖。 如果輔助標頭包含租用戶的有效權杖，則會處理跨租用戶的要求。

## <a name="next-steps"></a>後續步驟

* 要瞭解身份驗證請求，請參閱[身份驗證流和應用程式方案](../../active-directory/develop/authentication-flows-app-scenarios.md)。
* 如需權杖的詳細資訊，請參閱 [Azure Active Directory 存取權杖](../../active-directory/develop/access-tokens.md)。
