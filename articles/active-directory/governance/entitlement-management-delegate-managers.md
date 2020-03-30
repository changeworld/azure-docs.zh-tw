---
title: 將訪問治理委派到訪問 Azure AD 授權管理中的包管理器 - Azure 活動目錄
description: 瞭解如何將訪問治理委派給 IT 管理員以訪問包經理和專案經理，以便他們能夠自行管理訪問。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174365"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>將訪問治理委派給 Azure AD 授權管理中的包管理器

要委託目錄中訪問包的創建和管理，可以將使用者添加到訪問包管理器角色。 訪問包管理員必須熟悉使用者請求訪問目錄中資源的需求。 例如，如果目錄用於專案，則專案潛在客戶可能是該目錄的訪問包管理器。  訪問包管理器無法將資源添加到目錄中，但他們可以管理目錄中的訪問包和策略。  然後，當委派給訪問包管理器時，此人可以負責：

- 使用者對目錄中的資源將具有什麼角色
- 誰需要訪問
- 誰需要批准訪問請求
- 專案將持續多長時間

本視頻概述了如何將訪問治理從目錄擁有者委派為訪問包管理器。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>作為目錄擁有者，委託給訪問包管理器

按照以下步驟將使用者分配給訪問包管理器角色：

**先決條件角色：** 全域管理員、使用者管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"目錄"，** 然後打開要向其添加管理員的目錄。

1. 在左側功能表中，按一下 **"角色和管理員**"。

    ![編目角色和管理員](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 按一下"**添加訪問包管理器**"以選擇這些角色的成員。

1. 按一下"**選擇"** 以添加這些成員。

## <a name="remove-an-access-package-manager"></a>刪除訪問包管理器

按照以下步驟從訪問包管理器角色中刪除使用者：

**先決條件角色：** 全域管理員、使用者管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"目錄"，** 然後打開要向其添加管理員的目錄。

1. 在左側功能表中，按一下 **"角色和管理員**"。

1. 在要刪除的訪問包管理器旁邊添加核取記號。

1. 按一下 [移除]****。

## <a name="next-steps"></a>後續步驟

- [建立新的存取套件](entitlement-management-access-package-create.md)
