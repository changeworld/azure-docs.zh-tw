---
title: 管理 Azure AD Domain Services 的 DNS |Microsoft Docs
description: 瞭解如何安裝 DNS 伺服器工具來管理 Azure Active Directory Domain Services 受控網域的 DNS。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613685"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>管理 Azure AD Domain Services 受控網域中的 DNS

在 Azure Active Directory Domain Services （Azure AD DS）中，主要元件是 DNS （功能變數名稱解析）。 Azure AD DS 包含 DNS 伺服器，可提供受控網域的名稱解析。 此 DNS 伺服器包含內建的 DNS 記錄，以及允許服務執行之主要元件的更新。

當您執行自己的應用程式和服務時，可能需要為未加入網域的電腦建立 DNS 記錄、設定負載平衡器的虛擬 IP 位址，或設定外部 DNS 轉寄站。 屬於*AAD DC 系統管理員*群組的使用者會被授與 Azure AD DS 受控網域上的 DNS 管理許可權，而且可以建立和編輯自訂 DNS 記錄。

在混合式環境中，內部部署 AD DS 環境中設定的 DNS 區域和記錄不會同步處理至 Azure AD DS。 若要定義和使用您自己的 DNS 專案，請在 Azure AD DS DNS 伺服器中建立記錄，或使用指向您環境中現有 DNS 伺服器的條件轉寄站。

本文說明如何安裝 DNS 伺服器工具，然後使用 DNS 主控台來管理 Azure AD DS 中的記錄。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以[建立 Windows SERVER VM，並將它加入受控網域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="install-dns-server-tools"></a>安裝 DNS 伺服器工具

若要在 Azure AD DS 中建立及修改 DNS 記錄，您必須安裝 DNS 伺服器工具。 這些工具可以在 Windows Server 中安裝為功能。 如需有關如何在 Windows 用戶端上安裝系統管理工具的詳細資訊，請參閱 install[遠端伺服器管理工具（RSAT）][install-rsat]。

1. 登入您的管理 VM。 如需如何使用 Azure 入口網站進行連線的步驟，請參閱連線[到 Windows SERVER VM][connect-windows-server-vm]。
1. 如果 [伺服器管理員] 在您登入 VM 時並未預設為開啟狀態，請選取 [開始] 功能表，然後選擇 [伺服器管理員]。
1. 在 [伺服器管理員] 視窗的 [儀表板] 窗格內，選取 [新增角色及功能]。
1. 在 [新增角色及功能精靈] 的 [開始之前] 頁面上，選取 [下一步]。
1. 針對 [安裝類型]，保持勾選 [角色型或功能型安裝] 選項，然後選取 [下一步]。
1. 在 [**伺服器選擇**] 頁面上，從伺服器集區中選擇目前的 VM，例如*myvm.aaddscontoso.com*，然後選取 **[下一步]** 。
1. 在 [伺服器角色] 頁面上，按 [下一步]。
1. 在 [功能] 頁面上，展開 [遠端伺服器管理工具] 節點，然後展開 [角色管理工具] 節點。 從角色管理工具清單中，選取 [DNS 伺服器工具] 功能。

    ![從可用的角色管理工具清單中選擇安裝 DNS 伺服器工具](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. 在 [確認] 頁面上，選取 [安裝]。 安裝群組原則管理工具可能需要一或兩分鐘的時間。
1. 完成功能安裝時，請選取 [關閉] 以結束 [新增角色及功能] 精靈。

## <a name="open-the-dns-management-console-to-administer-dns"></a>開啟 DNS 管理主控台來管理 DNS

安裝 DNS 伺服器工具之後，您就可以管理 Azure AD DS 受控網域上的 DNS 記錄。

> [!NOTE]
> 若要管理 Azure AD DS 受控網域中的 DNS，您必須登入屬於*AAD DC 系統管理員*群組成員的使用者帳戶。

1. 從 [開始] 畫面中，選取 [系統**管理工具**]。 隨即會顯示可用的管理工具清單，包括上一節中所安裝的**DNS** 。 選取 [ **dns** ] 以啟動 dns 管理主控台。
1. 在 [連線**到 DNS 伺服器**] 對話方塊中，選取 **[下列電腦**]，然後輸入受控網域的 DNS 功能變數名稱，例如*aaddscontoso.com*：

    ![連接到 DNS 主控台中的 Azure AD DS 受控網域](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS 主控台會連線到指定的 Azure AD DS 受控網域。 展開 [**正向對應區域**] 或 [**反向對應區域**]，以建立必要的 DNS 專案，或視需要編輯現有的記錄。

    ![DNS 主控台 - 管理網域](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> 當您使用 DNS 伺服器工具來記錄管理時，請確定您不會刪除或修改 Azure AD DS 所使用的內建 DNS 記錄。 內建 DNS 記錄包括網域 DNS 記錄、名稱伺服器記錄和其他用於 DC 位置的記錄。 如果您修改這些記錄，虛擬網路上的網域服務會中斷。

## <a name="next-steps"></a>後續步驟

如需管理 DNS 的詳細資訊，請參閱 [Technet 上的 DNS 工具文章](https://technet.microsoft.com/library/cc753579.aspx)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
