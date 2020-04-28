---
title: 委派存取治理以存取 Azure AD 權利管理中的套件管理員-Azure Active Directory
description: 瞭解如何將存取管理委派給 IT 系統管理員，以存取封裝管理員和專案管理人員，讓他們可以自行管理存取權。
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73174365"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>委派存取治理以存取 Azure AD 權利管理中的套件管理員

若要委派目錄中的存取封裝建立和管理，您可以將使用者新增至 [存取套件管理員] 角色。 存取套件管理員必須熟悉使用者對目錄中的資源要求存取權的需求。 例如，如果專案使用目錄，則專案負責人可能是該目錄的存取套件管理員。  存取套件管理員無法將資源新增至目錄，但是可以管理目錄中的存取封裝和原則。  委派給存取套件管理員時，該人員可以負責：

- 使用者對目錄中的資源有哪些角色
- 需要存取權的人員
- 誰需要核准存取要求
- 專案會持續多久

這段影片概述如何從目錄擁有者委派存取權管理，以存取封裝管理員。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>身為目錄擁有者，委派給存取套件管理員

請遵循下列步驟，將使用者指派給存取套件管理員角色：

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中，按一下 [**角色和系統管理員**]。

    ![目錄角色和系統管理員](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 按一下 [**新增存取套件管理員**]，以選取這些角色的成員。

1. 按一下 [**選取**] 以加入這些成員。

## <a name="remove-an-access-package-manager"></a>移除存取套件管理員

請遵循下列步驟，從存取套件管理員角色中移除使用者：

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**目錄**]，然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中，按一下 [**角色和系統管理員**]。

1. 在您想要移除的存取套件管理員旁新增核取記號。

1. 按一下 [移除]****。

## <a name="next-steps"></a>後續步驟

- [建立新的存取套件](entitlement-management-access-package-create.md)
