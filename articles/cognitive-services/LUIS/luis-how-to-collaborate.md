---
title: 與其他人共同作業-LUIS
titleSuffix: Azure Cognitive Services
description: 應用程式擁有者可以將參與者新增至撰寫資源。 這些參與者可以修改模型、定型及發佈應用程式。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: d4bde21dd13b562ffbb51b27ef083ee53685397a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007956"
---
# <a name="add-contributors-to-your-app"></a>將參與者新增至您的應用程式

應用程式擁有者可以將參與者新增至應用程式。 這些參與者可以修改模型、定型及發佈應用程式。 [遷移](luis-migration-authoring.md)您的帳戶之後，就會在撰寫資源的 Azure 入口網站中管理 _參與者_，並使用 **存取控制 (IAM)** 頁面。 使用共同作業者的電子郵件地址和 _參與者_ 角色來新增使用者。

## <a name="add-contributor-to-azure-authoring-resource"></a>將參與者新增至 Azure 撰寫資源

如果您的 LUIS 撰寫體驗系結至 LUIS 入口網站中 [ **管理-> Azure 資源** ] 頁面上的撰寫資源，則您已遷移。

1. 在 Azure 入口網站中，尋找 Language Understanding (LUIS) 編寫資源。 它具有類型 `LUIS.Authoring` 。
1. 在此資源的 **存取控制 (IAM)** ] 頁面上，選取 [ **+ 新增** ]，然後選取 [ **新增角色指派**]。

    ![在 Azure 入口網站中，新增撰寫資源的角色指派。](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. 在 [ **新增角色指派** ] 視窗中，選取 [參與者] **角色** 。 在 [ **指派存取權** ] 選項中，選取 [ **使用者、群組或服務主體 Azure AD**]。 在 [ **選取** ] 選項中，輸入使用者的電子郵件地址。 如果相同網域的使用者超過1個電子郵件地址，請務必輸入 _主要_ 電子郵件帳戶。

    ![將使用者的電子郵件新增至 Azure AD 的參與者角色](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    找到使用者的電子郵件時，請選取帳戶，然後選取 [ **儲存**]。

    如果您有此角色指派的問題，請參閱 [azure 角色指派](../../role-based-access-control/role-assignments-portal.md) 和 [azure 存取控制疑難排解](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)。

## <a name="view-the-app-as-a-contributor"></a>以參與者的形式來查看應用程式

當您新增為參與者之後，請登 [入 LUIS 入口網站](sign-in-luis-portal.md)。

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>具有多個電子郵件的使用者

如果您將參與者新增至 LUIS 應用程式，則會指定確切的電子郵件地址。 雖然 Azure Active Directory (Azure AD) 讓單一使用者能以交替的方式使用多個電子郵件帳戶，LUIS 需要使用者以新增參與者時指定的電子郵件地址登入。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 資源

如果您在組織中使用 [Azure Active Directory](../../active-directory/index.yml) (Azure AD)，Language Understanding (LUIS) 需要具備使用者在想要使用 LUIS 時所存取資訊的權限。 LUIS 需要的資源非常少。

當您嘗試使用具有管理員同意或不需要管理員同意的帳戶註冊時，您會看到詳細的描述，例如系統管理員同意：

* 允許您使用貴組織帳戶來登入應用程式，並且讓應用程式讀取您的設定檔。 它也允許應用程式讀取基本公司資訊。 這讓 LUIS 有權讀取基本設定檔資料，例如使用者識別碼、電子郵件、名稱。
* 允許應用程式查看並更新您的資料，即使您目前並未使用該應用程式。 需要有此權限，才能重新整理使用者的存取權杖。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租用戶使用者

LUIS 使用標準 Azure Active Directory (Azure AD) 同意流程。

租用戶系統管理員應該與需要被授權允許在 Azure AD 中使用 LUIS 的使用者直接合作。

* 首先，使用者登入 LUIS，然後看到一個需要系統管理員核准的快顯對話方塊。 使用者會先連絡租用戶系統管理員，然後才能繼續後面的步驟。
* 第二，租用戶系統管理員登入 LUIS，然後看到一個同意流程快顯對話方塊。 系統管理員必須在這個對話方塊中授與權限給使用者。 當系統管理員接受權限後，使用者便能繼續使用 LUIS。 如果租用戶管理員將不會登入 LUIS，該管理員就可以存取 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications) (如下列螢幕擷取畫面所示)。 請注意，會將此清單篩選出包含 `LUIS` 名稱的項目。

![依應用程式網站分類的 Azure Active Directory 權限](./media/luis-how-to-collaborate/tenant-permissions.png)

如果租用戶系統管理員只希望特定使用者使用 LUIS，則有一些可能的解決方式：
* 提供「管理員同意」(同意 Azure AD 的所有使用者)，然後將 [企業應用程式屬性] 之下的 [需要使用者指派] 設定為 [是]，最後只將想要的使用者指派/新增至應用程式。 使用此方法，系統管理員仍提供應用程式的「管理員同意」，不過，能夠控制可以存取該應用程式的使用者。
* 第二個解決方案是 [在 Microsoft Graph 中使用 Azure AD 身分識別和存取管理 API](/graph/azuread-identity-access-management-concept-overview) ，以提供每個特定使用者的同意。

深入了解 Azure Active Directory 使用者和同意：
* 將[您的應用程式限制](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)為一組使用者

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何使用版本](luis-how-to-manage-versions.md) 來控制您的應用程式生命週期。
* 瞭解包括在該資源上 [撰寫資源](luis-how-to-azure-subscription.md#authoring-key) 和 [參與者](luis-how-to-azure-subscription.md#contributions-from-other-authors) 的概念。
* 瞭解 [如何建立](luis-how-to-azure-subscription.md) 撰寫和執行時間資源
* 遷移至新的 [撰寫資源](luis-migration-authoring.md)