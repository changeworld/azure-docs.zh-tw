---
title: 將存取治理委派給 Azure AD 權利管理中的目錄建立者-Azure Active Directory
description: 瞭解如何將「存取治理」從 IT 系統管理員委派給目錄建立者和專案經理，讓他們可以自行管理存取權。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798421"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD 權利管理將存取治理委派給目錄建立者

目錄是資源和存取套件的容器。 當您想要將相關資源和存取套件分組時，請建立目錄。 根據預設，全域系統管理員或使用者系統管理員可以 [建立目錄](entitlement-management-catalog-create.md)，並可將其他使用者新增為目錄擁有者。

若要委派給非系統管理員的使用者，讓他們可以建立自己的目錄，您可以將這些使用者新增至 Azure AD 權利管理定義的目錄建立者角色。 您可以新增個別使用者，也可以新增群組，其成員接著可以建立目錄。  建立目錄之後，他們就可以將他們擁有的資源新增至其目錄。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>以 IT 系統管理員的身分，委派給目錄建立者

請遵循下列步驟，將使用者指派給目錄建立者角色。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表的 [ **權利管理** ] 區段中，按一下 [ **設定**]。

1. 按一下 **[編輯]** 。

    ![新增目錄建立者的設定](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在 [ **委派權利管理** ] 區段中，按一下 [ **新增目錄建立者** ]，選取您要委派此權利管理角色的使用者或群組。

1. 按一下 [選取]。 

1. 按一下 **[儲存]** 。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允許委派的角色存取 Azure 入口網站

若要允許委派的角色（例如目錄建立者和存取套件管理員）存取 Azure 入口網站來管理存取套件，您應該檢查系統管理入口網站設定。

**必要角色：** 全域管理員或使用者管理員

1. 在 [Azure 入口網站中，按一下 [ **Azure Active Directory** ]，然後按一下 [ **使用者**]。

1. 在左側功能表中，按一下 [ **使用者設定**]。

1. 請確定 [ **限制存取 Azure AD 系統管理入口網站** ] 設定為 [ **否**]。

    ![Azure AD 使用者設定-系統管理入口網站](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>後續步驟

- [建立及管理資源的類別目錄](entitlement-management-catalog-create.md)
- [委派存取治理以存取套件管理員](entitlement-management-delegate-managers.md)

