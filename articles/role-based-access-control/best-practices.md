---
title: Azure RBAC 的最佳做法
description: 使用 Azure 角色型存取控制（Azure RBAC）的最佳作法。
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
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735329"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC 的最佳做法

本文說明使用 Azure 角色型存取控制（Azure RBAC）的一些最佳作法。 這些最佳作法衍生自我們的 Azure RBAC 經驗和客戶的經驗。

## <a name="only-grant-the-access-users-need"></a>僅授與使用者所需的存取權

Azure RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許在特定範圍執行特定的動作。

在規劃存取控制策略時，最佳做法是授與使用者完成其工作的最低權限。 下圖顯示使用 Azure RBAC 的建議模式。

![Azure RBAC 和最低許可權](./media/best-practices/rbac-least-privilege.png)

如需有關如何新增角色指派的詳細資訊，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)。

## <a name="limit-the-number-of-subscription-owners"></a>限制訂用帳戶擁有者的數目

您最多可以有3個訂用帳戶擁有者，以降低受到遭入侵之擁有者入侵的可能性。 Azure 資訊安全中心可以監視這項建議。 如需資訊安全中心中的其他身分識別和存取建議，請參閱[安全性建議-參考指南](../security-center/recommendations-reference.md)。

## <a name="use-azure-ad-privileged-identity-management"></a>使用 Azure AD Privileged Identity Management

若要保護具特殊權限的帳戶免受惡意網路攻擊的威脅，您可以使用 Azure Active Directory Privileged Identity Management (PIM) 來降低權限的曝光時間，並透過報告和警示提升您對其使用情況的可見度。 PIM 藉由提供 Azure AD 和 Azure 資源的即時特殊許可權存取來協助保護特殊許可權帳戶。 存取權可以是時間界限，在這之後會自動撤銷許可權。 

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。

## <a name="next-steps"></a>後續步驟

- [針對 Azure RBAC 進行疑難排解](troubleshooting.md)