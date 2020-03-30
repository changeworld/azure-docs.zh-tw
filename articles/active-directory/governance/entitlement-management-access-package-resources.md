---
title: 更改 Azure AD 授權管理中訪問包的資源角色 - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄授權管理中更改現有訪問包的資源角色。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261888"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>更改 Azure AD 授權管理中訪問包的資源角色

作為訪問包管理器，您可以隨時更改訪問包中的資源，而不必擔心預配使用者對新資源的訪問，或從以前的資源中刪除其存取權限。 本文介紹如何更改現有訪問包的資源角色。

本視頻概述了如何更改訪問包。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>檢查目錄資源

如果需要將資源添加到訪問包，則應檢查目錄中所需的資源是否可用。 如果您是訪問包管理器，則無法將資源添加到目錄，即使您擁有這些資源也是如此。 您僅限於使用目錄中可用的資源。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"目錄"，** 然後打開目錄。

1. 在左側功能表中，按一下 **"資源"** 以查看此目錄中的資源清單。

    ![目錄中的資源清單](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 如果您是訪問包管理器，並且需要向目錄添加資源，則可以要求目錄擁有者添加這些資源。

## <a name="add-resource-roles"></a>添加資源角色

資源角色是與資源關聯的許可權的集合。 使資源可供使用者請求的方式是向訪問包添加資源角色。 您可以為組、團隊、應用程式和 SharePoint 網站添加資源角色。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在左側功能表中，按一下 **"資源角色**"。

1. 按一下 **"添加資源角色**"以打開"添加資源角色以訪問包"頁。

    ![訪問包 - 添加資源角色](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 根據是否要添加組、團隊、應用程式或 SharePoint 網站，請執行以下資源角色部分之一中的步驟。

## <a name="add-a-group-or-team-resource-role"></a>添加組或團隊資源角色

當使用者被分配訪問包時，您可以讓授權管理自動將使用者添加到 Microsoft Teams 中的組或團隊中。 

- 當組或團隊是訪問包的一部分，並且使用者被分配到該訪問包時，使用者將添加到該組或團隊中（如果尚未存在）。
- 當使用者的訪問包分配到期時，他們將從組或團隊中刪除，除非他們當前已分配到包含同一組或團隊的另一個訪問包。

您可以選擇任何[Azure AD 安全性群組或 Office 365 組](../fundamentals/active-directory-groups-create-azure-portal.md)。 管理員可以將任何組添加到目錄中;但是，管理員可以將任何組添加到目錄中。目錄擁有者可以將任何組添加到目錄（如果他們是組的擁有者）。 選擇組時，請記住以下 Azure AD 約束：

- 當使用者（包括來賓）作為成員添加到組或團隊時，他們可以看到該組或團隊的所有其他成員。
- Azure AD 無法更改使用 Azure AD 連接從 Windows 伺服器活動目錄同步的組的成員身份，或者在 Exchange Online 中作為通訊組創建的組的成員身份。  
- 動態組的成員身份無法通過添加或刪除成員來更新，因此動態組成員身份不適合用于授權管理。

有關詳細資訊，請參閱[比較組](https://docs.microsoft.com/office365/admin/create-groups/compare-groups)和[Office 365 組和 Microsoft 團隊](https://docs.microsoft.com/microsoftteams/office-365-groups)。

1. 在"**添加資源角色以訪問包**"頁上，按一下 **"組和團隊**"以打開"選擇組"窗格。

1. 選擇要包含在訪問包中的組和團隊。

    ![訪問包 - 添加資源角色 - 選擇組](./media/entitlement-management-access-package-resources/group-select.png)

1. 按一下 **"選擇**"。

    選擇組或團隊後，"**子類型"** 列將列出以下子類型之一：

    |  |  |
    | --- | --- |
    | 安全性 | 用於授予對資源的訪問。 |
    | 散發 | 用於向一組人員發送通知。 |
    | O365 | 未啟用團隊的 Office 365 組。 用於公司內外使用者之間的協作。 |
    | 小組 | 啟用團隊的 Office 365 組。 用於公司內外使用者之間的協作。 |

1. 在**角色**清單中，選擇 **"擁有者**"或 **"成員**"。

    您通常選擇"成員"角色。 如果選擇"擁有者"角色，則該角色將允許使用者添加或刪除其他成員或擁有者。

    ![訪問包 - 為組或團隊添加資源角色](./media/entitlement-management-access-package-resources/group-role.png)

1. 按一下 **[新增]**。

    對訪問包具有現有分配的任何使用者在添加訪問包時將自動成為此組或團隊的成員。

## <a name="add-an-application-resource-role"></a>添加應用程式資源角色

您可以讓 Azure AD 自動將使用者訪問分配給 Azure AD 企業應用程式，包括 SaaS 應用程式和將組織的應用程式聯合到 Azure AD，此時為使用者分配了訪問包。 對於通過聯合單一登入與 Azure AD 集成的應用程式，Azure AD 將為分配給應用程式的使用者頒發聯合權杖。

應用程式可以有多個角色。 將應用程式添加到訪問包時，如果該應用程式具有多個角色，則需要為這些使用者指定適當的角色。 如果您正在開發應用程式，則可以在["如何：配置企業應用程式的 SAML 權杖中頒發的角色聲明](../develop/active-directory-enterprise-app-role-management.md)"中詳細瞭解如何將這些角色添加到應用程式中。

一旦應用程式角色成為訪問包的一部分：

- 當為使用者分配該訪問包時，如果使用者尚未存在，則該使用者將添加到該應用程式角色。
- 當使用者的訪問包分配到期時，他們的存取權限將從應用程式中刪除，除非他們對包含該應用程式角色的另一個訪問包有分配。

以下是選擇應用程式時的一些注意事項：

- 應用程式也可能將組分配給其角色。  您可以選擇在訪問包中添加組以代替應用程式角色，但是應用程式在"我的訪問"門戶中作為訪問包的一部分對使用者不可見。

1. 在"**添加資源角色以訪問包**"頁上，按一下"**應用程式**"以打開"選擇應用程式"窗格。

1. 選擇要包含在訪問包中的應用程式。

    ![訪問包 - 添加資源角色 - 選擇應用程式](./media/entitlement-management-access-package-resources/application-select.png)

1. 按一下 **"選擇**"。

1. 在**角色**清單中，選擇應用程式角色。

    ![訪問包 - 為應用程式添加資源角色](./media/entitlement-management-access-package-resources/application-role.png)

1. 按一下 **[新增]**。

    任何對訪問包具有現有分配的使用者在添加訪問包時將自動獲得對此應用程式的存取權限。

## <a name="add-a-sharepoint-site-resource-role"></a>添加 SharePoint 網站資源角色

Azure AD 可以自動分配使用者訪問 SharePoint 連線網站或 SharePoint 連線網站集時，他們分配了訪問包。

1. 在"**添加資源角色以訪問包**"頁上，按一下 **"共用點"網站**以打開"選擇共用點連線網站"窗格。

1. 選擇要包含在訪問包中的 SharePoint 連線網站。

    ![訪問包 - 添加資源角色 - 選擇共用點連線網站](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. 按一下 **"選擇**"。

1. 在**角色**清單中，選擇 SharePoint 連線網站角色。

    ![訪問包 - 為 SharePoint 連線網站添加資源角色](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. 按一下 **[新增]**。

    任何已分配到訪問包的使用者在添加此 SharePoint Online 網站時將自動獲得存取權限。

## <a name="remove-resource-roles"></a>刪除資源角色

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在左側功能表中，按一下 **"資源角色**"。

1. 在資源角色清單中，找到要刪除的資源角色。

1. 按一下省略號 （**...），** 然後按一下 **"刪除資源角色**"。

    具有訪問包現有分配的任何使用者在刪除訪問包時將自動吊銷對此資源角色的存取權限。

## <a name="when-changes-are-applied"></a>應用更改時

在授權管理中，Azure AD 將每天處理多次訪問包中分配和資源的批量更改。 因此，如果您分配或更改訪問包的資源角色，則在 Azure AD 中進行該更改最多可能需要 24 小時，以及將這些更改傳播到其他 Microsoft 線上服務或連接的 SaaS 所需的時間應用。 如果更改僅影響幾個物件，則更改可能只需幾分鐘即可在 Azure AD 中應用，之後其他 Azure AD 元件將檢測該更改並更新 SaaS 應用程式。 如果更改影響數千個物件，則更改將需要更長的時間。 例如，如果您有一個包含 2 個應用程式和 100 個使用者分配的訪問包，並且決定向訪問包添加 SharePoint 網站角色，則可能會延遲，直到所有使用者都是該 SharePoint 網站角色的一部分。 您可以通過 Azure AD 稽核記錄、Azure AD 預配日誌和 SharePoint 網站稽核記錄監視進度。

移除小組成員時，也會從 Office 365 群組中將其移除。 移除小組的交談功能方面可能會延遲。 有關詳細資訊，請參閱[組成員身份](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 建立基本群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
- [操作說明：針對企業應用程式，設定 SAML 權杖中發出的角色宣告](../develop/active-directory-enterprise-app-role-management.md)
- [線上分享點簡介](/sharepoint/introduction)