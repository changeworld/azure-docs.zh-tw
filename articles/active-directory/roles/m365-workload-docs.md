---
title: 跨 Microsoft 365 services 的系統管理員角色檔-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中尋找 Microsoft 365 服務之系統管理員角色的內容和 API 參考
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5579e0d2094c3dc596e8e50992ae6bc31818ff87
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743061"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory 中 Microsoft 365 服務的角色

Microsoft 365 中的所有產品都可透過 Azure Active Directory (Azure AD) 中的系統管理角色來管理。 某些產品也提供其他專屬於該產品的角色。 如需每個產品所支援的角色資訊，請參閱下表。 有關委派問題的一般討論，請參閱 [Azure Active Directory 中的角色委派規劃](concept-delegation.md)。

## <a name="where-to-find-content"></a>內容所在位置

Microsoft 365 服務 | 角色內容 | API 內容
---------------------- | ------------------ | -----------------
Office 365 與 Microsoft 365 商務計劃中的管理員角色 | [Microsoft 365 系統管理員角色](/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | 無法使用
Azure Active Directory (Azure AD) 與 Azure AD Identity Protection| [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [交換角色型存取控制](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[擷取角色指派](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Azure AD 管理員角色](permissions-reference.md)<br>以及 [Microsoft 365 中的 SharePoint 管理員角色](/sharepoint/sharepoint-admin-role) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
小組/商務用 Skype | [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
安全性與合規性中心 (Office 365 進階威脅防護、Exchange Online Protection、Information Protection) | [Office 365 系統管理角色](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[擷取角色指派](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
安全分數 | [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
合規性管理員 | [合規性管理員角色](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 無法使用
Azure 資訊保護 | [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Microsoft Cloud App Security | [角色型存取控制](/cloud-app-security/manage-admins) | [API 參考](/cloud-app-security/api-tokens) 
Azure 進階威脅防護 | [Azure ATP 角色群組](/azure-advanced-threat-protection/atp-role-groups) | 無法使用
Windows Defender 進階威脅防護 | [Windows Defender ATP 角色型存取控制](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 無法使用
Privileged Identity Management | [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Intune 角色型存取控制](/intune/role-based-access-control) | [圖形 API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[擷取角色指派](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
受控桌面 | [Azure AD 管理員角色](permissions-reference.md) | [圖形 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[擷取角色指派](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>後續步驟

* [如何指派或移除 Azure AD 系統管理員角色](manage-roles-portal.md)
* [Azure AD 系統管理員角色參考](permissions-reference.md)