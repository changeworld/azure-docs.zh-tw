---
title: 與他人協作 - LUIS
titleSuffix: Azure Cognitive Services
description: 應用擁有者可以向創作資源添加參與者。 這些參與者可以修改模型、訓練和發佈應用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053450"
---
# <a name="add-contributors-to-your-app"></a>向應用添加參與者

應用擁有者可以將參與者添加到應用。 這些共同作業者可以修改模型、訓練及發佈應用程式。 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>向 Azure 創作資源添加參與者

以下過程適用于**已遷移**以使用 Azure 創作資源的所有使用者。

如果 LUIS 創作體驗與 LUIS 門戶中的 **"管理-> Azure 資源**"頁上的創作資源相關聯，則已遷移。

1. 在 Azure 門戶中，查找語言理解 （LUIS） 創作資源。 它有類型`LUIS.Authoring`。
1. 在此資源的 **"存取控制 （IAM）"** 頁上，選擇 **"添加"** 然後選擇 **"添加角色指派**"。

    ![在 Azure 門戶中，在創作資源上添加角色指派。](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. 在 **"添加角色指派"** 視窗中，選擇"參與者**的角色**"。 在"**分配對選項的訪問**"中，選擇**Azure AD 使用者、組或服務主體**。 在 **"選擇"** 選項中，輸入使用者的電子郵件地址。 如果同一域的 1 個以上電子郵件地址知道該使用者，請確保輸入_主_電子郵件帳戶。

    ![將使用者的電子郵件添加到 Azure AD 的參與者角色](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    找到使用者的電子郵件後，選擇帳戶並選擇"**保存**"。 

    如果在此角色指派時遇到問題，請查看[Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)和[Azure 存取控制故障排除](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)。

## <a name="add-collaborator-to-luis-app"></a>將協作者添加到 LUIS 應用

以下過程適用于**尚未遷移**以使用 Azure 創作資源的所有使用者。

如果您的 LUIS 創作體驗未綁定到 LUIS 門戶中的 **"管理-> Azure 資源**"頁上的創作資源，則您尚未遷移。

一個應用程式只有單一建立者 (擁有者)，但可以有許多共同作業者。 若要允許共同作業者編輯您的 LUIS 應用程式，您必須將他們用來存取 LUIS 入口網站的電子郵件新增至共同作業者清單。 新增之後，應用程式就會顯示在其 LUIS 入口網站中。

1. 從右上方功能表中選取 [管理]****，然後選取左側功能表中的 [共同作業者]****。

1. 從工具列中選取 [新增共同作業者]****。

1. 輸入共同作業者用來登入 LUIS 入口網站的電子郵件地址。

    ![新增共同作業者的電子郵件地址](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>具有多封電子郵件的使用者 

如果將參與者/協作者添加到 LUIS 應用，則指定確切的電子郵件地址。 雖然 Azure 活動目錄 （Azure AD） 允許單個使用者可互換使用多個電子郵件帳戶，但 LUIS 要求使用者使用添加參與者/協作者時指定的電子郵件地址登錄。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 資源

如果您在組織中使用 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD)，Language Understanding (LUIS) 需要具備使用者在想要使用 LUIS 時所存取資訊的權限。 LUIS 需要的資源非常少。 

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
* 第二個解決方案是使用[Microsoft Graph 中的 Azure AD 標識和訪問管理 API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview)向每個特定使用者提供同意。 

深入了解 Azure Active Directory 使用者和同意： 
* [將您的應用程式限制](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)為僅供一組使用者存取

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用版本](luis-how-to-manage-versions.md)來控制應用生命週期。
* 瞭解包括編寫[資源](luis-concept-keys.md#authoring-key)以及[該資源貢獻者在內的](luis-concept-keys.md#contributions-from-other-authors)概念。
* [瞭解如何創建](luis-how-to-azure-subscription.md)創作和運行時資源
* 遷移到新的[創作資源](luis-migration-authoring.md) 
