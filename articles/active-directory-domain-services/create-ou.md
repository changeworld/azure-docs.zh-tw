---
title: 在 Azure AD 域服務中創建組織單位 （OU） |微軟文檔
description: 瞭解如何在 Azure AD 域服務託管域中創建和管理自訂群組織單元 （OU）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7abbdf03e85f425f65a45e6640b82529c2b9c84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614057"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服務託管域中創建組織單位 （OU）

活動目錄域服務 （AD DS） 中的組織單位 （O） 允許您以邏輯方式對物件（如使用者帳戶、服務帳戶或電腦帳戶）進行分組。 然後，您可以將管理員分配給特定 O，並應用群組原則來強制實施目標配置設置。

Azure AD DS 託管域包括兩個內置 O - *AADDC 電腦*和*AADDC 使用者*。 *AADDC 電腦*OU 包含所有連接到託管域的電腦的電腦物件。 *AADDC 使用者*OU 包括從 Azure AD 租戶同步的使用者和組。 創建和運行使用 Azure AD DS 的工作負載時，可能需要為應用程式創建服務帳戶以進行自我身份驗證。 要組織這些服務帳戶，您通常在 Azure AD DS 託管域中創建自訂 OU，然後在該 OU 中創建服務帳戶。

在混合環境中，在本地 AD DS 環境中創建的 O 不會同步到 Azure AD DS。 Azure AD DS 託管域使用平面 OU 結構。 所有使用者帳戶和組都存儲在*AADDC 使用者*容器中，儘管從不同的本地域或林同步，即使您已在那裡配置了分層 OU 結構也是如此。

本文介紹如何在 Azure AD DS 託管域中創建 OU。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前

要完成本文，您需要以下資源和特權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要，請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要，請完成教程以創建[管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

## <a name="custom-ou-considerations-and-limitations"></a>自訂 OU 注意事項和限制

在 Azure AD DS 託管域中創建自訂 O 時，您將獲得用於使用者管理和應用群組原則的額外管理靈活性。 與本地 AD DS 環境相比，在 Azure AD DS 中創建和管理自訂 OU 結構時存在一些限制和注意事項：

* 要創建自訂 O，使用者必須是*AAD DC 管理員*組的成員。
* 創建自訂 OU 的使用者被授予對該 OU 的管理許可權（完全控制），並且是資源擁有者。
    * 預設情況下 *，AAD DC 管理員*組還可以完全控制自訂 OU。
* 為*AADDC 使用者*創建預設 OU，其中包含 Azure AD 租戶中的所有同步使用者帳戶。
    * 不能將使用者或組從*AddC 使用者*OU 移動到您創建的自訂 OU。 只能在 Azure AD DS 託管域中創建的使用者帳戶或資源才能移動到自訂 O 中。
* 在自訂 O 下創建的使用者帳戶、組、服務帳戶和電腦物件在 Azure AD 租戶中不可用。
    * 這些物件不會使用 Microsoft 圖形 API 或 Azure AD UI 顯示;否則，這些物件不會顯示在 Azure AD UI 中。它們僅在 Azure AD DS 託管域中可用。

## <a name="create-a-custom-ou"></a>創建自訂 OU

要創建自訂 OU，請使用加入域的 VM 中的活動目錄管理工具。 活動目錄管理中心允許您在 Azure AD DS 託管域（包括 OEM）中查看、編輯和創建資源。

> [!NOTE]
> 要在 Azure AD DS 託管域中創建自訂 OU，必須登錄到*屬於 AAD DC 管理員*組成員的使用者帳戶。

1. 登錄到管理 VM。 有關如何使用 Azure 門戶進行連接的步驟，請參閱[連接到 Windows 伺服器 VM][connect-windows-server-vm]。
1. 在"開始"螢幕中，選擇 **"管理工具**"。 在本教程中顯示了用於[創建管理 VM][tutorial-create-management-vm]的可用管理工具的清單。
1. 要創建和管理 O，請從管理工具清單中選擇**活動目錄管理中心**。
1. 在左側窗格中，選擇 Azure AD DS 託管域，如*aaddscontoso.com*。 將顯示現有非統組織和資源的清單：

    ![在活動目錄管理中心中選擇 Azure AD DS 託管域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. "**任務**"窗格顯示在活動目錄管理中心的右側。 在域下，如*aaddscontoso.com，* 選擇 **"新>組織單位**"。

    ![選擇在活動目錄管理中心創建新 OU 的選項](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. 在 **"創建組織單位"** 對話方塊中，為新 OU 指定**名稱**，如*MyCustomOu*。 提供 OU 的簡短說明，例如*服務帳戶的自訂 OU。* 如果需要，還可以為 OU 設置 **"按管理"** 欄位。 要創建自訂 OU，請選擇 **"確定**"。

    ![從活動目錄管理中心創建自訂 OU](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. 回到活動目錄管理中心，自訂 OU 現已列出，可供使用：

    ![自訂 OU 可在活動目錄管理中心使用](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>後續步驟

有關使用管理工具或創建和使用服務帳戶的詳細資訊，請參閱以下文章：

* [Active Directory 管理中心：入門](https://technet.microsoft.com/library/dd560651.aspx)
* [服務帳戶的逐步指南](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
