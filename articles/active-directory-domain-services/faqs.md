---
title: 關於 Azure AD Domain Services 的常見問題 |Microsoft Docs
description: 閱讀並瞭解 Azure Active Directory Domain Services 的設定、系統管理和可用性方面的一些常見問題
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
ms.openlocfilehash: cc78df7ea904bf85f5f2561319e6fc773244e971
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005208"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>關於 Azure Active Directory （AD）網域服務的常見問題（Faq）

此頁面會回答有關 Azure Active Directory Domain Services 的常見問題。

## <a name="configuration"></a>組態

* [我可以針對單一 Azure AD 目錄建立多個受控網域嗎？](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [我可以在傳統虛擬網路中啟用 Azure AD Domain Services 嗎？](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [是否可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD 網域服務？](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [我可以將現有的受控網域從傳統虛擬網路遷移到 Resource Manager 虛擬網路嗎？](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [是否可以在 Azure CSP (雲端方案提供者) 訂用帳戶中啟用 Azure AD Domain Services？](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [我可以在同盟 Azure AD 目錄中啟用 Azure AD Domain Services 嗎？我不會將密碼雜湊同步處理到 Azure AD。我可以啟用此目錄的 Azure AD Domain Services 嗎？](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？](#can-i-enable-azure-ad-domain-services-using-powershell)
* [我可以使用 Resource Manager 範本來啟用 Azure AD Domain Services 嗎？](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [可以將網域控制站新增至 Azure AD 網域服務的受控網域嗎？](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [受邀到我目錄的來賓使用者是否可以使用 Azure AD Domain Services？](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [我可以將現有的 Azure AD Domain Services 受控網域移至不同的訂用帳戶、資源群組、區域或虛擬網路嗎？](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Azure AD Domain Services 是否包含高可用性選項？](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>我可以針對單一 Azure AD 目錄建立多個受控網域嗎？
不可以。 您只能針對單一 Azure AD 目錄，建立由 Azure AD Domain Services 所服務的單一受控網域服務。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>我可以在傳統虛擬網路中啟用 Azure AD Domain Services 嗎？
新的部署不支援傳統虛擬網路。 部署在傳統虛擬網路中的現有受控網域在2023年3月1日淘汰之前，會繼續受到支援。 針對現有的部署，您可以將[Azure AD Domain Services 從傳統虛擬網路模型遷移至 Resource Manager](migrate-from-classic-vnet.md)。

如需詳細資訊，請參閱[官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD 網域服務？
是。 可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD Domain Services。 當您建立受控網域時，將無法再使用傳統 Azure 虛擬網路。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>我可以將現有的受控網域從傳統虛擬網路遷移到 Resource Manager 虛擬網路嗎？
是。 如需詳細資訊，請參閱[將 Azure AD Domain Services 從傳統虛擬網路模型遷移至 Resource Manager](migrate-from-classic-vnet.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>是否可以在 Azure CSP (雲端方案提供者) 訂用帳戶中啟用 Azure AD Domain Services？
是。 如需詳細資訊，請參閱[如何在 AZURE CSP 訂用帳戶中啟用 Azure AD Domain Services](csp.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>我可以在同盟 Azure AD 目錄中啟用 Azure AD Domain Services 嗎？ 我不要將密碼雜湊同步至 Azure AD。 我可以針對此目錄啟用 Azure AD Domain Services 嗎？
不可以。 若要透過 NTLM 或 Kerberos 驗證使用者，Azure AD Domain Services 需要存取使用者帳戶的密碼雜湊。 在同盟目錄中，密碼雜湊不會儲存在 Azure AD 目錄中。 因此，Azure AD Domain Services 無法與這類 Azure AD 目錄搭配使用。

不過，如果您使用 Azure AD Connect 進行密碼雜湊同步處理，您可以使用 Azure AD Domain Services，因為密碼雜湊值會儲存在 Azure AD 中。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？
服務本身不直接支援此案例。 受控網域一次只能在一個虛擬網路上使用。 不過，您可以設定多個虛擬網路之間的連線，以向其他虛擬網路公開 Azure AD Domain Services。 如需詳細資訊，請參閱如何使用 VPN 閘道或[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)[來連接 Azure 中的虛擬網路](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？
是。 如需詳細資訊，請參閱[如何使用 PowerShell 啟用 Azure AD Domain Services](powershell-create-instance.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>是否可以使用 Resource Manager 範本啟用 Azure AD Domain Services？
是，您可以使用 Resource Manager 範本來建立 Azure AD Domain Services 受控網域。 在部署範本之前，必須先使用 Azure 入口網站或 Azure PowerShell 來建立服務主體和 Azure AD 群組以進行管理。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本建立 AZURE AD DS 受控網域](template-create-instance.md)。 當您在 Azure 入口網站中建立 Azure AD Domain Services 受控網域時，還有另一個選項可以匯出範本以搭配其他部署使用。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>可以將網域控制站新增至 Azure AD 網域服務的受控網域嗎？
不可以。 Azure Active Directory Domain Services 所提供的網域是受控網域。 您不需要布建、設定或以其他方式管理此網域的網域控制站。 這些管理活動是由 Microsoft 提供的服務。 因此，您無法為受控網域新增其他網域控制站（讀寫或唯讀）。

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>受邀到我目錄的來賓使用者是否可以使用 Azure AD Domain Services？
不可以。 以 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 邀請流程受邀到您 Azure AD 目錄的來賓使用者會同步至您的 Azure AD Domain Services 受控網域。 不過，這些使用者的密碼不會儲存在您的 Azure AD 目錄中。 因此，Azure AD Domain Services 無法將這些使用者的 NTLM 和 Kerberos 雜湊同步處理至您的受控網域。 這類使用者無法登入或將電腦加入受控網域。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>我可以將現有的 Azure AD Domain Services 受控網域移至不同的訂用帳戶、資源群組、區域或虛擬網路嗎？
不可以。 建立 Azure AD Domain Services 受控網域之後，您就無法將受控網域移至不同的資源群組、虛擬網路、訂用帳戶等。當您部署受控網域時，請小心選取最適當的訂用帳戶、資源群組、區域和虛擬網路。

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Azure AD Domain Services 是否包含高可用性選項？

是。 每個 Azure AD Domain Services 受控網域都包含兩個網域控制站。 您不會管理或連接到這些網域控制站，而是受管理服務的一部分。 如果您將 Azure AD Domain Services 部署到支援可用性區域的區域，則網域控制站會分散到各個區域。 在不支援可用性區域的區域中，網域控制站會分散到可用性設定組。 您沒有任何設定選項或管理控制此散發。 如需詳細資訊，請參閱[Azure 中虛擬機器的可用性選項](../virtual-machines/windows/availability.md)。

## <a name="administration-and-operations"></a>系統管理與作業

* [我可以使用遠端桌面連線到我的受控網域的網域控制站嗎？](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [我已啟用 Azure AD Domain Services。我使用哪個使用者帳戶將電腦加入此網域？](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [我有 Azure AD Domain Services 所提供的受控網域的網域系統管理員權限嗎？](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [我可以在受控網域上使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [我對 Azure AD 目錄所做的變更要多久才會反映在我的受控網域中？](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [可以擴充 Azure AD Domain Services 所提供之受控網域的結構描述嗎？](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [是否可以在受控網域中修改或新增 DNS 記錄？](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [受控網域上的密碼存留期原則為何？](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure Active Directory Domain Services 是否提供 AD 帳戶鎖定保護？](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [我可以在 Azure AD Domain Services 中設定分散式檔案系統（DFS）和複寫嗎？](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>我可以使用遠端桌面連線到我的受控網域的網域控制站嗎？
不可以。 您沒有許可權可以使用遠端桌面連線到受控網域的網域控制站。 *AAD DC 系統管理員*群組的成員可以使用 ad 系統管理工具（例如 Active Directory 系統管理中心（ADAC）或 AD PowerShell）來管理受控網域。 這些工具會使用已加入受控網域之 Windows Server 上的*遠端伺服器管理工具*功能來安裝。 如需詳細資訊，請參閱[建立管理 VM 以設定和管理 Azure AD Domain Services 受控網域](tutorial-create-management-vm.md)。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已啟用 Azure AD Domain Services。 我應該使用哪一個使用者帳戶來將電腦加入此網域？
屬於受控網域的任何使用者帳戶都可以加入 VM。 *AAD DC 系統管理員*群組的成員會被授與遠端桌面存取已加入受控網域的電腦。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>我有 Azure AD Domain Services 所提供的受控網域的網域系統管理員權限嗎？
不可以。 您不會被授與受控網域的系統管理許可權。 *網域系統管理員*和*企業系統管理員*許可權無法供您在網域內使用。 在內部部署 Active Directory 中，網域系統管理員或企業系統管理員群組的成員，也不會被授與受控網域的網域/企業系統管理員許可權。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>我可以在受控網域上使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？
無法修改從 Azure Active Directory 同步到 Azure AD Domain Services 的使用者和群組，因為其原始來源是 Azure Active Directory 的。 可能會修改源自于受控網域的任何使用者或群組。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>我對 Azure AD 目錄所做的變更要多久才會反映在我的受控網域中？
您在 Azure AD 目錄中使用 Azure AD UI 或 PowerShell 所做的變更，會自動同步處理至您的受控網域。 這個同步處理程序會在背景執行。 這個同步處理沒有定義的時段，無法完成所有物件變更。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>可以擴充 Azure AD Domain Services 所提供之受控網域的結構描述嗎？
不可以。 結構描述是由 Microsoft 針對受控網域進行管理。 Azure AD Domain Services 不支援架構延伸。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在受控網域中修改或新增 DNS 記錄？
是。 *AAD DC Administrators*群組的成員會被授與*dns 系統管理員*許可權，以修改受控網域中的 dns 記錄。 這些使用者可以在已加入受控網域之 Windows Server 的電腦上，使用 DNS 管理員主控台來管理 DNS。 若要使用 DNS 管理員主控台，請安裝*Dns 伺服器工具*，這是伺服器上*遠端伺服器管理工具*選用功能的一部分。 如需詳細資訊，請參閱[管理 Azure AD Domain Services 受控網域中的 DNS](manage-dns.md)。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>受控網域上的密碼存留期原則為何？
Azure AD Domain Services 受控網域上的預設密碼存留期為 90 天。 此密碼存留期不會與 Azure AD 中設定的密碼存留期同步。 因此，您可能會遇到使用者的密碼在您的受控網域中到期，但在 Azure AD 中卻仍然有效的情況。 在這種情況下，使用者必須變更他們在 Azure AD 中的密碼，而新密碼將會同步至您的受控網域。 此外，使用者帳戶的*密碼-不過期*和*使用者必須變更密碼的下次登*入屬性不會同步處理至您的受控網域。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure Active Directory Domain Services 是否提供 AD 帳戶鎖定保護？
是。 2 分鐘內在受控網域中輸入不正確的密碼五次，即會導致使用者帳戶鎖定 30 分鐘。 30 分鐘後，使用者帳戶會自動解除鎖定。 受控網域上的密碼嘗試無效不會鎖定 Azure AD 中的使用者帳戶。 Azure AD Domain Services 受控網域內的使用者帳戶才會遭到鎖定。 如需詳細資訊，請參閱[受控網域上的密碼和帳戶鎖定原則](password-policy.md)。

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>我可以在 Azure AD Domain Services 中設定分散式檔案系統和複寫嗎？
不可以。 使用 Azure AD Domain Services 時，無法使用分散式檔案系統（DFS）和複寫。

## <a name="billing-and-availability"></a>計費與可用性

* [Azure AD 網域服務是付費服務嗎？](#is-azure-ad-domain-services-a-paid-service)
* [是否可以免費試用服務？](#is-there-a-free-trial-for-the-service)
* [我能否暫停 Azure AD Domain Services 受控網域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [我可以針對 DR 事件將 Azure AD Domain Services 容錯移轉到另一個區域嗎？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [我可以在企業行動套件（EMS）中取得 Azure AD Domain Services 嗎？我需要 Azure AD Premium 才能使用 Azure AD Domain Services 嗎？](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [哪些 Azure 區域提供此服務？](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 網域服務是付費服務嗎？
是。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

### <a name="is-there-a-free-trial-for-the-service"></a>是否可以免費試用服務？
Azure 免費試用版包含 Azure AD Domain Services。 您可以註冊以 [免費試用 Azure 一個月](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>我能否暫停 Azure AD Domain Services 受控網域？
不可以。 一旦您啟用了 Azure AD Domain Services 受控網域之後，在您選取的虛擬網路中，必須先刪除受控網域，才能使用此服務。 沒有任何方法可以暫停服務。 除非您刪除受控網域，否則會以每小時計費。

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>我可以針對 DR 事件將 Azure AD Domain Services 容錯移轉到另一個區域嗎？
不可以。 Azure AD Domain Services 目前不提供地理位置多餘的部署模型。 僅限於 Azure 區域中的單一虛擬網路。 如果您想要利用多個 Azure 區域，您需要在 Azure IaaS VM 上執行 Active Directory 網域控制站。 如需架構指引，請參閱將[您的內部部署 Active Directory 網域擴充至 Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>我可以從 Enterprise Mobility Suite (EMS) 中取得 Azure AD 網域服務嗎？ 我是否需要 Azure AD Premium 才能使用 Azure AD 網域服務？
不可以。 Azure AD Domain Services 是隨用隨付 Azure 服務，不是 EMS 的一部分。 Azure AD Domain Services 可以與所有版本的 Azure AD （免費和 Premium）搭配使用。 視使用量而定，您將以小時為單位計費。

### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 區域提供此服務？
請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/)頁面，以查看可使用 Azure AD 網域服務的 Azure 區域清單。

## <a name="troubleshooting"></a>疑難排解

關於 Azure AD Domain Services 在設定或管理上常見問題的解決方案，請參閱[疑難排解指南](troubleshoot.md)。

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解 Azure AD Domain Services，請參閱[什麼是 Azure Active Directory Domain Services？](overview.md)。

若要開始使用，請參閱[建立和設定 Azure Active Directory Domain Services 受控網域](tutorial-create-instance.md)。
