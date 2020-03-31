---
title: 跨 Microsoft 365 服務的管理員角色文檔 - Azure AD |微軟文檔
description: 在 Azure 活動目錄中查找 Microsoft 365 服務的管理員角色的內容和 API 引用
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024448"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>微軟 365 服務的管理員角色

在 Azure AD 中可透過管理角色管理 Microsoft 365 中的所有產品。 某些產品也提供其他專屬於該產品的角色。 如需每個產品所支援的角色資訊，請參閱下表。 有關委派問題的一般討論，請參閱 [Azure Active Directory 中的角色委派規劃](roles-concept-delegation.md)。

## <a name="where-to-find-content"></a>內容所在位置

微軟365服務 | 角色內容 | API 內容
---------------------- | ------------------ | -----------------
Office 365 與 Microsoft 365 商務計劃中的管理員角色 | [Office 365 系統管理角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | 無法使用
Azure Active Directory (Azure AD) 與 Azure AD Identity Protection| [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [交換角色型存取控制](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[擷取角色指派](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD 管理員角色](directory-assign-admin-roles.md)<br>還有[關於 Office 365 中的 SharePoint 管理員角色](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
小組/商務用 Skype | [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
安全性與合規性中心 (Office 365 進階威脅防護、Exchange Online Protection、Information Protection) | [Office 365 系統管理角色](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[擷取角色指派](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
安全分數 | [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
合規性管理員 | [合規性管理員角色](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 無法使用
Azure 資訊保護 | [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [基於角色的存取控制](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API 參考](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure 進階威脅防護 | [Azure ATP 角色群組](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | 無法使用
Windows Defender 進階威脅防護 | [Windows Defender ATP 角色型存取控制](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 無法使用
Privileged Identity Management | [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune 角色型存取控制](https://docs.microsoft.com/intune/role-based-access-control) | [圖形 API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
受控桌面 | [Azure AD 管理員角色](directory-assign-admin-roles.md) | [圖形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[擷取角色指派](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>後續步驟

* [如何指派或移除 Azure AD 系統管理員角色](directory-manage-roles-portal.md)
* [Azure AD 系統管理員角色參考](directory-assign-admin-roles.md)
