---
title: 教學課程 - 為 Azure Active Directory Domain Services 建立管理 VM | Microsoft Docs
description: 在本教學課程中，您將了解如何建立及設定用來管理 Azure Active Directory Domain Services 受控網域的 Windows 虛擬機器。
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 52a97b824824d8e9eaf79cfa4a447494bf1525cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175146"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>教學課程：建立管理 VM 來設定及管理 Azure Active Directory Domain Services 受控網域

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如，網域加入、群組原則、LDAP 和 Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您會使用與內部部署 Active Directory Domain Services 網域搭配的相同遠端伺服器管理工具 (RSAT) 來管理此受控網域。 因為 Azure AD DS 是受控服務，所以有一些您無法執行的管理工作，例如使用遠端桌面通訊協定 (RDP) 連線到網域控制站。

本教學課程將說明如何在 Azure 中設定 Windows Server VM，並安裝必要的工具來管理 Azure AD DS 受控網域。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 了解受控網域中可執行的管理工作
> * 在 Windows Server VM 上安裝 Active Directory 管理工具
> * 使用 Active Directory 管理中心執行一般工作

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請參閱第一個教學課程，以[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 已加入受控網域的 Windows Server VM。
    * 如有需要，請參閱先前的教學課程，以[建立 Windows Server VM 並將其加入受控網域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。
* 部署在 Azure AD DS 虛擬網路中的 Azure Bastion 主機。
    * 如有需要，請[建立 Azure Bastion 主機][azure-bastion]。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您會使用 Azure 入口網站來建立和設定管理 VM。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Azure AD DS 中可執行的管理工作

Azure AD DS 會為您的使用者、應用程式和服務提供可取用的受控網域。 此方法會變更您可以執行的一些管理工作，以及您在受控網域內擁有的權限。 這些工作和權限可能會與您在一般內部部署 Active Directory Domain Services 環境中所遇到的不同。 您也無法使用遠端桌面連線至受控網域上的網域控制站。

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>可在受控網域中執行的系統管理工作

*AAD DC 系統管理員* 群組的成員已獲授與受控網域的權限，這些權限讓他們可以進行如下工作：

* 為受控網域中的「AADDC 電腦」和「AADDC 使用者」容器設定內建群組原則物件 (GPO)。
* 管理受控網域上的 DNS。
* 建立及管理受控網域上的自訂組織單位 (OU)。
* 取得已加入受控網域之電腦的系統管理存取權。

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>您未擁有的受控網域系統管理權限

網域已被鎖定，因此您沒有在網域上進行特定管理工作的權限。 下列幾個範例是您無法執行的工作：

* 延伸受控網域的結構描述。
* 使用遠端桌面連線到受控網域的網域控制站。
* 在受控網域中新增網域控制站。
* 您不會擁有受控網域的「網域系統管理員」或「企業系統管理員」權限。

## <a name="sign-in-to-the-windows-server-vm"></a>登入 Windows Server VM

在上一個教學課程中，我們已建立 Windows Server VM 並將其加入受控網域。 使用該 VM 來安裝管理工具。 如有需要，[請依照教學課程中的步驟來建立 Windows Server VM 並將其加入受控網域][create-join-windows-vm]。

> [!NOTE]
> 在本教學課程中，您將在 Azure 中使用已加入受控網域的 Windows Server VM。 您也可以使用已加入受控網域的 Windows 用戶端 (例如 Windows 10)。
>
> 如需有關如何在 Windows 用戶端上安裝管理工具的詳細資訊，請參閱[安裝遠端伺服器管理工具 (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

若要開始，請連線到 Windows Server VM，如下所示：

1. 從 Azure 入口網站的左側選取 [資源群組]。 選擇建立您 VM 的資源群組，例如 myResourceGroup，然後選取 VM，例如 myVM。
1. 在 VM 的 [概觀] 窗格中，依序選取 [連線] 和 [Bastion]。

    ![在 Azure 入口網站中使用 Bastion 連線到 Windows 虛擬機器](./media/join-windows-vm/connect-to-vm.png)

1. 輸入 VM 的認證，然後選取 [連線]。

   ![在 Azure 入口網站中透過 Bastion 主機連線](./media/join-windows-vm/connect-to-bastion.png)

如有需要，請允許網頁瀏覽器開啟快顯視窗以便顯示 Bastion 連線。 需要幾秒鐘的時間才能連線到 VM。

## <a name="install-active-directory-administrative-tools"></a>安裝 Active Directory 管理工具

您可以在受控網域中使用與內部部署 AD DS 環境相同的系統管理工具，例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell 等。 在 Windows Server 和用戶端電腦上，可以將這些工具安裝為遠端伺服器管理工具 (RSAT) 功能的一部分。 然後，「AAD DC 系統管理員」群組的成員可使用這些 AD 管理工具，從已加入受控網域的電腦遠端管理受控網域。

若要在已加入網域的 VM 上安裝 Active Directory 管理工具，請完成下列步驟：

1. 如果 [伺服器管理員] 在您登入 VM 時並未預設為開啟狀態，請選取 [開始] 功能表，然後選擇 [伺服器管理員]。
1. 在 [伺服器管理員] 視窗的 [儀表板] 窗格內，選取 [新增角色及功能]。
1. 在 [新增角色及功能精靈] 的 [開始之前] 頁面上，選取 [下一步]。
1. 針對 [安裝類型]，保持勾選 [角色型或功能型安裝] 選項，然後選取 [下一步]。
1. 在 [伺服器選擇] 頁面上，從伺服器集區中選擇目前的 VM，例如 myvm.aaddscontoso.com，然後選取 [下一步]。
1. 在 [伺服器角色] 頁面上，按 [下一步]。
1. 在 [功能] 頁面上，展開 [遠端伺服器管理工具] 節點，然後展開 [角色管理工具] 節點。

    從角色管理工具的清單中選擇 [AD DS 及 AD LDS 工具]  功能，然後選取 [下一步]。

    ![從 [功能] 頁面安裝 [AD DS 和 AD LDS 工具]](./media/tutorial-create-management-vm/install-features.png)

1. 在 [確認] 頁面上，選取 [安裝]。 安裝管理工具可能需要一或兩分鐘的時間。
1. 完成功能安裝時，請選取 [關閉] 以結束 [新增角色及功能] 精靈。

## <a name="use-active-directory-administrative-tools"></a>使用 Active Directory 管理工具

安裝管理工具之後，我們將了解如何使用這些工具來管理受控網域。 請確定您已使用屬於「AAD DC 系統管理員」群組成員的使用者帳戶登入 VM。

1. 在 [開始] 功能表中選取 [Windows 管理工具]。 先前步驟中所安裝的 AD 管理工具會列在其中。

    ![安裝在伺服器上的管理工具清單](./media/tutorial-create-management-vm/list-admin-tools.png)

1. 選取 [Active Directory 管理中心]。
1. 若要探索受控網域，請在左側窗格中選擇網域名稱，例如 aaddscontoso。 名為「AADDC 電腦」和「AADDC 使用者」的兩個容器位於清單頂端。

    ![列出受控網域中可用的容器部分](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. 若要查看屬於受控網域的使用者和群組，請選取 [AADDC 使用者] 容器。 此容器中會列出您 Azure AD 租用戶中的使用者帳戶和群組。

    在下列範例輸出中，名為 *Contoso Admin* 的使用者帳戶和「AAD DC 系統管理員」群組會顯示在此容器中。

    ![查看 Active Directory 管理中心中的 Azure AD DS 網域使用者清單](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. 若要查看已加入受控網域的電腦，請選取 [AADDC 電腦] 容器。 目前虛擬機器的項目(例如 myVM) 會隨即列出。 針對所有已加入受控網域的裝置，其電腦帳戶會儲存在此「AADDC 電腦」容器中。

您可以使用常見的 Active Directory 管理中心動作，例如重設使用者帳戶密碼或管理群組成員資格。 這些動作僅適用於直接在受控網域中建立的使用者和群組。 身分識別資訊只會從 Azure AD 同步處理到 Azure AD DS。 不會從 Azure AD DS 寫回 Azure AD。 對於從 Azure AD 同步處理的使用者，您無法變更密碼或受控群組成員資格，並將這些變更會反向同步處理。

您也可以使用「適用於 Windows PowerShell 的 Active Directory 模組」(已安裝為系統管理工具的一部分) 來管理受控網域中的一般動作。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 了解受控網域中可執行的管理工作
> * 在 Windows Server VM 上安裝 Active Directory 管理工具
> * 使用 Active Directory 管理中心執行一般工作

若要從其他應用程式安全地與您的受控網域互動，請啟用安全的輕量型目錄存取通訊協定 (LDAPS)。

> [!div class="nextstepaction"]
> [為受控網域設定安全 LDAP](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md