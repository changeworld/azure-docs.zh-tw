---
title: 將存取管理委派給 Azure AD 權利管理中的目錄建立者-Azure Active Directory
description: 瞭解如何將 IT 系統管理員的存取管理委派給目錄建立者和專案管理人員，讓他們可以自行管理存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77120188"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中將存取管理委派給目錄建立者

目錄是資源和存取封裝的容器。 當您想要將相關的資源和存取封裝分組時，請建立類別目錄。 根據預設，全域管理員或使用者系統管理員可以[建立目錄](entitlement-management-catalog-create.md)，也可以將其他使用者新增為目錄擁有者。

若要委派給不是系統管理員的使用者，讓他們可以建立自己的目錄，您可以將這些使用者新增至 Azure AD 權利管理定義的目錄建立者角色。 您可以新增個別使用者，也可以新增群組，其成員接著可以建立目錄。  建立類別目錄之後，他們就可以將自己擁有的資源新增至其目錄。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>身為 IT 系統管理員，委派給目錄建立者

請遵循下列步驟，將使用者指派給目錄建立者角色。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表的 [**權利管理**] 區段中，按一下 [**設定**]。

1. 按一下 **[編輯]**。

    ![新增目錄建立者的設定](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在 [**委派權利管理**] 區段中，按一下 [**新增類別目錄建立者**]，選取您要委派此權利管理角色的使用者或群組。

1. 按一下 [選取]。 

1. 按一下 [檔案]  。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允許委派的角色存取 Azure 入口網站

若要允許委派的角色（例如目錄建立者和存取套件管理員）存取 Azure 入口網站以管理存取套件，您應該檢查系統管理入口網站設定。

**必要角色：** 全域管理員或使用者管理員

1. 在 [Azure 入口網站中，按一下 [ **Azure Active Directory**然後按一下 [**使用者**]。

1. 在左側功能表中，按一下 [**使用者設定**]。

1. 請確定**Azure AD 系統管理入口網站的 [限制存取**] 設定為 [**否**]。

    ![Azure AD 使用者設定-系統管理入口網站](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>後續步驟

- [建立和管理資原始目錄](entitlement-management-catalog-create.md)
- [委派存取管理以存取封裝管理員](entitlement-management-delegate-managers.md)

