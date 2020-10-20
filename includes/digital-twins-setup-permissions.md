---
author: baanders
description: 在 Azure 數位 Twins 設定中包含許可權必要條件的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205471"
---
## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須 [在訂用帳戶中](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 擁有具有下列許可權的角色：
* 建立及管理 Azure 資源
* 管理使用者對 Azure 資源的存取權 (包括授與和委派許可權) 

符合這項需求的通用角色是 *擁有*者、 *帳戶管理員*，或是 *使用者存取系統管理員* 和 *參與者*的組合。 如需角色和許可權的完整說明，包括其他角色所包含的許可權，請造訪 Azure RBAC 檔中的 [*傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 角色*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 。

若要在訂用帳戶中查看您的角色，請造訪 Azure 入口網站中的 [訂用帳戶] [頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (您可以使用此連結，或使用入口網站搜尋 *列來尋找* 訂用帳戶) 。 尋找您正在使用的訂用帳戶名稱，並在 [ *我的角色* ] 資料行中查看您的角色：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="查看 Azure 入口網站中的 [訂用帳戶] 頁面，其中顯示使用者為擁有者" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果您發現值為 *參與者*，或另一個沒有上述必要許可權的角色，您可以在 *訂用帳戶上與具有這些* (許可權的使用者（例如訂用帳戶擁有者或帳戶管理員) ）連線，並以下列其中一種方式繼續進行：
* 要求他們替您完成本文中的步驟
* 要求他們提升您在訂用帳戶上的角色，讓您有權繼續進行。 這是否適當取決於您的組織和您在其中的角色。