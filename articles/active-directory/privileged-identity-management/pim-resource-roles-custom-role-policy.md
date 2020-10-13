---
title: 在 PIM 中使用 Azure 自訂角色-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中使用 Azure 自訂角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782595"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中使用 Azure 自訂角色

您可能需要將嚴格的 Privileged Identity Management (PIM) 設定套用至 Azure Active Directory (Azure AD) 組織的特殊許可權角色中的某些使用者，同時為他人提供更大的自主性。 例如，假設您的組織雇用數個合約相關聯的案例，以協助開發將在 Azure 訂用帳戶中執行的應用程式。

身為資源管理員的您，會希望員工能夠存取而不需要經過核准。 不過，當約聘夥伴要求存取組織資源時，全部都必須經過核准。

遵循下一節中所述的步驟，設定 Azure 資源角色的目標 Privileged Identity Management 設定。

## <a name="create-the-custom-role"></a>建立自訂角色

若要建立資源的自訂角色，請遵循 [Azure 自訂角色](../../role-based-access-control/custom-roles.md)中所述的步驟。

建立自訂角色時請包含描述性名稱，如此方可輕易記住您打算複製的內建角色。

> [!NOTE]
> 確保自訂角色是您想複製的內建角色重複項，且其範圍符合內建角色。

## <a name="apply-pim-settings"></a>套用 PIM 設定

在 Azure AD 組織中建立角色之後，請移至 Azure 入口網站中的 [ **Privileged Identity Management-Azure 資源** ] 頁面。 選取角色適用的資源。

![「Privileged Identity Management - Azure 資源」窗格](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

設定應套用至角色成員的[Privileged Identity Management 角色設定](pim-resource-roles-configure-role-settings.md)。

最後，[將角色指派](pim-resource-roles-assign-roles.md)給您想要使用這些設定來鎖定的不同成員群組。

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [Azure 中的自訂角色](../../role-based-access-control/custom-roles.md)