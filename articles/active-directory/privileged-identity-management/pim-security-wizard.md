---
title: Azure AD 角色探索和深入解析 (預覽) Privileged Identity Management 先前的安全性嚮導-Azure Active Directory
description: 探索和 insights (先前的安全性 Wizard) 可協助您將永久 Azure AD 角色指派轉換成與 Privileged Identity Management 的即時指派。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005897"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>探索和見解 (預覽) 適用于先前 (安全性嚮導的 Azure AD 角色) 

如果您開始使用 Azure Active Directory (Azure AD) 組織中的 Privileged Identity Management (PIM) ，您可以使用**探索和深入解析 (preview) **頁面開始。 這項功能會顯示指派給您組織中特殊許可權角色的人員，以及如何使用 PIM 將永久角色指派快速變更為及時指派。 您可以在**探索和深入解析 (預覽) **中，查看或變更您的永久特殊許可權角色指派。 它是一種分析工具和動作工具。

## <a name="discovery-and-insights-preview"></a>探索和深入解析 (預覽) 

在您的組織開始使用 Privileged Identity Management 之前，所有角色指派都是永久的。 即使使用者不需要許可權，也一定會在其指派的角色中。 探索和深入解析 (預覽) （取代先前的安全性嚮導）會顯示特殊許可權角色清單，以及目前在這些角色中的使用者人數。 如果有一或多個不熟悉，您可以列出角色的指派，以深入瞭解指派的使用者。

： heavy_check_mark： **Microsoft 建議**您保留2個中斷的玻璃帳戶，永久指派給在登入時不需要多重要素驗證的全域管理員角色。 您可以在任何中斷玻璃案例中使用這些功能，或在沒有人能夠提升為其全域管理員角色的情況下使用。

此外，如果使用者有 Microsoft 帳戶的 (（也就是他們用來登入 Microsoft 服務（例如 Skype）或 Outlook.com) 的帳戶），請將角色指派保持為永久。 如果您需要在這類角色中啟用多重要素驗證，將會鎖定使用者。

## <a name="open-discovery-and-insights-preview"></a>開啟探索及深入解析 (預覽) 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]，然後選取 [探索] 和 [深入解析] ** (預覽) **。 開啟頁面會開始探索進程，以尋找相關的角色指派。

    ![Azure AD 角色-[探索] 和 [深入解析] 頁面顯示3個選項](./media/pim-security-wizard/new-preview-link.png)

1. 選取 [**減少全域管理員**]。

    ![減少全域系統管理員-顯示所有成員的角色窗格](./media/pim-security-wizard/new-preview-page.png)

1. 查看全域管理員角色指派清單。

    ![減少全域系統管理員-顯示所有成員的角色窗格](./media/pim-security-wizard/new-global-administrator-list.png)

1. 選取 [**下一步]** 以選取您要讓其符合資格的使用者或群組，然後選取 [**設為合格**或**移除指派**]。

    ![將成員轉換為合格頁面，並提供選項來選取您想要讓角色符合資格的成員](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. 您也可以要求所有全域管理員檢查自己的存取權。

    ![顯示存取權審查區段的全域管理員頁面](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. 在您選取其中任何一項變更之後，您會看到 Azure 通知。

1. 然後，您可以選取 [排除先前的**存取權**] 或 [**審查服務主體**]，在其他特殊許可權角色和服務主體角色指派上重複上述步驟。 針對服務主體角色指派，您只能移除角色指派。

    ![其他深入解析選項，以消除持續存取並審查服務主體 ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授與其他系統管理員存取權以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
