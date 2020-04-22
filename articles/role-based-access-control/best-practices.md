---
title: Azure RBAC 的最佳實務
description: 使用基於 Azure 角色的訪問控制 (Azure RBAC) 的最佳做法。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726770"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC 的最佳實務

本文介紹使用基於 Azure 角色的存取控制 (Azure RBAC) 的一些最佳實務。 這些最佳實踐源自我們在 Azure RBAC 方面的經驗以及您這樣的客戶的體驗。

## <a name="only-grant-the-access-users-need"></a>只被使用使用者的權限

使用 Azure RBAC,可以隔離團隊中的職責,並僅向使用者授予執行作業所需的訪問許可權量。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許在特定範圍執行特定的動作。

在規劃存取控制策略時，最佳做法是授與使用者完成其工作的最低權限。 下圖顯示使用 RBAC 的建議模式。

![RBAC 和最低權限](./media/best-practices/rbac-least-privilege.png)

有關如何新增角色分配的資訊,請參閱[新增或刪除角色分配](role-assignments-portal.md)。

## <a name="limit-the-number-of-subscription-owners"></a>限制訂閱擁有者的數量

您最多應擁有 3 個訂閱擁有者,以減少被洩露的擁有者違反的可能性。 可以在 Azure 安全中心監視此建議。 有關安全中心中的其他標識和訪問建議,請參閱[安全建議 - 參考指南](../security-center/recommendations-reference.md)。

## <a name="use-azure-ad-privileged-identity-management"></a>使用 Azure AD 特權識別管理

若要保護具特殊權限的帳戶免受惡意網路攻擊的威脅，您可以使用 Azure Active Directory Privileged Identity Management (PIM) 來降低權限的曝光時間，並透過報告和警示提升您對其使用情況的可見度。 PIM 透過提供對 Azure AD 和 Azure 資源的及時特權存取,幫助保護特權帳戶。 訪問可以受時限限制,之後許可權將自動撤銷。 

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。

## <a name="next-steps"></a>後續步驟

- [排除 Azure RBAC 故障](troubleshooting.md)