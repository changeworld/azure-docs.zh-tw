---
title: 建立 & 管理 [權利管理] 中的資原始目錄-Azure AD
description: 瞭解如何在 Azure Active Directory 權利管理中建立新的資源容器和存取封裝。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16a9c8ddfbc2c2a45067080dc68a759de226591b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339574"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中建立和管理資原始目錄

## <a name="create-a-catalog"></a>建立目錄

目錄是資源和存取封裝的容器。 當您想要將相關的資源和存取封裝分組時，請建立類別目錄。 建立類別目錄的人員會成為第一個目錄擁有者。 目錄擁有者可以新增其他目錄擁有者。

**先決條件角色：** 全域管理員、使用者系統管理員或目錄建立者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]。

    ![Azure 入口網站中的權利管理目錄](./media/entitlement-management-catalog-create/catalogs.png)

1. 按一下 [**新增目錄**]。

1. 輸入目錄的唯一名稱，並提供描述。

    使用者會在存取套件的詳細資料中看到這項資訊。

1. 如果您想要讓使用者在建立此目錄中的存取套件時可以立即要求，請將 [**啟用**] 設定為 **[是]**。

1. 如果您想要允許選取的外部目錄中的使用者能夠要求此目錄中的存取封裝，請將 [**為外部使用者啟用**] 設為 **[是]**。

    ![新增目錄窗格](./media/entitlement-management-shared/new-catalog.png)

1. 按一下 [**建立**] 以建立類別目錄。

### <a name="creating-a-catalog-programmatically"></a>以程式設計方式建立目錄

您也可以使用 Microsoft Graph 建立目錄。  具有具有委派許可權之應用程式的適當角色中的使用者， `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來[建立 accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)。

## <a name="add-resources-to-a-catalog"></a>將資源新增至目錄

若要在存取封裝中包含資源，資源必須存在於目錄中。 您可以新增的資源類型包括 [群組]、[應用程式] 和 [SharePoint Online 網站]。 這些群組可以是雲端建立的 Microsoft 365 群組或雲端建立的 Azure AD 安全性群組。 應用程式可以 Azure AD 企業應用程式，包括 SaaS 應用程式和您自己的應用程式（與 Azure AD 聯盟）。 網站可以是 SharePoint Online 網站或 SharePoint Online 網站集合。

**先決條件角色：** 請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要新增資源的目錄。

1. 在左側功能表中，按一下 [**資源**]。

1. 按一下 [**新增資源**]。

1. 按一下 [資源類型]： [**群組和小組**]、[**應用程式**] 或 [ **SharePoint 網站**]。

    如果您沒有看到想要新增的資源，或無法新增資源，請確定您擁有必要的 Azure AD directory 角色和權利管理角色。 您可能需要具有必要角色的人員，才能將資源新增至您的目錄。 如需詳細資訊，請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 選取您要新增至目錄之類型的一或多個資源。

    ![將資源新增至目錄](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成後，按一下 [**新增**]。

    這些資源現在可以包含在目錄內的存取封裝中。

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>以程式設計方式將資源新增至目錄

您也可以使用 Microsoft Graph 將資源新增至目錄。  適當角色中的使用者或目錄和資源擁有者，以及具有委派許可權的應用程式， `EntitlementManagement.ReadWrite.All` 都可以呼叫 API 來[建立 accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)。

## <a name="remove-resources-from-a-catalog"></a>從目錄中移除資源

您可以從目錄中移除資源。 只有當資源未在目錄的任何存取套件中使用時，才可以從目錄中移除它。

**先決條件角色：** 請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要從中移除資源的目錄。

1. 在左側功能表中，按一下 [**資源**]。

1. 選取您想要移除的資源。

1. 按一下 [**移除**] （或按一下省略號（**...**），然後按一下 [**移除資源**]）。

## <a name="add-additional-catalog-owners"></a>新增其他目錄擁有者

建立目錄的使用者會成為第一個目錄擁有者。 若要委派目錄的管理，您可以將使用者新增至目錄擁有者角色。 這有助於分享目錄管理責任。 

請遵循下列步驟，將使用者指派給目錄擁有者角色：

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中，按一下 [**角色和系統管理員**]。

    ![目錄角色和系統管理員](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 按一下 [**新增擁有**者]，以選取這些角色的成員。

1. 按一下 [**選取**] 以加入這些成員。

## <a name="edit-a-catalog"></a>編輯目錄

您可以編輯目錄的名稱和描述。 使用者會在存取套件的詳細資料中看到這項資訊。

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要編輯的目錄。

1. 在目錄的 **[總覽**] 頁面上，按一下 [**編輯**]。

1. 編輯目錄的 [名稱]、[描述] 或 [已啟用] 設定。

    ![編輯目錄設定](./media/entitlement-management-shared/catalog-edit.png)

1. 按一下 [檔案] 。

## <a name="delete-a-catalog"></a>刪除目錄

您可以刪除類別目錄，但只有在它沒有任何存取套件時才可刪除。

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要刪除的目錄。

1. 在目錄的**總覽**中，按一下 [**刪除**]。

1. 在出現的訊息方塊中，按一下 [確定]****。

### <a name="deleting-a-catalog-programmatically"></a>以程式設計方式刪除目錄

您也可以使用 Microsoft Graph 刪除目錄。  具有具有委派許可權之應用程式的適當角色中的使用者， `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來[刪除 accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)。

## <a name="next-steps"></a>後續步驟

- [委派存取管理以存取封裝管理員](entitlement-management-delegate-managers.md)
