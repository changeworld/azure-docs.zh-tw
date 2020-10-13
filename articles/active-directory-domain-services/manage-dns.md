---
title: 管理 Azure AD Domain Services 的 DNS |Microsoft Docs
description: 瞭解如何安裝 DNS 伺服器工具來管理 DNS，以及建立 Azure Active Directory Domain Services 受控網域的條件轉寄站。
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: b347f8043216d4347099d68ff1c62156410582a3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963171"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>管理 DNS 並在 Azure Active Directory Domain Services 受控網域中建立條件轉寄站

在 Azure Active Directory Domain Services (Azure AD DS) 中，金鑰元件是 DNS (功能變數名稱解析) 。 Azure AD DS 包含可提供受控網域名稱解析的 DNS 伺服器。 此 DNS 伺服器包含內建的 DNS 記錄，以及允許服務執行的重要元件更新。

當您執行自己的應用程式和服務時，您可能需要為未加入網域的電腦建立 DNS 記錄、設定負載平衡器的虛擬 IP 位址，或設定外部 DNS 轉寄站。 屬於 *AAD DC 系統管理員* 群組的使用者會被授與 Azure AD DS 受控網域上的 DNS 系統管理許可權，而且可以建立和編輯自訂 DNS 記錄。

在混合式環境中，在其他 DNS 命名空間中設定的 DNS 區域和記錄（例如內部部署 AD DS 環境）不會同步處理至受控網域。 若要解析其他 DNS 命名空間中的已命名資源，請建立和使用指向環境中現有 DNS 伺服器的條件轉寄站。

本文說明如何安裝 DNS 伺服器工具，然後使用 DNS 主控台來記錄管理，並在 Azure AD DS 中建立條件轉寄站。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以 [建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 從您的 Azure AD DS 虛擬網路連線到其他 DNS 命名空間的裝載位置。
    * 您可以使用 [Azure ExpressRoute][expressroute] 或 [azure VPN 網][vpn-gateway] 關聯機來提供此連線能力。
* 已加入受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以 [建立 Windows SERVER VM，並將它加入受控網域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="install-dns-server-tools"></a>安裝 DNS 伺服器工具

若要在受控網域中建立和修改 DNS 記錄，您必須安裝 DNS 伺服器工具。 這些工具可以安裝為 Windows Server 中的一項功能。 如需有關如何在 Windows 用戶端上安裝系統管理工具的詳細資訊，請參閱安裝 [遠端伺服器管理工具 (RSAT) ][install-rsat]。

1. 登入您的管理 VM。 如需如何使用 Azure 入口網站連接的步驟，請參閱連線 [到 Windows SERVER VM][connect-windows-server-vm]。
1. 如果 [伺服器管理員] 在您登入 VM 時並未預設為開啟狀態，請選取 [開始] 功能表，然後選擇 [伺服器管理員]。
1. 在 [伺服器管理員] 視窗的 [儀表板] 窗格內，選取 [新增角色及功能]。
1. 在 [新增角色及功能精靈] 的 [開始之前] 頁面上，選取 [下一步]。
1. 針對 [安裝類型]，保持勾選 [角色型或功能型安裝] 選項，然後選取 [下一步]。
1. 在 [伺服器選擇] 頁面上，從伺服器集區中選擇目前的 VM，例如 myvm.aaddscontoso.com，然後選取 [下一步]。
1. 在 [伺服器角色] 頁面上，按 [下一步]。
1. 在 [功能] 頁面上，展開 [遠端伺服器管理工具] 節點，然後展開 [角色管理工具] 節點。 從角色管理工具清單中，選取 [DNS 伺服器工具]**** 功能。

    ![從可用的角色管理工具清單選擇安裝 DNS 伺服器工具](./media/manage-dns/install-dns-tools.png)

1. 在 [確認] 頁面上，選取 [安裝]。 安裝 DNS 伺服器工具可能需要一或兩分鐘的時間。
1. 完成功能安裝時，請選取 [關閉] 以結束 [新增角色及功能] 精靈。

## <a name="open-the-dns-management-console-to-administer-dns"></a>開啟 DNS 管理主控台以管理 DNS

安裝 DNS 伺服器工具之後，您就可以管理受控網域上的 DNS 記錄。

> [!NOTE]
> 若要管理受控網域中的 DNS，您必須登入屬於 *AAD DC 系統管理員* 群組成員的使用者帳戶。

1. 從開始畫面選取 [系統 **管理工具**]。 其中會顯示可用的管理工具清單，包括上一節中安裝的 **DNS** 。 選取 [ **dns** ] 以啟動 dns 管理主控台。
1. 在 [連線 **到 DNS 伺服器** ] 對話方塊中，選取 **[下列電腦**]，然後輸入受控網域的 DNS 功能變數名稱，例如 *aaddscontoso.com*：

    ![連接到 DNS 主控台中的受控網域](./media/manage-dns/connect-dns-server.png)

1. DNS 主控台會連接到指定的受控網域。 展開 [ **正向對應區域** ] 或 [ **反向對應區域** ]，以建立必要的 DNS 專案，或視需要編輯現有的記錄。

    ![DNS 主控台 - 管理網域](./media/manage-dns/dns-manager.png)

> [!WARNING]
> 當您使用 DNS 伺服器工具記錄管理時，請確定您不會刪除或修改 Azure AD DS 所使用的內建 DNS 記錄。 內建 DNS 記錄包括網域 DNS 記錄、名稱伺服器記錄和其他用於 DC 位置的記錄。 如果您修改這些記錄，虛擬網路上的網域服務會中斷。

## <a name="create-conditional-forwarders"></a>建立條件式轉寄站

Azure AD DS DNS 區域應只包含受控網域本身的區域和記錄。 請勿在受控網域中建立其他區域，以解析其他 DNS 命名空間中的已命名資源。 相反地，請在受控網域中使用條件式轉寄站，告訴 DNS 伺服器要將這些資源的位址解析至何處。

條件轉寄站是 DNS 伺服器中的設定選項，可讓您定義 DNS 網域（例如 *contoso.com*），以將查詢轉送至。 系統會將 DNS 查詢轉送至該網域的已設定 DNS，而不是在本機 DNS 伺服器嘗試解析該網域中記錄的查詢。 這項設定可確保傳回正確的 DNS 記錄，因為您不會在受控網域中建立具有重複記錄的本機 DNS 區域，以反映這些資源。

若要在受控網域中建立條件轉寄站，請完成下列步驟：

1. 選取您的 DNS 區域，例如 *aaddscontoso.com*。
1. 選取**條件**式轉寄站，然後以滑鼠右鍵選取並選擇 [**新增條件**轉寄站]。
1. 輸入您的其他 **Dns 網域**（例如 *contoso.com*），然後為該命名空間輸入 DNS 伺服器的 IP 位址，如下列範例所示：

    ![新增及設定 DNS 伺服器的條件轉寄站](./media/manage-dns/create-conditional-forwarder.png)

1. 核取 [ **在 Active Directory 中儲存此條件**轉寄站的方塊，並將其複寫如下，然後為 *此網域中的所有 DNS 伺服器*選取選項，如下列範例所示：

    ![DNS 主控台-選取此網域中的所有 DNS 伺服器](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 如果條件轉寄站是儲存在 *樹* 系中，而不是 *網域*，則條件式轉寄站會失敗。

1. 若要建立條件式轉寄站，請選取 **[確定]**。

從連線到受控網域的 Vm，其他命名空間中資源的名稱解析現在應該會正確地解析。 條件轉寄站中設定之 DNS 網域的查詢會傳遞至相關的 DNS 伺服器。

## <a name="next-steps"></a>後續步驟

如需管理 DNS 的詳細資訊，請參閱 [Technet 上的 DNS 工具文章](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11))。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh