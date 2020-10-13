---
title: 在 Privileged Identity Management (PIM) 中，查看特殊許可權存取群組指派的審核報表 Azure AD |Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中，查看特殊許可權存取群組指派的活動和審核歷程記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141664"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management 中 (預覽) 的特殊許可權存取群組指派審核活動歷程記錄

您可以使用 Privileged Identity Management (PIM) 來查看 Azure 特殊許可權存取群組成員的活動、啟用和審核歷程記錄，以及 Azure Active Directory (Azure AD) 組織內的擁有者。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用 [Azure 委派資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，此服務提供者所授權的角色指派將不會顯示在此處。

請遵循下列步驟來查看特殊許可權存取群組的審核歷程記錄。

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

**資源審核** 可讓您查看所有與您的特殊許可權存取群組相關聯的活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 ** (預覽) **的特殊許可權存取群組。

1. 選取您要查看其審核歷程記錄的特殊許可權存取群組。

1. 在 [ **活動**] 底下，選取 [ **資源審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![具有篩選的資源審核清單](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>檢視我的稽核

「**我的審核**」可讓您查看特殊許可權存取群組的個人角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 ** (預覽) **的特殊許可權存取群組。

1. 選取您要查看其審核歷程記錄的特殊許可權存取群組。

1. 在 [ **活動**] 底下，選取 [ **我的審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![目前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派群組成員資格和擁有權](groups-assign-member-owner.md)
- [在 PIM 中核准或拒絕特殊許可權存取群組的要求](groups-approval-workflow.md)
- [在 PIM 中查看 Azure AD 角色的審核歷程記錄](groups-audit.md)
