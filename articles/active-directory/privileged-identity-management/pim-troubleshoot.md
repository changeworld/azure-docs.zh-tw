---
title: 針對 Privileged Identity Management Azure Active Directory 的問題進行疑難排解 |Microsoft Docs
description: 瞭解如何使用 Azure AD Privileged Identity Management (PIM) 中的角色針對系統錯誤進行疑難排解。
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
ms.openlocfilehash: dcbebcb32e912abdf0112007c743c6890fae36e4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372390"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>針對 Privileged Identity Management 的問題進行疑難排解

您在 Azure Active Directory (Azure AD) 中有 Privileged Identity Management (PIM) 的問題嗎？ 後續資訊可協助您重新運作項目。

## <a name="access-to-azure-resources-denied"></a>拒絕存取 Azure 資源

### <a name="problem"></a>問題

作為 Azure 資源的作用中擁有者或使用者存取系統管理員，您可以在 Privileged Identity Management 中看到您的資源，但無法執行任何動作，例如進行合格指派，或從資源總覽頁面查看角色指派清單。 任何這些動作都會導致授權錯誤。

### <a name="cause"></a>原因

當 PIM 服務主體的使用者存取系統管理員角色意外從訂用帳戶中移除時，就會發生此問題。 若要讓 Privileged Identity Management 服務能夠存取 Azure 資源，應一律透過 Azure 訂用帳戶將「 [使用者存取系統管理員」角色](../../role-based-access-control/built-in-roles.md#user-access-administrator) 指派給「MS PIM 服務主體」。

### <a name="resolution"></a>解決方案

將使用者存取系統管理員角色指派給訂用帳戶層級 (MS – PIM) 的特殊許可權身分識別管理服務主體名稱。 此指派應允許特殊許可權身分識別管理服務存取 Azure 資源。 您可以根據您的需求，在管理群組層級或訂用帳戶層級上指派角色。 如需服務主體的詳細資訊，請參閱 [將應用程式指派給角色](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)。

## <a name="next-steps"></a>後續步驟

- [使用 Privileged Identity Management 的授權要求](subscription-requirements.md)
- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)