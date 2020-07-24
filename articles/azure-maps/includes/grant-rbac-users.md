---
title: 為使用者授與以角色為基礎的存取權
titleSuffix: Azure Maps
description: 使用角色型存取控制將授權授與使用者 Azure 地圖服務
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2b138dab2a97537a93b8d873f79b6ee9c00b4af4
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126735"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>將角色型存取權授與使用者，以 Azure 地圖服務

您可以將 Azure AD 群組或安全性主體指派給一或多個 Azure 地圖服務存取控制角色定義，以授與*角色型存取控制*（RBAC）。 若要查看可用於 Azure 地圖服務的 RBAC 角色定義，請移至 **[存取控制（IAM）**]。 選取 [**角色**]，然後搜尋以*Azure 地圖服務*開頭的角色。

* 若要有效率地管理大量使用者對 Azure 地圖服務的存取，請參閱[Azure AD 群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)。
* 若要讓使用者能夠向應用程式進行驗證，則必須在 Azure AD 中建立使用者。 請參閱[使用 Azure AD 新增或刪除使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)。

深入瞭解[Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/)以有效管理使用者的目錄。

1. 移至您的**Azure 地圖服務帳戶**。 選取 **[存取控制（IAM）**  >  **角色指派**]。

    ![授與 RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [**角色指派**] 索引標籤的 [**角色**] 底下，選取內建 Azure 地圖服務角色定義，例如**Azure 地圖服務資料讀取器**或**Azure 地圖服務資料參與者**。 在 [存取權指派對象為]**** 底下，選取 [Azure AD 使用者、群組或服務主體]****。 依名稱選取 [主體]。 然後選取 [儲存]。

   * 請參閱[新增或移除角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)的詳細資料。

> [!WARNING]
> Azure 地圖服務內建角色定義可為許多 Azure 地圖服務 REST Api 提供非常大的授權存取權。 若要將使用者的 Api 限制為最少，請參閱[建立自訂角色定義和將使用者指派](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)給自訂角色定義。 這可讓使用者擁有應用程式所需的最低許可權。