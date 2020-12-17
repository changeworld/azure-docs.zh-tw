---
title: 在 PIM 中啟用 Azure 資源角色-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中啟用 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e3fe72492ef92fe65572c75bf9f88b5f9ffda3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653413"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中啟用我的 Azure 資源角色

使用 Privileged Identity Management (PIM) ，以允許 Azure 資源的合格角色成員排程在未來的日期和時間啟用。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。

本文適用于需要在 Privileged Identity Management 中啟用其 Azure 資源角色的成員。

## <a name="activate-a-role"></a>啟用角色

當您需要使用 Azure 資源角色時，可以使用 Privileged Identity Management 中的 [ **我的角色** ] 流覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。 如需如何將 Privileged Identity Management 磚新增至儀表板的詳細資訊，請參閱 [開始使用 Privileged Identity Management](pim-getting-started.md)。

1. 選取 [ **我的角色**]。

    ![顯示您可以啟動之角色的 [我的角色] 頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 選取 [ **azure 資源角色** ] 以查看符合資格的 azure 資源角色清單。

   ![我的角色-Azure 資源角色頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. 在 [Azure 資源角色] 清單中，尋找您要啟用的角色。

    ![Azure 資源角色-我的合格角色清單](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 選取 [ **啟動** ] 以開啟 [啟用] 頁面。

    ![開啟的啟用窗格，包含範圍、開始時間、持續時間和原因](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. 如果您的角色需要多重要素驗證，請選取 [先驗證您的身分識別後再繼續]。 您只需在每個工作階段驗證一次。

    ![在啟用角色之前，先以 MFA 驗證我的身分識別](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 選取 [ **驗證我** 的身分識別]，並遵循指示來提供額外的安全性驗證。

    ![提供安全性驗證的畫面，例如 PIN 碼](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果您想要指定縮減的範圍，請選取 [ **範圍** ] 以開啟 [資源篩選] 窗格。

    最佳做法是僅要求存取您需要的資源。 在 [資源] 篩選窗格中，您可以指定資源群組或您需要存取的群組。

    ![啟用-資源篩選窗格以指定範圍](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 如有必要，請指定自訂啟用開始時間。 成員會在選取的時間後啟用。

1. 在 [原因] 方塊中輸入此啟用要求的原因。

    ![完成的啟用窗格，範圍、開始時間、持續時間和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 選取 [啟用]  。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟用要求正在等待核准通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 選取 [ **我的要求** ]，以查看您的 Azure AD 角色和 Azure 資源角色要求清單。

    ![我的要求-顯示您擱置要求的 Azure 資源頁面](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 捲動至右側可檢視 [要求狀態] 欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 選取 [ **我的要求**]。

1. 針對您想要取消的角色，請選取 [ **取消** ] 連結。

    當您選取 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![已反白顯示取消動作的我的要求清單](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

當您在 Privileged Identity Management 中啟用角色時，啟用可能不會立即傳播至所有需要特殊許可權角色的入口網站。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟用延遲，以下是您應該進行的動作。

1. 登出 Azure 入口網站，然後重新登入。
1. 在 Privileged Identity Management 中，確認您已列為角色的成員。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中啟用我的 Azure AD 角色](pim-how-to-activate-role.md)
