---
title: 管理 Azure AD 網域服務的 DNS |微軟文件
description: 瞭解如何安裝 DNS 伺服器工具以管理 Azure 活動目錄域服務託管域的 DNS。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f0d8f73b47b1110e8e05365013bbf07fd94eb6ca
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655070"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 網域服務託管網域中管理 DNS

在 Azure 活動目錄域服務 (Azure AD DS) 中,關鍵元件是 DNS(網域名稱解析)。 Azure AD DS 包括 DNS 伺服器,該伺服器為託管域提供名稱解析。 此 DNS 伺服器包括允許服務運行的關鍵元件的內建 DNS 記錄和更新。

在執行自己的應用程式和服務時,您可能需要為未加入網域的電腦創建 DNS 記錄,為負載均衡器配置虛擬 IP 位址,或設定外部 DNS 轉寄站。 屬於*AAD DC 管理員*組的使用者在 Azure AD DS 託管域上被授予 DNS 管理許可權,並可以創建和編輯自訂 DNS 記錄。

在混合環境中,在本地 ADDS 環境中配置的 DNS 區域和記錄不會同步到 Azure AD DS。 要定義和使用自己的 DNS 條目,請在 Azure AD DS DNS 伺服器中創建記錄,或使用指向環境中現有 DNS 伺服器的條件轉寄站。

本文介紹如何安裝 DNS 伺服器工具,然後使用 DNS 控制台管理 Azure AD DS 中的記錄。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要,請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要,請完成[建立 Windows 伺服器 VM 的教學並將加入託管域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

## <a name="install-dns-server-tools"></a>安裝 DNS 伺服器工具

要在 Azure AD DS 中建立和修改 DNS 記錄,需要安裝 DNS 伺服器工具。 這些工具可以作為功能安裝在 Windows 伺服器中。 有關如何在 Windows 用戶端上安裝管理工具的詳細資訊,請參閱安裝[遠端伺服器管理工具 (RSAT)。][install-rsat]

1. 登錄到管理 VM。 有關如何使用 Azure 門戶進行連接的步驟,請參閱[連接到 Windows 伺服器 VM][connect-windows-server-vm]。
1. 如果 [伺服器管理員]**** 在您登入 VM 時並未預設為開啟狀態，請選取 [開始]**** 功能表，然後選擇 [伺服器管理員]****。
1. 在 [伺服器管理員]**** 視窗的 [儀表板]** 窗格內，選取 [新增角色及功能]****。
1. 在 [新增角色及功能精靈]**** 的 [開始之前]** 頁面上，選取 [下一步]****。
1. 針對 [安裝類型]**，保持勾選 [角色型或功能型安裝]**** 選項，然後選取 [下一步]****。
1. 在 **「伺服器選擇**」頁上,從伺服器池中選擇當前 VM,如*myvm.aaddscontoso.com,* 然後選擇 **「下一步**」 。
1. 在 [伺服器角色]**** 頁面上，按 [下一步]****。
1. 在 [功能]**** 頁面上，展開 [遠端伺服器管理工具]**** 節點，然後展開 [角色管理工具]**** 節點。 從角色管理工具清單中，選取 [DNS 伺服器工具]**** 功能。

    ![選擇從可用角色管理工具清單中安裝 DNS 伺服器工具](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. 在 [確認]**** 頁面上，選取 [安裝]****。 安裝組策略管理工具可能需要一兩分鐘。
1. 完成功能安裝時，請選取 [關閉]**** 以結束 [新增角色及功能]**** 精靈。

## <a name="open-the-dns-management-console-to-administer-dns"></a>開啟 DNS 管理主控台以管理 DNS

安裝 DNS 伺服器工具後,可以在 Azure AD DS 託管域上管理 DNS 記錄。

> [!NOTE]
> 若要在 Azure AD DS 託管域中管理 DNS,必須登入*屬於 AAD DC 管理員*組的使用者帳戶。

1. 在"開始"螢幕中,選擇 **"管理工具**"。 將顯示可用管理工具的清單,包括上一節中安裝的**DNS。** 選擇**DNS**以啟動 DNS 管理主控台。
1. 在 **'SEL'** 對話框**中,選擇以下電腦**,然後輸入託管域的 DNS 網域名稱,如*aaddscontoso.com*:

    ![連接到 DNS 控制中的 Azure AD DS 託管域](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS 主控台連接到指定的 Azure AD DS 託管域。 展開**前瞻查找區域**或**反向查找區域**以創建所需的 DNS 條目或根據需要編輯現有記錄。

    ![DNS 主控台 - 管理網域](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> 使用 DNS 伺服器工具管理記錄時,請確保不要刪除或修改 Azure AD DS 使用的內建 DNS 記錄。 內建 DNS 記錄包括網域 DNS 記錄、名稱伺服器記錄和其他用於 DC 位置的記錄。 如果您修改這些記錄，虛擬網路上的網域服務會中斷。

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
