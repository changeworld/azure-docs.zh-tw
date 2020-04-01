---
title: 在 Azure AD 網域服務中保護遠端 VM 訪問 |微軟文件
description: 瞭解如何使用網路策略伺服器 (NPS) 和 Azure 多重身份驗證在 Azure 活動目錄域服務託管域中的遠端桌面服務部署保護對 VM 的遠端存取。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423141"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>保護對 Azure 活動目錄網域服務中的虛擬機器的遠端存取

要保護對在 Azure 活動目錄域服務 (Azure AD DS) 託管域中運行的虛擬機器 (VM) 的遠端訪問,可以使用遠端桌面服務 (RDS) 和網路策略伺服器 (NPS)。 Azure AD DS 在使用者請求透過 RDS 環境進行訪問時對其進行身份驗證。 為了增強安全性,可以集成 Azure 多重身份驗證,在登入事件期間提供其他身份驗證提示。 Azure 多重身份驗證使用 NPS 的擴展來提供此功能。

> [!IMPORTANT]
> 在 Azure AD DS 託管域中安全地連接到 VM 的推薦方法是使用 Azure Bastion,這是一種在虛擬網路內預配的完全平臺託管的 PaaS 服務。 堡壘主機透過 SSL 直接在 Azure 門戶中提供與 VM 的安全無縫遠端桌面協定 (RDP) 連接。 通過堡壘主機進行連接時,VM 不需要公共 IP 位址,並且不需要使用網路安全組來公開對 TCP 埠 3389 上的 RDP 的訪問。
>
> 我們強烈建議您在支援 Azure 堡壘的所有區域中使用 Azure 堡壘。 在沒有 Azure 堡壘可用性的區域中,請按照本文中詳細介紹的步驟操作,直到 Azure 堡壘可用。 注意將公共 IP 位址分配給連接到 Azure AD DS 的 VM,允許所有傳入 RDP 流量。
>
> 有關詳細資訊,請參閱什麼是[Azure 堡壘?][bastion-overview]

本文介紹如何在 Azure AD DS 中配置 RDS,並選擇性地使用 Azure 多重身份驗證 NPS 擴展。

![遠端桌面服務 (RDS) 概述](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Prerequisites

要完成本文,您需要以下資源:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 在 Azure 活動目錄域服務虛擬網路中創建的*工作負載*子網。
    * 如果需要,[為 Azure 的目錄網域服務託管網域設定虛擬網路][configure-azureadds-vnet]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>部署與設定遠端桌面環境

要開始,請至少創建兩個運行 Windows Server 2016 或 Windows Server 2019 的 Azure VM。 對於遠端桌面 (RD) 環境的冗餘性和高可用性,您可以稍後添加和負載平衡其他主機。

建議的 RDS 部署包括以下兩個 VM:

* *RDGVM01* - 執行 RD 連接代理伺服器、RD Web 存取伺服器和 RD 閘道伺服器。
* *RDSHVM01* - 運行 RD 工作階段主機伺服器。

確保 VM 已部署到 Azure AD DS 虛擬網路*的工作負載*子網,然後將 VM 加入 Azure AD DS 託管域。 有關詳細資訊,請參閱如何[建立 Windows 伺服器 VM 並將其加入到 Azure AD DS 託管域][tutorial-create-join-vm]。

RD 環境部署包含許多步驟。 無需在 Azure AD DS 託管域中使用任何特定變更即可使用現有的 RD 部署指南:

1. 使用屬於*Azure AD DC 管理員*群組的帳戶(如*contosoadmin)* 登錄到為 RD 環境創建的 VM。
1. 要建立與設定 RDS,請使用現有的[遠端桌面環境部署指南][deploy-remote-desktop]。 根據需要在 Azure VM 中分發 RD 伺服器元件。
1. 如果要使用 Web 瀏覽器提供存取權限,請[為使用者設定遠端桌面 Web 用戶端][rd-web-client]。

將 RD 部署到 Azure AD DS 託管域中,您可以像使用本地 ADDS 域一樣管理和使用服務。

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>部署與設定 NPS 與 Azure MFA NPS 延伸

如果要提高使用者登錄體驗的安全性,可以選擇將 RD 環境與 Azure 多重身份驗證集成。 使用此配置,使用者在登錄期間會收到一個額外的提示,以確認其身份。

要提供此功能,在您的環境中安裝一個額外的網路策略伺服器 (NPS) 以及 Azure 多重身份驗證 NPS 擴展。 此擴展與 Azure AD 整合,以請求並返回多重身份驗證提示的狀態。

必須註冊使用者[才能使用 Azure 多重身份驗證][user-mfa-registration],這可能需要其他 Azure AD 許可證。

要將 Azure 多重身份驗證整合到 Azure AD DS 遠端桌面環境中,請建立 NPS 伺服器並安裝延伸:

1. 創建連接到 Azure AD DS 虛擬網路中*的工作負載*子網路的其他 Windows 伺服器 2016 或 2019 VM,例如*NPSVM01。* 將 VM 加入 Azure AD DS 託管域。
1. 作為*Azure AD DC 管理員*組(如*contosoadmin)* 的一部分的帳戶登錄到 NPS VM。
1. 從**伺服器管理員**中,選擇 **「添加角色和功能**」,然後安裝*網路策略和存取服務*角色。
1. 使用現有的「執行」文章[安裝與設定 Azure MFA NPS 延伸][nps-extension]。

安裝 NPS 伺服器和 Azure 多重身份驗證 NPS 擴展後,完成下一節以將其配置為與 RD 環境一起使用。

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>整合遠端桌面閘道與 Azure 多重身份驗證

要整合 Azure 多重身份驗證 NPS 延伸,請使用現有的操作文章[使用網路策略伺服器 (NPS) 延伸與 Azure AD 整合遠端桌面閘道基礎結構][azure-mfa-nps-integration]。

與 Azure AD DS 託管域整合需要以下其他設定選項:

1. 不要在[作用目錄中註冊 NPS 伺服器][register-nps-ad]。 此步驟在 Azure AD DS 託管域中失敗。
1. 在[步驟 4 中設定網路政策][create-nps-policy],還選取「**忽略使用者帳戶撥入屬性」 複選框**。
1. 如果對 NPS 伺服器使用 Windows Server 2019 和 Azure 多重身份驗證 NPS 擴展,請執行以下指令以更新安全通道以允許 NPS 伺服器正確通訊:

    ```powershell
    sc sidtype IAS unrestricted
    ```

現在,使用者登錄時會提示他們輸入其他身份驗證因素,例如 Microsoft 身份驗證器應用中的簡訊或提示。

## <a name="next-steps"></a>後續步驟

有關提高部署恢復能力的詳細資訊,請參閱[遠端桌面服務 - 高可用性][rds-high-availability]。

有關保護使用者登錄的詳細資訊,請參閱[其工作原理:Azure 多重身份驗證][concepts-mfa]。

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
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
