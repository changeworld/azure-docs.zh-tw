---
title: Azure RBAC 的最佳作法
description: 使用 Azure 角色型存取控制 (Azure RBAC) 的最佳作法。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 00fd00112dad9efc31cea83f69bb458a8e1ca935
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617369"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC 的最佳作法

本文說明使用 Azure 角色型存取控制 (Azure RBAC) 的一些最佳作法。 這些最佳作法衍生自我們的 Azure RBAC 體驗以及客戶的體驗。

## <a name="only-grant-the-access-users-need"></a>只授與使用者所需的存取權

Azure RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許在特定範圍執行特定的動作。

在規劃存取控制策略時，最佳做法是授與使用者完成其工作的最低權限。 即便一開始可能較方便操作，仍應避免以較廣泛的範圍指派較廣泛的角色。 建立自訂角色時，只會包含使用者所需的許可權。 藉由限制角色和範圍，如果安全性主體遭盜用，您可以限制哪些資源有風險。

下圖顯示使用 Azure RBAC 的建議模式。

![Azure RBAC 和最低許可權](./media/best-practices/rbac-least-privilege.png)

如需如何新增角色指派的相關資訊，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)。

## <a name="limit-the-number-of-subscription-owners"></a>限制訂用帳戶擁有者數目

您應擁有最多3個訂用帳戶擁有者，以降低遭盜用的擁有者可能發生缺口。 這項建議可在 Azure 資訊安全中心中監視。 如需資訊安全中心的其他身分識別和存取建議，請參閱 [安全性建議-參考指南](../security-center/recommendations-reference.md)。

## <a name="use-azure-ad-privileged-identity-management"></a>使用 Azure AD Privileged Identity Management

若要保護具特殊權限的帳戶免受惡意網路攻擊的威脅，您可以使用 Azure Active Directory Privileged Identity Management (PIM) 來降低權限的曝光時間，並透過報告和警示提升您對其使用情況的可見度。 PIM 提供對 Azure AD 和 Azure 資源的即時特殊許可權存取，有助於保護特殊許可權的帳戶。 存取權可以是時間界限，在這之後，就會自動撤銷許可權。 

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。

## <a name="next-steps"></a>後續步驟

- [針對 Azure RBAC 進行疑難排解](troubleshooting.md)