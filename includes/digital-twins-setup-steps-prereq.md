---
author: baanders
description: 包含 Azure 數位 Twins 設定中步驟總覽和許可權必要條件的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009632"
---
>[!NOTE]
>這些作業的目的是要由有能力管理 Azure 訂用帳戶上資源和使用者存取權的使用者來完成。 雖然某些步驟可以使用較低的許可權來完成，但是具有這些許可權的人員必須具備這些許可權才能完全設定可用的實例。 如需詳細資訊，請參閱下面的必要條件 [*：必要許可權*](#prerequisites-permission-requirements) 一節。

新 Azure 數位 Twins 實例的完整設定包含三個部分：
1. **建立實例**
2. **設定使用者存取權限**： azure 使用者必須在 Azure 數位 Twins 實例上擁有 *Azure 數位 Twins 擁有者 (預覽版) * 角色，才能管理它及其資料。 在此步驟中，您身為 Azure 訂用帳戶的擁有者/系統管理員會將此角色指派給即將管理 Azure 數位 Twins 實例的人員。 這可能是您自己或您組織中的其他人。
3. **設定用戶端應用程式的存取權限**：通常會撰寫用來與您的實例互動的用戶端應用程式。 為了讓該用戶端應用程式能夠存取您的 Azure 數位 Twins，您必須在用戶端應用程式將用來向實例進行驗證的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中設定*應用程式註冊*。

若要繼續，您將需要 Azure 訂用帳戶。 您可以在 [這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個。

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