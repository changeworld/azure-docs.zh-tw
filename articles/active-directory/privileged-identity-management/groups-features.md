---
title: 在 Privileged Identity Management (PIM) 中管理具特殊權限的 Azure AD 群組 | Microsoft Docs
description: 如何管理 Privileged Identity Management (PIM) 中具特殊存取權限群組的成員和擁有者
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6709d7589ebf4c82ec25c6b7a79fa31a305839
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506438"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>具 Azure AD 群組 (預覽) 特殊存取權限的管理功能

在 Privileged Identity Management (PIM) 中，您現在可以指派具特殊存取權限群組的成員資格或所有權資格。 從本預覽版開始，您可以將 Azure Active Directory (Azure AD) 內建角色指派給雲端群組，並使用 PIM 來管理群組成員和擁有者資格與啟用。 如需 Azure AD 中可指派角色群組的詳細資訊，請參閱[使用雲端群組來管理 Azure Active Directory (預覽) 中的角色指派](../users-groups-roles/roles-groups-concept.md)。

## <a name="require-different-policies-for-each-role-assignable-group"></a>每個可指派角色的群組都需要不同的原則

有些組織使用像是 Azure AD 的企業對企業 (B2B) 共同作業工具，邀請合作夥伴作為其 Azure AD 組織的來賓。 您可以使用其各自的原則建立兩個不同的具特殊存取權限群組，而不是所有指派給具特殊權限角色的一次性原則。 您可以針對信任的員工強制執行較不嚴格的需求，而在合作夥伴要求啟用指派群組的核准工作流程時，強制執行較嚴格的需求。

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>在單一要求中啟用多個角色指派

使用特殊存取權限群組預覽，您可以讓工作負載特定的管理員快速存取多個具有單一 Just-In-Time 要求的角色。 例如，您的第 3 層級 Office 管理員可能需要 Exchange 管理員、Office 應用程式管理員、Teams 管理員和搜尋管理角色的 Just-In-Time 存取權，才能完整調查每日事件。 在今天之前，系統需要四個連續的要求，表示這個流程需要一些時間才能完成。 但您可以建立名為「第 3 層級 Office 管理員」的可指派角色群組，將其指派給之前提到的四個角色 (或任何 Azure AD 內建角色)，並在群組的活動區段中啟用具特殊存取權限。 一旦啟用了具特殊存取權限，您可以針對群組成員設定 Just-In-Time 設定，並將管理員和擁有者設為符合資格。 當管理員升格至群組時，會成為這四個 Azure AD 角色的成員。

## <a name="extend-and-renew-group-assignments"></a>擴充和更新群組指派

設定時間限制擁有者或成員指派之後，您可能會問的第一個問題是如果指派到期會發生什麼事？ 在這個新版本中，我們提供兩個適用於此案例的選項：

- 擴充 - 當角色指派接近到期時，使用者可以使用 Privileged Identity Management 來要求擴充角色指派
- 更新 - 當角色指派接近到期時，使用者可以使用 Privileged Identity Management 來要求更新角色指派

兩者的使用者起始動作都需要全域管理員或具特殊權限角色管理員的核准。 管理員不再需要管理這些與到期相關的作業， 而只需等待擴充或更新要求，並在要求有效時核准即可。

## <a name="next-steps"></a>後續步驟

- [指派具特殊存取權限群組擁有者或成員](groups-assign-member-owner.md)
- [核准或拒絕具特殊存取權限群組成員和擁有者的啟用要求](groups-approval-workflow.md)
