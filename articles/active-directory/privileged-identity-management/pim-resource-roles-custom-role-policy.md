---
title: 在 PIM 中對 Azure 資源使用自訂角色 - Azure AD |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中使用適用於 Azure 資源的自訂角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847053"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>在特權標識管理中對 Azure 資源使用自訂角色

您可能需要對 Azure 活動目錄 （Azure AD） 組織中具有特權角色的某些使用者應用嚴格的特權標識管理 （PIM） 設置，同時為其他使用者提供更大的自治性。 例如，假設您的組織雇用多個合同助理來説明開發將在 Azure 訂閱中運行的應用程式。

身為資源管理員的您，會希望員工能夠存取而不需要經過核准。 不過，當約聘夥伴要求存取組織資源時，全部都必須經過核准。

按照下一節中概述的步驟為 Azure 資源角色設置有針對性的特權標識管理設置。

## <a name="create-the-custom-role"></a>建立自訂角色

若要建立資源適用的自訂角色，請依照[建立 Azure 角色型存取控制適用的自訂角色](../role-based-access-control-custom-roles.md)所述步驟操作。

建立自訂角色時請包含描述性名稱，如此方可輕易記住您打算複製的內建角色。

> [!NOTE]
> 確保自訂角色是您想複製的內建角色重複項，且其範圍符合內建角色。

## <a name="apply-pim-settings"></a>套用 PIM 設定

在 Azure AD 組織中創建角色後，轉到 Azure 門戶中的 **"特權標識管理 - Azure 資源**"頁。 選取角色適用的資源。

![「Privileged Identity Management - Azure 資源」窗格](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

配置應應用於角色的這些成員[的特權標識管理角色設置](pim-resource-roles-configure-role-settings.md)。

最後，[將角色指派](pim-resource-roles-assign-roles.md)給您想要使用這些設定來鎖定的不同成員群組。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
- [Azure 中的自訂角色](../../role-based-access-control/custom-roles.md)
