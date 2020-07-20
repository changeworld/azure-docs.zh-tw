---
title: 在 PIM 中啟用我的 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中啟用 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433ccecdc5eee5314114d020571761ee82afd6b9
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024091"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>在 PIM 中啟用 Azure AD 角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 簡化了企業管理以特殊權限身分存取 Azure AD 中的資源和其他 Microsoft 線上服務 (如 Office 365 或 Microsoft Intune) 的方式。  

如果您已具備系統管理角色的資格，則當您需要執行特殊許可權動作時，必須啟用角色指派。 例如，如果您偶爾會管理 Office 365 功能，則貴組織的特殊權限角色管理員可能不會讓您成為永久全域管理員，因為該角色也會影響其他服務。 他們反而會讓您符合 Azure AD 角色 (例如「Exchange Online 管理員」) 的資格。 您可以在需要權限時，要求啟用該角色，然後您將會在預定的時段內擁有系統管理員控制權。

本文適用于需要在 Privileged Identity Management 中啟用其 Azure AD 角色的系統管理員。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

從2019年11月開始，Privileged Identity Management 的 Azure AD 角色部分更新為符合 Azure 資源角色體驗的新版本。 這會建立額外的功能，以及[現有 API 的變更](azure-ad-roles-features.md#api-changes)。 推出新版本時，您在本文中遵循的程式取決於您目前擁有的 Privileged Identity Management 版本。 請依照本節中的步驟來判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以選取本文中符合該版本的程式。

1. 以[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟**Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請遵循本文的 [**新版本**] 索引標籤中的指示。 否則，請依照 [**先前版本**] 索引標籤中的指示進行。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="activate-a-role"></a>啟用角色

當您需要擔任 Azure AD 角色時，您可以在 Privileged Identity Management 中開啟 [**我的角色**] 來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。 如需如何將 [Privileged Identity Management] 磚加入儀表板的相關資訊，請參閱[開始使用 Privileged Identity Management](pim-getting-started.md)。

1. 選取 [**我的角色**]，然後選取 [ **Azure AD 角色**]，以查看符合資格的 Azure AD 角色清單。

    ![顯示您可以啟用之角色的 [我的角色] 頁面](./media/pim-how-to-activate-role/my-roles.png)

1. 在 [ **Azure AD 角色**] 清單中，尋找您想要啟用的角色。

    ![Azure AD 角色-我的合格角色清單](./media/pim-how-to-activate-role/activate-link.png)

1. 選取 [**啟用**] 以開啟 [啟動] 頁面。

    ![Azure AD 角色-啟用頁面包含持續時間和範圍](./media/pim-how-to-activate-role/activate-page.png)

1. 如果您的角色需要多重要素驗證，請選取 [先驗證您的身分識別後再繼續]****。 您只需在每個工作階段驗證一次。

    ![在啟用角色之前，使用 MFA 驗證我的身分識別](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 選取 [**驗證我**的身分識別]，並遵循指示來提供額外的安全性驗證。

    ![提供安全性驗證（例如 PIN 碼）的畫面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果您想要指定縮減的範圍，請選取 [**範圍**] 以開啟 [篩選] 窗格。 在 [篩選] 窗格中，您可以指定您需要存取的 Azure AD 資源。 最佳做法是只要求存取您所需的資源。

1. 如有必要，請指定自訂啟用開始時間。 Azure AD 角色會在選取的時間之後啟用。

1. 在 [原因]**** 方塊中輸入此啟用要求的原因。

1. 選取 [啟用]  。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟用要求正在等待核准通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 選取 [**我的要求**] 以查看您的 Azure AD 角色和 Azure 資源角色要求清單。

    ![[我的要求-Azure AD] 頁面，其中顯示您的擱置要求](./media/pim-how-to-activate-role/my-requests-page.png)

1. 捲動至右側可檢視 [要求狀態]**** 欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 選取 [**我的要求**]。

1. 針對您想要取消的角色，選取 [**取消**] 連結。

    當您選取 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![已反白顯示取消動作的我的要求清單](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

當您在 Privileged Identity Management 中啟動角色時，啟用可能不會立即傳播至所有需要特殊許可權角色的入口網站。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟用延遲，以下是您應該執行的動作。

1. 登出 Azure 入口網站，然後重新登入。

1. 在 [Privileged Identity Management 中，確認您已列為角色的成員。

# <a name="previous-version"></a>[先前的版本](#tab/previous)

## <a name="activate-a-role"></a>啟用角色

當您需要採用 Azure AD 角色時，可以使用 Privileged Identity Management 中的 [**我的角色**] 導覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。 如需如何將 [Privileged Identity Management] 磚加入儀表板的相關資訊，請參閱[開始使用 Privileged Identity Management](pim-getting-started.md)。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [**我的角色**] 以查看符合資格的 Azure AD 角色清單。

    ![Azure AD 角色-顯示合格或作用中角色清單的我的角色](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 尋找您想要啟用的角色。

    ![Azure AD 角色-顯示啟用連結的合格角色清單](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. 按一下 [啟用]**** 以開啟角色啟用詳細資料窗格。

1. 如果您的角色需要多重要素驗證 (MFA)，請按一下 [先驗證您的身分識別後再繼續]****。 您只需在每個工作階段驗證一次。

    ![在啟用角色之前，使用 MFA 驗證我的身分識別窗格](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. 按一下 [驗證我的身分識別]****，並遵循指示來提供其他安全性驗證。

    ![其他安全性驗證頁面，詢問如何聯絡您](./media/pim-how-to-activate-role/additional-security-verification.png)

1. 按一下 [啟用]**** 以開啟啟用窗格。

    ![用來指定開始時間、持續時間、票證和原因的啟動窗格](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 如有必要，請指定自訂啟用開始時間。

1. 指定啟用持續時間。

1. 在 [啟用原因]**** 方塊中輸入此啟用要求的原因。 有些角色會要求您提供問題票證號碼。

    ![具有自訂開始時間、持續時間、票證和原因的已完成啟用窗格](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. 按一下 [啟動]****。

    如果角色不需要核准，則會出現 [**啟用狀態**] 窗格，顯示啟用的狀態。

    ![顯示三個啟用階段的啟用狀態頁面](./media/pim-how-to-activate-role/activation-status.png)

    完成所有階段之後，請按一下 [**登出**] 連結登出 Azure 入口網站。 當您重新登入入口網站時，您現在可以使用角色。

    如果[角色需要核准](./azure-ad-pim-approval-workflow.md)才能啟用，則 Azure 通知會出現在瀏覽器的右上角，通知您要求正在等待核准。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的要求]**** 以查看要求清單。

    ![Azure AD 角色-我的要求清單](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>停用角色

角色一經啟用，就會在達到時間限制 (合格的持續時間) 時自動停用。

如果您提早完成系統管理員工作，也可以在 Azure AD Privileged Identity Management 中手動停用角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的角色]****。

1. 按一下 [使用中的角色]****，以查看使用中的角色清單。

1. 尋找您使用完畢的角色，然後按一下 [停用]****。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [我的要求]****。

1. 針對您想要取消的角色，按一下 [取消]**** 按鈕。

    當您按一下 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![已反白顯示 [取消] 按鈕的 [我的要求] 清單](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

當您在 Privileged Identity Management 中啟動角色時，啟用可能不會立即傳播至所有需要特殊許可權角色的入口網站。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟用延遲，以下是您應該執行的動作。

1. 登出 Azure 入口網站，然後重新登入。

    當您啟動 Azure AD 角色時，您會看到啟用的階段。 當所有階段完成後，您會看到 [登出]**** 連結。 您可以使用此連結來登出。這將可解決啟用延遲的大部分情況。

1. 在 [Privileged Identity Management 中，確認您已列為角色的成員。

 ---

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中啟用我的 Azure AD 角色](pim-how-to-activate-role.md)
