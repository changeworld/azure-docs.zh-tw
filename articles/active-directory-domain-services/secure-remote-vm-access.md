---
title: Azure AD Domain Services 中的安全遠端 VM 存取 |Microsoft Docs
description: 瞭解如何使用網路原則伺服器 (NPS) 和 Azure Multi-Factor Authentication，透過 Azure Active Directory Domain Services 受控網域中的遠端桌面服務部署來保護對 Vm 的遠端存取。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 462e0ff8ef8baadf40ba81ed2ae137d8904fbf7a
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722512"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>在 Azure Active Directory Domain Services 中安全地遠端存取虛擬機器

若要保護虛擬機器的遠端存取 () 在 Azure Active Directory Domain Services (Azure AD DS) 受控網域中執行的 Vm，您可以使用遠端桌面服務 (RDS) 和網路原則伺服器 (NPS) 。 Azure AD DS 會在使用者透過 RDS 環境要求存取權時進行驗證。 為了加強安全性，您可以整合 Azure Multi-Factor Authentication，在登入事件期間提供額外的驗證提示。 Azure Multi-Factor Authentication 會使用 NPS 的延伸模組來提供此功能。

> [!IMPORTANT]
> 在 Azure AD DS 受控網域中安全地連線至 Vm 的建議方式是使用 Azure 防禦，這是您在虛擬網路內布建的全平臺受控 PaaS 服務。 防禦主機提供安全且順暢的遠端桌面通訊協定 (RDP) 透過 SSL 直接在 Azure 入口網站透過 SSL 連線到您的 Vm。 當您透過堡壘主機進行連線時，您的 Vm 不需要公用 IP 位址，而且您不需要使用網路安全性群組來公開 TCP 埠3389上的 RDP 存取。
>
> 強烈建議您在所有支援的區域中使用 Azure 防禦。 在沒有 Azure 防禦可用性的區域中，請遵循本文中詳述的步驟，直到有可用的 Azure 防禦。 請小心將公用 IP 位址指派給已加入至允許所有連入 RDP 流量的 Azure AD DS 的 Vm。
>
> 如需詳細資訊，請參閱 [什麼是 Azure 防禦？][bastion-overview]。

本文說明如何在 Azure AD DS 中設定 RDS，並選擇性地使用 Azure Multi-Factor Authentication NPS 擴充功能。

![遠端桌面服務 (RDS) 總覽](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* Azure Active Directory Domain Services 虛擬網路中建立的 *工作負載* 子網。
    * 如有需要，請 [設定 Azure Active Directory Domain Services 受控網域的虛擬網路][configure-azureadds-vnet]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>部署及設定遠端桌面環境

若要開始使用，請建立至少兩個執行 Windows Server 2016 或 Windows Server 2019 的 Azure Vm。 如需遠端桌面 (RD) 環境的冗余和高可用性，您可以在稍後新增額外的主機並對其進行負載平衡。

建議的 RDS 部署包含下列兩個 Vm：

* *RDGVM01* -執行 RD 連線代理人伺服器、RD Web 存取伺服器和 RD 閘道伺服器。
* *RDSHVM01* -執行 RD 工作階段主機伺服器。

確定 Vm 已部署至您 Azure AD DS 虛擬網路的 *工作負載* 子網，然後將 vm 加入受控網域。 如需詳細資訊，請參閱如何 [建立 Windows SERVER VM 並將其加入受控網域][tutorial-create-join-vm]。

RD 環境部署包含幾個步驟。 您可以使用現有的 RD 部署指南，而不需要在受控網域中使用任何特定變更：

1. 使用屬於 *AZURE AD DC 系統管理員* 群組的帳戶（例如 *contosoadmin*），登入為 RD 環境建立的 vm。
1. 若要建立和設定 RDS，請使用現有的 [遠端桌面環境部署指南][deploy-remote-desktop]。 視需要在您的 Azure Vm 上散發 RD 伺服器元件。
    * 特定于 Azure AD DS-當您設定 RD 授權時，請將它設定為 [ **每一裝置** ] 模式，而不是依 [部署指南] 中所述的 **每個使用者** 。
1. 如果您想要使用網頁瀏覽器提供存取權，請 [為您的使用者設定遠端桌面 web 用戶端][rd-web-client]。

將 RD 部署到受控網域之後，您就可以管理和使用服務，就像使用內部部署 AD DS 網域一樣。

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>部署及設定 NPS 和 Azure MFA NPS 擴充功能

如果您想要提高使用者登入體驗的安全性，您可以選擇性地將 RD 環境與 Azure Multi-Factor Authentication 整合。 使用此設定時，使用者會在登入期間收到額外的提示，以確認其身分識別。

為了提供這項功能，系統會在您的環境中安裝額外的網路原則伺服器 (NPS) ，以及 Azure Multi-Factor Authentication NPS 擴充功能。 此延伸模組會與 Azure AD 整合，以要求並傳回多重要素驗證提示的狀態。

使用者必須 [註冊才能使用 Azure Multi-Factor Authentication][user-mfa-registration]，這可能需要額外的 Azure AD 授權。

若要將 Azure Multi-Factor Authentication 整合到您的 Azure AD DS 遠端桌面環境中，請建立 NPS 伺服器，並安裝此延伸模組：

1. 建立額外的 Windows Server 2016 或 2019 VM （例如 *NPSVM01*），以連線至您 Azure AD DS 虛擬網路中的 *工作負載* 子網。 將 VM 加入受控網域。
1. 以屬於 *AZURE AD DC 系統管理員* 群組的帳戶（例如 *contosoadmin*）登入 NPS VM。
1. 從 **伺服器管理員**中，選取 [ **新增角色及功能**]，然後安裝 [ *網路原則與存取服務* ] 角色。
1. 使用現有的操作說明文章來 [安裝和設定 Azure MFA NPS 延伸][nps-extension]模組。

安裝 NPS 伺服器和 Azure Multi-Factor Authentication NPS 擴充功能之後，請完成下一節，設定它以用於 RD 環境。

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>整合遠端桌面閘道和 Azure Multi-Factor Authentication

若要整合 Azure Multi-Factor Authentication NPS 擴充功能，請使用現有的操作說明文章，以 [使用網路原則伺服器 (NPS) 延伸模組和 Azure AD 來整合您的遠端桌面閘道基礎結構][azure-mfa-nps-integration]。

需要下列額外的設定選項才能與受控網域整合：

1. 請勿 [在 Active Directory 中註冊 NPS 伺服器][register-nps-ad]。 此步驟會在受控網域中失敗。
1. 在 [設定網路原則的步驟 4][create-nps-policy]中，也請核取 [ **略過使用者帳戶撥入**內容] 核取方塊。
1. 如果您針對 NPS 伺服器和 Azure Multi-Factor Authentication NPS 擴充功能使用 Windows Server 2019，請執行下列命令來更新安全通道，以允許 NPS 伺服器正確通訊：

    ```powershell
    sc sidtype IAS unrestricted
    ```

使用者現在會在登入時提示您輸入額外的驗證因素，例如 Microsoft Authenticator 應用程式中的文字訊息或提示。

## <a name="next-steps"></a>後續步驟

如需改善部署復原的詳細資訊，請參閱 [遠端桌面服務-高可用性][rds-high-availability]。

如需保護使用者登入安全的詳細資訊，請參閱 [其運作方式： Azure Multi-Factor Authentication][concepts-mfa]。

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability