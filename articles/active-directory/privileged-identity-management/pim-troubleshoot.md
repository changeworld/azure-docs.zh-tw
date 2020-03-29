---
title: 解決特權標識管理問題 - Azure 活動目錄 |微軟文檔
description: 瞭解如何使用 Azure AD 特權標識管理 （PIM） 中的角色解決系統錯誤。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299664"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>解決特權標識管理問題

Azure 活動目錄 （Azure AD） 中的特權標識管理 （PIM） 有問題嗎？ 後續資訊可協助您重新運作項目。

## <a name="access-to-azure-resources-denied"></a>訪問 Azure 資源被拒絕

### <a name="problem"></a>問題

作為 Azure 資源的活動擁有者或使用者訪問管理員，您可以在特權標識管理中查看資源，但不能執行任何操作，例如進行符合條件的分配或查看資源中的角色指派清單概述頁。 這些操作中的任何一個都會導致授權錯誤。

### <a name="cause"></a>原因

當 PIM 服務主體的使用者訪問管理員角色意外從訂閱中刪除時，可能會發生此問題。 為了使特權標識管理服務能夠訪問 Azure 資源，應始終在 Azure 訂閱上為 MS-PIM 服務主體分配[使用者訪問管理員角色](../../role-based-access-control/built-in-roles.md#user-access-administrator)。

### <a name="resolution"></a>解決方案

在訂閱級別將使用者訪問管理員角色分配給特權標識管理服務主體名稱 （MS_PIM）。 此分配應允許特權標識管理服務訪問 Azure 資源。 角色可以在管理組級別或訂閱級別分配，具體取決於您的要求。 有關詳細資訊，請參閱[將應用程式分配給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)。

## <a name="next-steps"></a>後續步驟

- [使用 Privileged Identity Management 的授權要求](subscription-requirements.md)
- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
