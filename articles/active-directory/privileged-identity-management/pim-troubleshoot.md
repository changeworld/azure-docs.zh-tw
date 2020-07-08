---
title: 針對 Privileged Identity Management Azure Active Directory 問題進行疑難排解 |Microsoft Docs
description: 瞭解如何使用 Azure AD Privileged Identity Management （PIM）中的角色對系統錯誤進行疑難排解。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d2ba5806e23aa87e5d7787be300512444f95b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742142"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>疑難排解 Privileged Identity Management 的問題

您在 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM）有問題嗎？ 後續資訊可協助您重新運作項目。

## <a name="access-to-azure-resources-denied"></a>拒絕存取 Azure 資源

### <a name="problem"></a>問題

身為 Azure 資源的「作用中擁有者」或「使用者存取系統管理員」，您可以在 Privileged Identity Management 中看到您的資源，但無法執行任何動作，例如在資源的 [總覽] 頁面中進行合格指派或查看角色指派清單。 其中任何一項動作都會導致授權錯誤。

### <a name="cause"></a>原因

當 PIM 服務主體的使用者存取系統管理員角色不小心從訂用帳戶中移除時，就會發生此問題。 若要讓 Privileged Identity Management 服務能夠存取 Azure 資源，應一律透過 Azure 訂用帳戶將「[使用者存取系統管理員」角色](../../role-based-access-control/built-in-roles.md#user-access-administrator)指派給「MS-PIM 服務主體」。

### <a name="resolution"></a>解決方案

將使用者存取系統管理員角色指派給訂用帳戶層級的特殊許可權身分識別管理服務主體名稱（MS-PIM）。 此指派應允許特殊許可權身分識別管理服務存取 Azure 資源。 角色可以在管理群組層級或訂用帳戶層級上指派，視您的需求而定。 如需服務主體的詳細資訊，請參閱[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)。

## <a name="next-steps"></a>後續步驟

- [使用 Privileged Identity Management 的授權要求](subscription-requirements.md)
- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
