---
title: 使用 Privileged Identity Management Azure Active Directory 的授權需求 |Microsoft Docs
description: 說明要使用 Azure AD Privileged Identity Management (PIM) 的授權需求。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d931206e99516e0320d0cbedd0812389ee41aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742159"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>使用 Privileged Identity Management 的授權要求

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目錄必須具有有效的授權。 此外，也必須指派授權給管理員和相關的使用者。 本文說明使用 Privileged Identity Management 的授權需求。

## <a name="valid-licenses"></a>有效的授權

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>您必須擁有多少個授權？

請確定您目錄擁有的 Azure AD Premium P2 授權至少與將執行下列工作的員工一樣多：

- 指派為符合使用 PIM 管理之 Azure AD 角色資格的使用者
- 能夠在 PIM 中核准或拒絕啟用要求的使用者
- 指派給 Azure 資源角色 (透過及時或直接 (以時間為基礎) 的方式指派) 的使用者  
- 指派給存取權檢閱的使用者
- 執行存取權檢閱的使用者

下列工作**不**需要 Azure AD Premium P2 授權：

- 具有設定 PIM 的全域管理員或特殊許可權角色管理員角色的使用者，不需要任何授權、設定原則、接收警示和設定存取審查。

如需授權的詳細資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

## <a name="example-license-scenarios"></a>範例授權案例

以下是一些範例授權案例，可協助您判斷您必須擁有的授權數目。

| 狀況 | 計算 | 授權數目 |
| --- | --- | --- |
| Woodgrove Bank 有10個不同部門的系統管理員，以及2個設定和管理 PIM 的全域系統管理員。 他們讓五位系統管理員都符合資格。 | 適用于系統管理員的五個授權 | 5 |
| 圖形設計研究所有25個系統管理員，其中14個是透過 PIM 管理。 角色啟用需要核准，而且組織中有三個不同的使用者可以核准啟用。 | 14個授權適用于符合資格的角色 + 三個核准者 | 17 |
| Contoso 有50個系統管理員，其中42是透過 PIM 管理。 角色啟用需要核准，而且組織中有五個不同的使用者可以核准啟用。 Contoso 也會對指派給系統管理員角色和審核者的使用者進行每月審核，這是使用者的經理，其中六個不是由 PIM 管理的系統管理員角色。 | 適用于合格角色的42授權 + 五位核准者 + 六位審核者 | 53 |

## <a name="what-happens-when-a-license-expires"></a>授權過期會發生什麼狀況？

如果 Azure AD Premium P2、EMS E5 或試用版授權過期，Privileged Identity Management 功能將無法在您的目錄中使用：

- Azure AD 角色的永久角色指派不會受到影響。
- Azure 入口網站中的 Privileged Identity Management 服務，以及 Privileged Identity Management 的圖形 API Cmdlet 和 PowerShell 介面，將無法再供使用者啟用特殊許可權角色、管理特殊許可權存取，或執行特殊許可權角色的存取權審查。
- Azure AD 角色的合格角色指派將會遭到移除，因為使用者將無法再啟動特殊權限的角色。
- Azure AD 角色的任何進行中存取檢查將會結束，且 Privileged Identity Management 設定將會移除。
- Privileged Identity Management 將不會再傳送有關角色指派變更的電子郵件。

## <a name="next-steps"></a>後續步驟

- [部署 Privileged Identity Management](pim-deployment-plan.md)
- [開始使用 Privileged Identity Management](pim-getting-started.md)
- [您無法在 Privileged Identity Management 中管理的角色](pim-roles.md)
