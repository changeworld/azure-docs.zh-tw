---
title: 將 Windows Server VM 加入 Azure AD Domain Services 受控網域 | Microsoft Docs
description: 在此教學課程中，了解如何將 Windows Server 虛擬機器加入 Azure Active Directory Domain Services 受控網域。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 8123608cbf2c1a4cbe0dc51d81d42b288bf2a91d
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024922"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>教學課程：將 Windows Server 虛擬機器加入 Azure Active Directory Domain Services 受控網域

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 使用 Azure AD DS 受控網域，您就可以在 Azure 中提供虛擬機器 (VM) 的網域加入功能和管理。 此教學課程說明如何建立 Windows Server VM，然後將其加入受控網域。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Windows Server VM
> * 將 Windows Server VM 連線到 Azure 虛擬網路
> * 將 VM 加入受控網域

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 屬於受控網域一部分的使用者帳戶。
    * 請確定已執行 Azure AD Connect 密碼雜湊同步或自助式密碼重設，讓帳戶能夠登入受控網域。
* 部署在 Azure AD DS 虛擬網路中的 Azure Bastion 主機。
    * 如有需要，請[建立 Azure Bastion 主機][azure-bastion]。

如果您已經有想要加入網域的 VM，請跳至[將 VM 加入受控網域](#join-the-vm-to-the-managed-domain)一節。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在此教學課程中，您會使用 Azure 入口網站建立 Windows Server VM，以加入您的受控網域。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-windows-server-virtual-machine"></a>建立 Windows Server 虛擬機器

為了解如何將電腦加入受控網域，讓我們建立 Windows Server VM。 此 VM 會連線到可與受控網域連線的 Azure 虛擬網路。 加入受控網域的程序，與加入一般內部部署 Active Directory Domain Services 網域的程序相同。

如果您已經有想要加入網域的 VM，請跳至[將 VM 加入受控網域](#join-the-vm-to-the-managed-domain)一節。

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。

1. 從 [開始使用] 中，選擇 [Windows Server 2016 Datacenter]。

    ![選擇在 Azure 入口網站中建立 Windows Server 2016 Datacenter VM](./media/join-windows-vm/select-vm-image.png)

1. 在 [基本] 視窗中，設定虛擬機器的核心設定。 保留 [可用性選項]、[映像] 及 [大小] 的預設設定。

    | 參數            | 建議的值   |
    |----------------------|-------------------|
    | 資源群組       | 選取或建立資源群組，例如 *myResourceGroup* |
    | 虛擬機器名稱 | 輸入 VM 的名稱，例如 *myVM* |
    | 區域               | 選擇要在其中建立 VM 的區域，例如「美國東部」 |
    | 使用者名稱             | 輸入要在 VM 上建立之本機系統管理員帳戶的使用者名稱，例如 *azureuser* |
    | 密碼             | 輸入要在 VM 上建立的本機系統管理員安全密碼，然後確認。 請勿指定網域使用者帳戶的認證。 |

1. 根據預設，您可以使用 RDP 從網際網路存取 Azure 中所建立的 VM。 啟用 RDP 時，可能會發生自動化登入攻擊，這可能會因為多次失敗的後續登入嘗試而停用具有一般名稱 (例如 *admin* 或 *administrator*) 的帳戶。

    RDP 應該只在必要時啟用，而且僅限於一組已授權的 IP 範圍。 此設定可協助改善 VM 的安全性，並減少潛在攻擊的範圍。 或者，請建立和使用只允許透過 TLS 從 Azure 入口網站存取的 Azure Bastion 主機。 在此教學課程的下一個步驟中，您需要使用 Azure Bastion 主機安全地連線到 VM。

    在 [公用輸入連接埠] 底下，選取 [無]。

1. 完成時，請選取 [下一步:磁碟]。
1. 從 [OS 磁碟類型] 的下拉式功能表中，選擇 [標準 SSD]，然後選取 [下一步:**網路]** 。
1. 您的 VM 必須連線到可與受控網域部署所在子網路通訊的 Azure 虛擬網路子網路。 我們建議將受控網域部署到其自身的專用子網路。 請勿將您的 VM 部署在與受控網域相同的子網路中。

    有兩種主要方式可部署您的 VM，並連線到適當的虛擬網路子網路：
    
    * 在與受控網域部署所在相同的虛擬網路中建立子網路或選取現有的子網路。
    * 使用 [Azure 虛擬網路對等互連][vnet-peering]，在 Azure 虛擬網路中選取與其連線的子網路。
    
    如果您選取的虛擬網路子網路未連線到受控網域的子網路，您就無法將 VM 加入受控網域。 在此教學課程中，我們將在 Azure 虛擬網路中建立新的子網路。

    在 [網路] 窗格中，選取受控網域部署所在的虛擬網路，例如 *aaads-vnet*
1. 在此範例中，會顯示受控網域連線至的現有 *aaads-subnet*。 請勿將您的 VM 連線到此子網路。 若要建立 VM 的子網路，請選取 [管理子網路設定]。

    ![選擇以在 Azure 入口網站中管理子網路設定](./media/join-windows-vm/manage-subnet.png)

1. 在虛擬網路視窗的左側功能表中，選取 [位址空間]。 建立虛擬網路時，會使用預設子網路所使用的單一位址空間 *10.0.2.0/24*。 其他適用於*工作負載*或 Azure Bastion 等的子網路也可能已經存在。

    將額外的 IP 位址範圍新增至虛擬網路。 此位址範圍的大小以及要使用的實際 IP 位址範圍，取決於已部署的其他網路資源。 IP 位址範圍不應與您 Azure 或內部部署環境中任何現有的位址範圍重疊。 請確定您的 IP 位址範圍大小足以容納預期要部署到子網路中的 VM 數目。

    在下列範例中，會新增額外的 IP 位址範圍 *10.0.5.0/24*。 在準備就緒時，選取 [儲存]。

    ![在 Azure 入口網站中新增額外的虛擬網路 IP 位址範圍](./media/join-windows-vm/add-vnet-address-range.png)

1. 接下來，在虛擬網路視窗的左側功能表中選取 [子網路]，然後選擇 [+ 子網路] 以新增子網路。

1. 選取 [+ 子網路]，然後輸入子網路的名稱，例如 *management*。 提供 [位址範圍 (CIDR 區塊)]，例如 *10.0.5.0/24*。 請確定此 IP 位址範圍不會與任何其他現有的 Azure 或內部部署位址範圍重疊。 保留其他選項的預設值，然後選取 [確定]。

    ![在 Azure 入口網站中建立子網路設定](./media/join-windows-vm/create-subnet.png)

1. 建立子網路需要幾秒鐘的時間。 建立之後，請選取 [X] 以關閉子網路視窗。
1. 回到 [網路] 窗格以建立 VM，從下拉式功能表中選擇您建立的子網路，例如 *management*。 再次強調，請務必選擇正確的子網路，而且不要將 VM 部署在與受控網域相同的子網路中。
1. 針對 [公用 IP]，從下拉式功能表中選取 [無]。 當您在本教學課程中使用 Azure Bastion 來連線到管理時，不需要指派給 VM 的公用 IP 位址。
1. 將其他選項保留為預設值，然後選取 [管理]。
1. 將 [開機診斷] 設定為 [關閉]。 將其他選項保留為預設值，然後選取 [檢閱 + 建立]。
1. 檢閱 VM 設定，然後選取 [建立]。

建立 VM 需要幾分鐘的時間。 Azure 入口網站會顯示部署的狀態。 VM 準備就緒後，選取 [前往資源]。

![成功建立後，請移至 Azure 入口網站中的 VM 資源](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>連線到 Windows Server VM

若要安全地連線到 VM，請使用 Azure Bastion 主機。 使用 Azure Bastion 時，受控主機會部署至您的虛擬網路，並提供對於 VM 的 Web 型 RDP 或 SSH 連線。 VM 不需要公用 IP 位址，而且您不需要為外部遠端流量開啟網路安全性群組規則。 您可以從網頁瀏覽器使用 Azure 入口網站來連線到 VM。 如有需要，請[建立 Azure Bastion 主機][azure-bastion]。

若要使用 Bastion 主機來連線到 VM，請完成下列步驟：

1. 在 VM 的 [概觀] 窗格中，依序選取 [連線] 和 [Bastion]。

    ![在 Azure 入口網站中使用 Bastion 連線到 Windows 虛擬機器](./media/join-windows-vm/connect-to-vm.png)

1. 輸入您在上一節為 VM 指定的認證，然後選取 [連線]。

   ![在 Azure 入口網站中透過 Bastion 主機連線](./media/join-windows-vm/connect-to-bastion.png)

如有需要，請允許網頁瀏覽器開啟快顯視窗以便顯示 Bastion 連線。 需要幾秒鐘的時間才能連線到 VM。

## <a name="join-the-vm-to-the-managed-domain"></a>將 VM 加入受控網域

使用 Azure Bastion 建立了 VM 與 Web 型 RDP 連線之後，現在讓我們將 Windows Server 虛擬機器加入受控網域。 此程序與連線到一般內部部署 Active Directory Domain Services 網域的電腦相同。

1. 如果 [伺服器管理員] 在您登入 VM 時並未預設為開啟狀態，請選取 [開始] 功能表，然後選擇 [伺服器管理員]。
1. 在 [伺服器管理員] 視窗的左窗格中，選取 [本機伺服器]。 在右窗格的 [屬性] 下，選擇 [工作群組]。

    ![在 VM 上開啟 [伺服器管理員] 並編輯工作群組屬性](./media/join-windows-vm/server-manager.png)

1. 在 [系統屬性] 視窗中，選取 [變更] 來加入受控網域。

    ![選擇以變更工作群組或網域屬性](./media/join-windows-vm/change-domain.png)

1. 在 [網域] 方塊中，指定受控網域的名稱 (例如 aaddscontoso.com)，然後選取 [確定]。

    ![指定要加入的受控網域](./media/join-windows-vm/join-domain.png)

1. 輸入網域認證以加入網域。 為屬於受控網域的使用者提供認證。 帳戶必須是受控網域的一部分，或 Azure AD 租用戶。與您的 Azure AD 租用戶相關聯之外部目錄中的帳戶，在網域加入程序期間無法正確地進行驗證。

    帳戶認證可以利用下列其中一種方式來指定：

    * **UPN 格式** (建議) - 輸入 Azure AD 中所設定使用者帳戶的使用者主體名稱 (UPN) 尾碼。 例如，使用者 *contosoadmin* 的 UPN 尾碼會是 `contosoadmin@aaddscontoso.onmicrosoft.com`。 有幾個常見的使用案例，其中 UPN 格式可以可靠地用來登入網域，而不是使用 *SAMAccountName* 格式：
        * 如果使用者的 UPN 前置詞太長 (例如 *deehasareallylongname*)，就可能自動產生 *SAMAccountName*。
        * 如果您的 Azure AD 租用戶中有多個使用者擁有相同的 UPN 前置詞 (例如 *dee*)，則其 *SAMAccountName* 格式可能會自動產生。
    * **SAMAccountName 格式** - 以 *SAMAccountName* 格式輸入帳戶名稱。 例如，使用者 *contosoadmin* 的 *SAMAccountName* 會是 `AADDSCONTOSO\contosoadmin`。

1. 需要幾秒鐘的時間才能加入受控網域。 完成時，會有下列訊息歡迎您加入網域：

    ![歡迎加入網域](./media/join-windows-vm/join-domain-successful.png)

    選取 [確定]  以繼續操作。

1. 若要完成加入受控網域的程序，請重新啟動 VM。

> [!TIP]
> 您可以使用 PowerShell 搭配 [Add-Computer][add-computer] Cmdlet，將 VM 加入網域。 下列範例會加入 AADDSCONTOSO 網域，然後重新啟動 VM。 出現提示時，請輸入屬於受控網域一部分之使用者的認證：
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> 若要在未連線的情況下將 VM 加入網域，並手動設定連線，您可以使用 [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell Cmdlet。

Windows Server VM 重新啟動之後，在受控網域中套用的任何原則都會推送至 VM。 您現在也可以使用適當的網域認證來登入 Windows Server VM。

## <a name="clean-up-resources"></a>清除資源

在下一個教學課程中，您會使用此 Windows Server VM 來安裝可讓您管理受控網域的管理工具。 如果您不想要繼續進行此系列教學課程，請檢閱下列清除步驟來[刪除 VM](#delete-the-vm)。 否則，請[繼續下一個教學課程](#next-steps)。

### <a name="unjoin-the-vm-from-the-managed-domain"></a>讓 VM 退出受控網域

若要從受控網域中移除 VM，請再次執行下列步驟，[將 VM 加入網域](#join-the-vm-to-the-managed-domain)。 這次請加入工作群組 (例如預設的 *WORKGROUP*)，而不是加入受控網域。 VM 重新開機之後，電腦物件就會從受控網域中移除。

如果您未先退出網域即[刪除 VM](#delete-the-vm)，則孤立的電腦物件將會留在 Azure AD DS 中。

### <a name="delete-the-vm"></a>刪除 VM

如果您不打算使用此 Windows Server VM，請使用下列步驟來刪除 VM：

1. 從左側功能表，選取 [資源群組]
1. 選擇您的資源群組，例如 *myResourceGroup*。
1. 選擇您的 VM (例如 *myVM*)，然後選取 [網路]。 選取 [是] 以確認刪除資源。 刪除 VM 需要幾分鐘的時間。
1. 刪除 VM 時，請選取具有 *myVM-* 首碼的 OS 磁碟、網路介面卡與任何其他資源，並將它們刪除。

## <a name="troubleshoot-domain-join-issues"></a>針對網域加入問題進行疑難排解

Windows Server VM 應該成功加入受控網域，其方式與一般內部部署電腦加入 Active Directory Domain Services 網域相同。 如果 Windows Server VM 無法加入受控網域，則表示有連線能力或認證相關問題。 請參閱下列疑難排解小節，以成功加入受控網域。

### <a name="connectivity-issues"></a>連線能力問題

如果您未收到要求認證以加入網域的提示，表示發生連線問題。 VM 無法連線到虛擬網路上的受控網域。

嘗試下列每個疑難排解步驟之後，請再次嘗試將 Windows Server VM 加入受控網域。

* 確認 VM 已連線至已啟用 Azure AD DS 的相同虛擬網路，或具有對等互連網路連線。
* 嘗試 ping 受控網域的 DNS 網域名稱，例如 `ping aaddscontoso.com`。
    * 如果偵測要求失敗，請嘗試 ping 受控網域的 IP 位址，例如 `ping 10.0.0.4`。 當您從 Azure 資源清單選取受控網域時，您的環境的 IP 位址會顯示在 [屬性] 頁面上。
    * 如果您可以 ping 該 IP 位址，但無法 ping 網域，則表示 DNS 的設定可能不正確。 確認受控網域的 IP 位址是否設定為虛擬網路的 DNS 伺服器。
* 請嘗試使用 `ipconfig /flushdns` 命令排清虛擬機器上的 DNS 解析程式快取。

### <a name="credentials-related-issues"></a>與認證相關的問題

如果您收到要求認證以加入網域的提示，但在您輸入那些認證之後發生錯誤，則 VM 可以連線到受控網域。 您提供的認證不會讓 VM 加入受控網域。

嘗試下列每個疑難排解步驟之後，請再次嘗試將 Windows Server VM 加入受控網域。

* 確定您所指定的使用者帳戶屬於受控網域。
* 確認帳戶屬於受控網域或 Azure AD 租用戶。 與您的 Azure AD 租用戶相關聯之外部目錄中的帳戶，在網域加入程序期間無法正確地進行驗證。
* 嘗試使用 UPN 格式來指定認證，例如 `contosoadmin@aaddscontoso.onmicrosoft.com`。 如果您的租用戶中有許多使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，系統可能就會自動為您的帳戶產生 *SAMAccountName*。 在這些情況下，您帳戶的 *SAMAccountName* 格式可能會與您在內部部署網域中預期或使用的格式不同。
* 確認您已為受控網域[啟用密碼同步化][password-sync]。 如果沒有此設定步驟，所需的密碼雜湊就不會出現在受控網域中，以正確地驗證您的登入嘗試。
* 等待密碼同步完成。 當使用者帳戶的密碼變更時，來自 Azure AD 的自動背景同步處理會更新 Azure AD DS 中的密碼。 需要一些時間，密碼才能用於加入網域。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 Windows Server VM
> * 將 Windows Server VM 連線到 Azure 虛擬網路
> * 將 VM 加入受控網域

若要管理受控網域，請使用 Active Directory 管理中心 (ADAC) 設定管理 VM。

> [!div class="nextstepaction"]
> [在管理 VM 上安裝系統管理工具](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
