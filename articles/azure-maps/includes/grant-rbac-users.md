---
title: 為使用者授與以角色為基礎的存取權
titleSuffix: Azure Maps
description: 使用角色型存取控制來授與使用者授權給 Azure 地圖服務
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895559"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>為使用者授與以角色為基礎的存取權 Azure 地圖服務

您可以將 Azure AD 群組或安全性主體指派給一或多個 Azure 地圖服務角色定義，以授與 *azure 角色型存取控制 (AZURE RBAC)* 。 若要查看可用於 Azure 地圖服務的 Azure 角色定義，請移至 [ **存取控制] (IAM)** 。 選取 [ **角色** ]，然後搜尋以 *Azure 地圖服務* 開頭的角色。

* 若要有效率地管理大量使用者存取 Azure 地圖服務，請參閱 [Azure AD 群組](../../active-directory/fundamentals/active-directory-manage-groups.md)。
* 若要讓使用者能夠驗證應用程式，必須在 Azure AD 中建立使用者。 請參閱 [使用 Azure AD 新增或刪除使用者](../../active-directory/fundamentals/add-users-azure-active-directory.md)。

請參閱 [Azure AD](../../active-directory/fundamentals/index.yml) 的詳細資訊，以有效地管理使用者的目錄。

1. 移至您的 **Azure 地圖服務帳戶** 。 選取 [ **存取控制] (IAM)**  >  **角色指派** 。

    ![使用 Azure RBAC 授與存取權](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [ **角色指派** ] 索引標籤的 [ **角色** ] 底下，選取內建的 Azure 地圖服務角色定義，例如 **Azure 地圖服務資料讀取器** 或 **Azure 地圖服務資料參與者** 。 在 [存取權指派對象為]  底下，選取 [Azure AD 使用者、群組或服務主體]  。 依名稱選取主體。 然後選取 [儲存]。

   * 請參閱 [新增或移除角色指派](../../role-based-access-control/role-assignments-portal.md)的詳細資料。

> [!WARNING]
> Azure 地圖服務內建角色定義可提供非常大的授權存取權給許多 Azure 地圖服務 REST Api。 若要限制使用者最少的 Api，請參閱 [建立自訂角色定義，並將使用者指派](../../role-based-access-control/custom-roles.md) 給自訂角色定義。 這可讓使用者擁有應用程式所需的最低許可權。