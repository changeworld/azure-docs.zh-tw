---
title: Privileged Identity Management 先前的安全性嚮導中 Azure AD 角色探索和深入解析 (預覽) -Azure Active Directory
description: 探索和深入解析 (先前的安全性嚮導) 可協助您將永久 Azure AD 的角色指派，轉換成與 Privileged Identity Management 的即時指派。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff5d15ef66b597fdf56fefe90f35cbf122bb093f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534432"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Azure AD 角色的探索和見解 (預覽)  (先前的安全性嚮導) 

如果您是從 Azure Active Directory (Azure AD 組織的 Privileged Identity Management (PIM) 開始，您可以使用 [ **探索與見解]) 預覽 (** 頁面來開始使用。 這項功能會顯示哪些人指派給您組織中的特殊許可權角色，以及如何使用 PIM 快速將永久角色指派變更為及時指派。 您可以在 **探索和見解 (預覽) **中，查看或變更您的永久特殊許可權角色指派。 它是一種分析工具和動作工具。

## <a name="discovery-and-insights-preview"></a>探索和見解 (預覽) 

在您的組織開始使用 Privileged Identity Management 之前，所有角色指派都是永久的。 使用者一律會在其指派的角色中，即使他們不需要他們的許可權也一樣。 探索和深入解析 (預覽) （取代先前的安全性嚮導）會顯示特殊許可權角色的清單，以及這些角色中目前有多少使用者。 您可以列出角色指派，以深入瞭解指派的使用者（如果有一或多個使用者不熟悉）。

： heavy_check_mark： **Microsoft 建議** 您保留永久指派給全域管理員角色的兩個「中斷玻璃」帳戶。 請確定這些帳戶不需要與一般系統管理帳戶相同的多重要素驗證機制來登入，如 Azure AD 中的 [管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)中所述。

此外，如果使用者具有 Microsoft 帳戶 (（也就是他們用來登入 Microsoft 服務例如 Skype 或 Outlook.com) 的帳戶），請將角色指派保持永久。 如果您需要具有 Microsoft 帳戶的使用者進行多重要素驗證，才能啟用角色指派，使用者將遭到鎖定。

## <a name="open-discovery-and-insights-preview"></a>開啟探索和見解 (預覽) 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色** ]，然後選取 [ **探索] 和 [見解] ([預覽]) **。 開啟頁面會開始探索程式，以尋找相關的角色指派。

    ![Azure AD 角色-顯示3個選項的探索和見解頁面](./media/pim-security-wizard/new-preview-link.png)

1. 選取 [ **減少全域管理員**]。

    ![螢幕擷取畫面，顯示已選取「減少全域管理員」動作的「探索和見解 (預覽) 」。](./media/pim-security-wizard/new-preview-page.png)

1. 檢查全域管理員角色指派的清單。

    ![減少全域管理員-顯示所有全域管理員的角色窗格](./media/pim-security-wizard/new-global-administrator-list.png)

1. 選取 [ **下一步]** 以選取您要設為合格的使用者或群組，然後選取 [ **設為合格** 或 **移除指派**]。

    ![將成員轉換為符合資格的頁面，並提供選項來選取您想要讓角色符合資格的成員](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. 您也可以要求所有全域系統管理員檢查自己的存取權。

    ![顯示存取權審核區段的全域系統管理員頁面](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. 當您選取其中任何一項變更之後，您會看到 Azure 通知。

1. 然後，您可以選取 [ **刪除持續存取** 或 **審核服務主體** ]，在其他特殊許可權角色和服務主體角色指派上重複上述步驟。 針對服務主體角色指派，您只能移除角色指派。

    ![消除持續存取和審核服務主體的其他見解選項 ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [將存取權授與其他系統管理員以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
