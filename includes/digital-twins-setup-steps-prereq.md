---
author: baanders
description: 包含 Azure 數位 Twins 設定中的步驟總覽和許可權先決條件的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009632"
---
>[!NOTE]
>這些作業必須由具有能力的使用者完成，才能管理 Azure 訂用帳戶上的資源和使用者存取。 雖然有些步驟可以使用較低的許可權來完成，但具有這些許可權之人員的合作將需要完整設定可用的實例。 如需詳細資訊，請參閱下列[*必要條件：必要許可權*](#prerequisites-permission-requirements)一節。

新的 Azure 數位 Twins 實例的完整設定包含三個部分：
1. **建立實例**
2. **設定使用者存取權限**： azure 使用者必須擁有 Azure 數位 Twins 實例上的*Azure 數位 Twins 擁有者 (預覽) *角色，才能管理 it 和其資料。 在此步驟中，您身為 Azure 訂用帳戶的擁有者/系統管理員，會將此角色指派給將管理您的 Azure 數位 Twins 實例的人員。 這可能是您自己或組織中的其他人。
3. **設定用戶端應用程式的存取權限**：通常會撰寫將用來與您的實例互動的用戶端應用程式。 為了讓該用戶端應用程式存取您的 Azure 數位 Twins，您必須在用戶端應用程式將用來向實例進行驗證的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中設定*應用程式註冊*。

若要繼續，您將需要 Azure 訂用帳戶。 您可以在[這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個。

## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須[在訂用帳戶中](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)擁有具有下列許可權的角色：
* 建立和管理 Azure 資源
* 管理使用者對 Azure 資源的存取權 (包括授與和委派許可權) 

符合此需求的一般角色為*擁有*者、*帳戶管理員*，或*使用者存取系統管理員*和*參與者*的組合。 如需角色和許可權的完整說明（包括其他角色所包含的許可權），請造訪 Azure RBAC 檔中的[*傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 角色*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

若要在您的訂用帳戶中查看您的角色，請造訪 Azure 入口網站中的 [[訂閱] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (您可以使用此連結，或使用入口網站搜尋列 *) 來尋找訂用帳戶*。 尋找您所使用的訂用帳戶名稱，並在 [*我的角色*] 欄中為它查看您的角色：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Azure 入口網站中的 [訂閱] 頁面，以擁有者身分顯示使用者" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果您發現值是「*參與者*」，或另一個沒有上述必要許可權的角色，您可以與您*訂用帳戶中具有這些* (許可權的使用者（例如訂閱擁有者或帳戶管理員) ）連線，並繼續進行下列其中一種方式：
* 要求他們替您完成本文中的步驟
* 要求他們提高訂用帳戶的角色許可權，讓您能夠自行執行。 這適用于您的組織和 it 內的角色。