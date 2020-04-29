---
title: 在 Azure AD Domain Services 中建立組織單位（OU） |Microsoft Docs '
description: 瞭解如何在 Azure AD Domain Services 受控網域中建立和管理自訂的組織單位（OU）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655549"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD Domain Services 受控網域中建立組織單位（OU）

Active Directory Domain Services （AD DS）中的組織單位（Ou）可讓您以邏輯方式將物件分組，例如使用者帳戶、服務帳戶或電腦帳戶。 接著，您可以將系統管理員指派給特定的 Ou，並套用群組原則來強制執行目標設定設定。

Azure AD DS 受控網域包含下列兩個內建的 Ou：

* *AADDC 電腦*-包含所有已加入受控網域之電腦的電腦物件。
* *AADDC 使用者*-包含從 Azure AD 租使用者中同步處理的使用者和群組。

當您建立和執行使用 Azure AD DS 的工作負載時，您可能需要建立服務帳戶，應用程式才能自行驗證。 若要組織這些服務帳戶，您通常會在 Azure AD DS 受控網域中建立自訂 OU，然後在該 OU 內建立服務帳戶。

在混合式環境中，在內部部署 AD DS 環境中建立的 Ou 不會同步處理至 Azure AD DS。 Azure AD DS 受控網域會使用單層的 OU 結構。 所有使用者帳戶和群組都會儲存在*AADDC Users*容器中，儘管是從不同的內部部署網域或樹系進行同步處理，即使您已在該處設定階層式 OU 結構也一樣。

本文說明如何在您的 Azure AD DS 受控網域中建立 OU。

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以[建立管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員  群組成員的使用者帳戶。

## <a name="custom-ou-considerations-and-limitations"></a>自訂 OU 的考慮和限制

當您在 Azure AD DS 受控網域中建立自訂 Ou 時，您會獲得額外的管理彈性以進行使用者管理和套用群組原則。 相較于內部部署 AD DS 環境，在 Azure AD DS 中建立和管理自訂 OU 結構時，有一些限制和考慮：

* 若要建立自訂 Ou，使用者必須是*AAD DC 系統管理員*群組的成員。
* 建立自訂 OU 的使用者會被授與該 OU 的系統管理許可權（完全控制），而且是資源擁有者。
    * 根據預設， *AAD DC 系統管理員*群組也具有自訂 OU 的完全控制權。
* 系統會建立*AADDC 使用者*的預設 OU，其中包含您 Azure AD 租使用者中所有已同步處理的使用者帳戶。
    * 您無法將使用者或群組從*AADDC users* OU 移至您所建立的自訂 ou。 只有在 Azure AD DS 受控網域中建立的使用者帳戶或資源可以移至自訂 Ou。
* 您在自訂 Ou 底下建立的使用者帳戶、群組、服務帳戶和電腦物件，無法在您的 Azure AD 租使用者中使用。
    * 這些物件不會使用 Microsoft Graph API 或在 Azure AD UI 中顯示;它們僅適用于您的 Azure AD DS 受控網域。

## <a name="create-a-custom-ou"></a>建立自訂 OU

若要建立自訂 OU，您可以使用來自已加入網域之 VM 的 Active Directory 系統管理工具。 此 Active Directory 管理中心可讓您在 Azure AD DS 受控網域（包括 Ou）中，查看、編輯和建立資源。

> [!NOTE]
> 若要在 Azure AD DS 受控網域中建立自訂 OU，您必須登入屬於*AAD DC 系統管理員*群組成員的使用者帳戶。

1. 登入您的管理 VM。 如需如何使用 Azure 入口網站進行連線的步驟，請參閱連線[到 Windows SERVER VM][connect-windows-server-vm]。
1. 從 [開始] 畫面中，選取 [系統**管理工具**]。 已安裝在教學課程中的可用管理工具清單，以[建立管理 VM][tutorial-create-management-vm]。
1. 若要建立和管理 Ou，請從系統管理工具清單中選取 [ **Active Directory 管理中心**]。
1. 在左窗格中，選擇您的 Azure AD DS 受控網域，例如*aaddscontoso.com*。 隨即顯示現有 Ou 和資源的清單：

    ![在 Active Directory 管理中心中選取您的 Azure AD DS 受控網域](./media/create-ou/create-ou-adac-overview.png)

1. [**工作] 窗格**會顯示在 Active Directory 管理中心的右邊。 在網域下（例如*aaddscontoso.com*），選取 [**新增 > 組織單位**]。

    ![在 [Active Directory 管理中心中選取 [建立新的 OU] 選項](./media/create-ou/create-ou-adac-new-ou.png)

1. 在 [**建立組織單位**] 對話方塊中，指定新 OU 的**名稱**，例如*MyCustomOu*。 提供 OU 的簡短描述，例如*服務帳戶的自訂 ou*。 如有需要，您也可以設定 OU 的 [**管理者**] 欄位。 若要建立自訂 OU，請選取 **[確定]**。

    ![從 Active Directory 管理中心建立自訂 OU](./media/create-ou/create-ou-dialog.png)

1. 回到 Active Directory 管理中心，現在會列出自訂 OU，並可供使用：

    ![可在 Active Directory 管理中心中使用的自訂 OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>後續步驟

如需有關使用系統管理工具或建立和使用服務帳戶的詳細資訊，請參閱下列文章：

* [Active Directory 管理中心：入門](https://technet.microsoft.com/library/dd560651.aspx)
* [服務帳戶的逐步指南](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
