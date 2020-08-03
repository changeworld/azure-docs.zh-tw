---
title: 查看 Privileged Identity Management （PIM）中特殊許可權存取群組指派的審核報表-Azure AD |Microsoft Docs
description: 在 Azure AD Privileged Identity Management （PIM）中，查看特殊許可權存取群組指派的活動和審核歷程記錄。
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
ms.openlocfilehash: d9bbc90776ca007b84d5f67c50f8550ee9c881c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506039"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management 中的特殊許可權存取群組指派（預覽）的審核活動歷程記錄

使用 Privileged Identity Management （PIM），您可以在您的 Azure Active Directory （Azure AD）組織內，查看 Azure 特殊許可權存取群組成員和擁有者的活動、啟用和審核歷程記錄。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

請遵循下列步驟來查看特殊許可權存取群組的審核歷程記錄。

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

**資源審核**可讓您查看與您的特殊許可權存取群組相關聯的所有活動。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 **[特殊許可權存取（預覽）**]。

1. 在 [**活動**] 下，選取 [**資源審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![具有篩選的資源審核清單](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 **[特殊許可權存取（預覽）**]。

1. 選取您想要查看其 [audit 歷程記錄] 的成員或群組。

1. 選取 [**我的審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![目前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>後續步驟

- [指派 Privileged Identity Management 中的群組成員資格和擁有權](groups-assign-member-owner.md)
- [在 PIM 中核准或拒絕特殊許可權存取群組的要求](groups-approval-workflow.md)
- [在 PIM 中查看 Azure AD 角色的審核歷程記錄](groups-audit.md)
