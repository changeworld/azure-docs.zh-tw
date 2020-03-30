---
title: 將訪問治理委派給 Azure AD 授權管理中的目錄建立者 - Azure 活動目錄
description: 瞭解如何將訪問治理從 IT 管理員委派給目錄建立者和專案經理，以便他們可以自行管理存取權限。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120188"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中將訪問治理委派給目錄建立者

目錄是資源和訪問包的容器。 如果要對相關資源和訪問包進行分組，即可創建目錄。 預設情況下，全域管理員或使用者管理員可以[創建目錄](entitlement-management-catalog-create.md)，並可以添加其他使用者作為目錄擁有者。

要委派給非管理員的使用者，以便他們可以創建自己的目錄，可以將這些使用者添加到 Azure AD 授權管理定義的目錄建立者角色。 您可以添加單個使用者，也可以添加組，然後其成員就可以創建目錄。  創建目錄後，他們可以隨後將自己擁有的資源添加到其目錄中。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>作為 IT 管理員，委派給目錄建立者

按照以下步驟將使用者分配給目錄建立者角色。

**先決條件角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，在 **"授權管理**"部分中，按一下 **"設置**"。

1. 按一下 **[編輯]**。

    ![添加目錄建立者的設置](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在 **"委派授權管理**"部分中，按一下"**添加目錄建立者**"以選擇要委派此授權管理角色的使用者或組。

1. 按一下 **"選擇**"。

1. 按一下 [儲存]****。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允許委派的角色訪問 Azure 門戶

要允許委派的角色（如目錄建立者和訪問包管理器）訪問 Azure 門戶以管理訪問包，應檢查監管中心設置。

**先決條件角色：** 全域管理員或使用者管理員

1. 在 Azure 門戶中，按一下**Azure 活動目錄**，然後按一下 **"使用者**"。

1. 在左側功能表中，按一下 **"使用者設置**"。

1. 確保**對 Azure AD 監管中心的存取權限**設置為 **"否**"。

    ![Azure AD 使用者設置 - 監管中心](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>後續步驟

- [創建和管理資原始目錄](entitlement-management-catalog-create.md)
- [將訪問治理委派給訪問包管理器](entitlement-management-delegate-managers.md)

