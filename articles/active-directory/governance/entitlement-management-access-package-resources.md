---
title: 變更 Azure AD 權利管理中存取套件的資源角色-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中變更現有存取套件的資源角色。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261888"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中變更存取套件的資源角色

身為存取套件管理員，您可以隨時變更存取套件中的資源，而不必擔心布建使用者對新資源的存取權，或從先前的資源中移除其存取權。 本文說明如何變更現有存取套件的資源角色。

這段影片提供如何變更存取封裝的總覽。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>檢查資源的類別目錄

如果您需要將資源新增至存取套件，您應該檢查目錄中是否有您所需的資源。 如果您是存取套件管理員，您就無法將資源新增至目錄，即使您擁有它們也一樣。 您只能使用目錄中的可用資源。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**類別目錄**]，然後開啟目錄。

1. 在左側功能表中，按一下 [**資源**] 以查看此目錄中的資源清單。

    ![目錄中的資源清單](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 如果您是存取套件管理員，而您需要將資源新增至目錄，您可以要求類別目錄擁有者加以新增。

## <a name="add-resource-roles"></a>新增資源角色

資源角色是與資源相關聯的許可權集合。 將資源角色新增至您的存取套件，讓使用者可以要求資源的方式。 您可以加入群組、小組、應用程式和 SharePoint 網站的資源角色。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**資源角色**]。

1. 按一下 [**新增資源角色**] 以開啟 [新增資源角色以存取封裝] 頁面。

    ![存取套件-新增資源角色](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 根據您要加入群組、小組、應用程式或 SharePoint 網站而定，請執行下列其中一個資源角色章節中的步驟。

## <a name="add-a-group-or-team-resource-role"></a>加入群組或小組資源角色

當使用者獲指派存取套件時，您可以讓 [權利管理] 自動將使用者新增至 Microsoft 小組中的群組或小組。 

- 當群組或小組屬於存取套件的一部分，且使用者被指派至該存取套件時，使用者就會新增至該群組或小組（如果尚未存在的話）。
- 當使用者的存取套件指派到期時，就會從群組或小組中移除，除非他們目前有指派給另一個包含相同群組或小組的存取封裝。

您可以選取任何[Azure AD 的安全性群組或 Office 365 群組](../fundamentals/active-directory-groups-create-azure-portal.md)。 系統管理員可以將任何群組新增至目錄;如果目錄擁有者是群組的擁有者，則可以將任何群組新增至目錄。 選取群組時，請記住下列 Azure AD 條件約束：

- 當使用者（包括來賓）新增為群組或小組的成員時，他們可以看到該群組或小組的所有其他成員。
- Azure AD 無法變更從 Windows Server Active Directory 同步處理的群組成員資格，其方式是使用 Azure AD Connect，或在 Exchange Online 中建立為通訊群組。  
- 藉由新增或移除成員，無法更新動態群組的成員資格，因此動態群組成員資格不適合與權利管理搭配使用。

如需詳細資訊，請參閱[比較群組](https://docs.microsoft.com/office365/admin/create-groups/compare-groups)和[Office 365 群組和 Microsoft 小組](https://docs.microsoft.com/microsoftteams/office-365-groups)。

1. 在 [**新增資源角色以存取封裝**] 頁面上，按一下 [**群組和小組**] 以開啟 [選取群組] 窗格。

1. 選取您想要包含在存取套件中的群組和小組。

    ![存取套件-新增資源角色-選取群組](./media/entitlement-management-access-package-resources/group-select.png)

1. 按一下 [選取]。 

    一旦您選取群組或小組之後，[**子類型**] 欄將會列出下列其中一個子類型：

    |  |  |
    | --- | --- |
    | 安全性 | 用來授與資源的存取權。 |
    | 散發 | 用來傳送通知給一群人。 |
    | O365 | 未啟用小組的 Office 365 群組。 用於在公司內部和外部的使用者之間共同作業。 |
    | 小組 | 已啟用小組的 Office 365 群組。 用於在公司內部和外部的使用者之間共同作業。 |

1. 在 [**角色**] 清單中，選取 [**擁有**者] 或 [**成員**]。

    您通常會選取成員角色。 如果您選取 [擁有者] 角色，則可讓使用者新增或移除其他成員或擁有者。

    ![存取套件-新增群組或小組的資源角色](./media/entitlement-management-access-package-resources/group-role.png)

1. 按一下 **[新增]** 。

    具有存取封裝之現有指派的任何使用者，在新增時，都會自動成為此群組或小組的成員。

## <a name="add-an-application-resource-role"></a>新增應用程式資源角色

當使用者獲指派存取套件時，您可以讓 Azure AD 自動指派 Azure AD 企業應用程式的存取權給使用者，包括 SaaS 應用程式以及與 Azure AD 同盟的組織應用程式。 針對透過同盟單一登入與 Azure AD 整合的應用程式，Azure AD 會為指派給應用程式的使用者發出同盟權杖。

應用程式可以有多個角色。 將應用程式新增至存取套件時，如果該應用程式有一個以上的角色，您必須為這些使用者指定適當的角色。 如果您正在開發應用程式，您可以在[如何：設定適用于企業應用程式的 SAML 權杖中發出的角色](../develop/active-directory-enterprise-app-role-management.md)宣告中，深入瞭解如何將這些角色新增至您的應用程式。

一旦應用程式角色是存取套件的一部分：

- 當使用者獲指派該存取套件時，會將使用者新增至該應用程式角色（如果尚未存在的話）。
- 當使用者的存取套件指派過期時，將會從應用程式中移除其存取權，除非他們有指派給另一個包含該應用程式角色的存取封裝。

以下是選取應用程式時的一些考慮：

- 應用程式也可能會有指派給其角色的群組。  您可以選擇新增群組來取代存取套件中的應用程式角色，但是在我的存取權入口網站中，使用者將看不到該應用程式作為存取套件的一部分。

1. 在 [**新增資源角色以存取封裝**] 頁面上，按一下 [**應用程式**] 以開啟 [選取應用程式] 窗格。

1. 選取您想要包含在存取套件中的應用程式。

    ![存取套件-新增資源角色-選取應用程式](./media/entitlement-management-access-package-resources/application-select.png)

1. 按一下 [選取]。 

1. 在 [**角色**] 清單中，選取應用程式角色。

    ![存取套件-新增應用程式的資源角色](./media/entitlement-management-access-package-resources/application-role.png)

1. 按一下 **[新增]** 。

    在新增此應用程式時，任何具有存取封裝之現有指派的使用者都會自動獲得存取權。

## <a name="add-a-sharepoint-site-resource-role"></a>新增 SharePoint 網站資源角色

當使用者獲指派存取套件時，Azure AD 可以自動指派 SharePoint Online 網站或 SharePoint Online 網站集合的存取權。

1. 在 [**將資源角色新增至存取封裝**] 頁面上，按一下 [ **sharepoint 網站**] 以開啟 [選取 sharepoint Online 網站] 窗格。

1. 選取您想要包含在存取套件中的 SharePoint Online 網站。

    ![存取套件-新增資源角色-選取 SharePoint Online 網站](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. 按一下 [選取]。 

1. 在 [**角色**] 清單中，選取 SharePoint Online 網站角色。

    ![存取套件-新增 SharePoint Online 網站的資源角色](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. 按一下 **[新增]** 。

    新增存取套件的任何使用者，將會自動獲得此 SharePoint Online 網站的存取權。

## <a name="remove-resource-roles"></a>移除資源角色

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**資源角色**]。

1. 在資源角色清單中，尋找您想要移除的資源角色。

1. 按一下省略號（**...**），然後按一下 [**移除資源角色**]。

    已移除存取套件之現有指派的任何使用者，將會自動在此資源角色中撤銷其存取權。

## <a name="when-changes-are-applied"></a>套用變更時

在 [權利管理] 中，Azure AD 會一天多次處理存取套件中指派和資源的大量變更。 因此，如果您進行指派，或變更存取套件的資源角色，可能需要24小時才會在 Azure AD 中進行該變更，加上將這些變更傳播到其他 Microsoft Online Services 或連接的 SaaS 應用程式所需的時間量。 如果您的變更只會影響幾個物件，則變更在 Azure AD 中可能只需要幾分鐘的時間，之後其他 Azure AD 元件就會偵測到該變更並更新 SaaS 應用程式。 如果您的變更會影響數以千計的物件，變更將會花費較長的時間。 例如，如果您有一個具有2個應用程式和100使用者指派的存取套件，而且您決定將 SharePoint 網站角色加入至存取封裝，則在所有使用者都屬於該 SharePoint 網站角色的一部分之前，可能會有延遲。 您可以透過 Azure AD 的 audit 記錄檔、Azure AD 布建記錄檔和 SharePoint 網站 audit 記錄來監視進度。

移除小組成員時，也會從 Office 365 群組中將其移除。 移除小組的交談功能方面可能會延遲。 如需詳細資訊，請參閱[群組成員資格](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 建立基本群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
- [操作說明：針對企業應用程式，設定 SAML 權杖中發出的角色宣告](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online 簡介](/sharepoint/introduction)