---
title: 有關 Azure AD 域服務的常見問題 |微軟文檔
description: 閱讀並理解有關 Azure 活動目錄域服務的配置、管理和可用性的一些常見問題
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: a57826c79babded6e616548879a5ec0c223307d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946426"
---
# <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

此頁回答了有關 Azure 活動目錄域服務的常見問題。

## <a name="configuration"></a>組態

* [我可以針對單一 Azure AD 目錄建立多個受控網域嗎？](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [是否可以在經典虛擬網路中啟用 Azure AD 域服務？](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [是否可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD 網域服務？](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [是否可以將現有託管域從經典虛擬網路遷移到資源管理器虛擬網路？](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [是否可以在 Azure CSP (雲端方案提供者) 訂用帳戶中啟用 Azure AD Domain Services？](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [是否可以在聯合 Azure AD 目錄中啟用 Azure AD 域服務？我不將密碼雜湊同步到 Azure AD。是否可以為此目錄啟用 Azure AD 域服務？](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？](#can-i-enable-azure-ad-domain-services-using-powershell)
* [是否可以使用資源管理器範本啟用 Azure AD 域服務？](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [可以將網域控制站新增至 Azure AD 網域服務的受控網域嗎？](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [受邀到我目錄的來賓使用者是否可以使用 Azure AD Domain Services？](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [是否可以將現有的 Azure AD 域服務託管域移動到其他訂閱、資源組、區域或虛擬網路？](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Azure AD 域服務是否包括高可用性選項？](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>我可以針對單一 Azure AD 目錄建立多個受控網域嗎？
否。 您只能針對單一 Azure AD 目錄，建立由 Azure AD Domain Services 所服務的單一受控網域服務。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>是否可以在經典虛擬網路中啟用 Azure AD 域服務？
新部署不支援經典虛擬網路。 部署在經典虛擬網路中的現有託管域將繼續得到支援，直到 2023 年 3 月 1 日停用。 對於現有部署，可以將[Azure AD 域服務從經典虛擬網路模型遷移到資源管理器](migrate-from-classic-vnet.md)。

有關詳細資訊，請參閱[官方棄用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD 網域服務？
是。 可以啟用 Azure Resource Manager 虛擬網路中的 Azure AD Domain Services。 創建託管域時，經典 Azure 虛擬網路不再可用。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>是否可以將現有託管域從經典虛擬網路遷移到資源管理器虛擬網路？
是。 有關詳細資訊，請參閱將[Azure AD 域服務從經典虛擬網路模型遷移到資源管理器](migrate-from-classic-vnet.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>是否可以在 Azure CSP (雲端方案提供者) 訂用帳戶中啟用 Azure AD Domain Services？
是。 有關詳細資訊，請參閱如何在[Azure CSP 訂閱中啟用 Azure AD 域服務](csp.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>我可以在同盟 Azure AD 目錄中啟用 Azure AD Domain Services 嗎？ 我不要將密碼雜湊同步至 Azure AD。 我可以針對此目錄啟用 Azure AD Domain Services 嗎？
否。 要通過 NTLM 或 Kerberos 對使用者進行身份驗證，Azure AD 域服務需要訪問使用者帳戶的密碼雜湊。 在聯合目錄中，密碼雜湊不會存儲在 Azure AD 目錄中。 因此，Azure AD 域服務不與此類 Azure AD 目錄配合使用。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？
服務本身不直接支援此方案。 受控網域一次只能在一個虛擬網路上使用。 但是，您可以配置多個虛擬網路之間的連接，以便將 Azure AD 域服務公開給其他虛擬網路。 有關詳細資訊，請參閱如何使用 VPN 閘道或[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)[在 Azure 中連接虛擬網路](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？
是。 有關詳細資訊，請參閱如何使用[PowerShell 啟用 Azure AD 域服務](powershell-create-instance.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>是否可以使用 Resource Manager 範本啟用 Azure AD Domain Services？
可以，您可以使用資源管理器範本創建 Azure AD 域服務託管域。 在部署範本之前，必須使用 Azure 門戶或 Azure PowerShell 創建服務主體和 Azure AD 組進行管理。 有關詳細資訊，請參閱使用[Azure 資源管理器範本創建 Azure AD DS 託管域](template-create-instance.md)。 在 Azure 門戶中創建 Azure AD 域服務託管域時，還可以匯出範本以用於其他部署。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>可以將網域控制站新增至 Azure AD 網域服務的受控網域嗎？
否。 Azure Active Directory Domain Services 所提供的網域是受控網域。 您無需為此域預配、配置或以其他方式管理網域控制站。 這些管理活動由 Microsoft 作為服務提供。 因此，不能為託管域添加其他網域控制站（讀寫或唯讀）。

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>受邀到我目錄的來賓使用者是否可以使用 Azure AD Domain Services？
否。 以 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 邀請流程受邀到您 Azure AD 目錄的來賓使用者會同步至您的 Azure AD Domain Services 受控網域。 但是，這些使用者的密碼不會存儲在 Azure AD 目錄中。 因此，Azure AD 域服務無法將這些使用者的 NTLM 和 Kerberos 雜湊同步到託管域中。 此類使用者無法登錄電腦或將電腦加入託管域。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>是否可以將現有的 Azure AD 域服務託管域移動到其他訂閱、資源組、區域或虛擬網路？
否。 創建 Azure AD 域服務託管域後，無法將實例移動到其他資源組、虛擬網路、訂閱等。部署 Azure AD DS 實例時，請注意選擇最合適的訂閱、資源組、區域和虛擬網路。

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Azure AD 域服務是否包括高可用性選項？

是。 每個 Azure AD 域服務託管域包括兩個網域控制站。 您不管理或連接到這些網域控制站，它們是託管服務的一部分。 如果將 Azure AD 域服務部署到支援可用性區域的區域，則網域控制站將分佈在多個區域。 在不支援可用性區域的區域中，網域控制站分佈在可用性集中。 您對此分發沒有配置選項或管理控制。 有關詳細資訊，請參閱[Azure 中的虛擬機器的可用性選項](../virtual-machines/windows/availability.md)。

## <a name="administration-and-operations"></a>行政和業務

* [我可以使用遠端桌面連線到我的受控網域的網域控制站嗎？](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [我已啟用 Azure AD 域服務。我使用哪些使用者帳戶將電腦加入到此域？](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [我有 Azure AD Domain Services 所提供的受控網域的網域系統管理員權限嗎？](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [我可以在受控網域上使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [我對 Azure AD 目錄所做的變更要多久才會反映在我的受控網域中？](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [可以擴充 Azure AD Domain Services 所提供之受控網域的結構描述嗎？](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [是否可以在受控網域中修改或新增 DNS 記錄？](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [受控網域上的密碼存留期原則為何？](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure Active Directory Domain Services 是否提供 AD 帳戶鎖定保護？](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>我可以使用遠端桌面連線到我的受控網域的網域控制站嗎？
否。 您沒有使用遠端桌面連線到託管網域控制站的許可權。 *AAD DC 管理員*組的成員可以使用 AD 管理工具（如活動目錄管理中心 （ADAC） 或 AD PowerShell）管理託管域。 這些工具使用連接到託管域的 Windows 伺服器上的*遠端伺服器管理工具*功能安裝。 有關詳細資訊，請參閱[創建管理 VM 以配置和管理 Azure AD 域服務託管域](tutorial-create-management-vm.md)。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已啟用 Azure AD 域服務。 我應該使用哪一個使用者帳戶來將電腦加入此網域？
屬於 Azure AD DS 託管域的任何使用者帳戶都可以加入 VM。 *AAD DC 管理員*組的成員被授予對已加入託管域的電腦的遠端桌面存取權限。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>我有 Azure AD Domain Services 所提供的受控網域的網域系統管理員權限嗎？
否。 未授予託管域的管理許可權。 *域管理員**和企業管理員*許可權不可用，您無法在域中使用。 本地活動目錄中的域管理員或企業管理員組的成員也不會在託管域上被授予域/企業管理員許可權。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>我可以在受控網域上使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？
無法修改從 Azure 活動目錄同步到 Azure AD 域服務的使用者和組，因為它們的源是 Azure 活動目錄。 可以修改源自託管域的任何使用者或組。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>我對 Azure AD 目錄所做的變更要多久才會反映在我的受控網域中？
使用 Azure AD UI 或 PowerShell 在 Azure AD 目錄中所做的更改將自動同步到託管域。 這個同步處理程序會在背景執行。 沒有為此同步完成所有物件更改定義的時間段。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>可以擴充 Azure AD Domain Services 所提供之受控網域的結構描述嗎？
否。 結構描述是由 Microsoft 針對受控網域進行管理。 Azure AD 域服務不支援架構擴展。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在受控網域中修改或新增 DNS 記錄？
是。 *AAD DC 管理員*組的成員被授予 DNS*管理員*許可權，以修改託管域中的 DNS 記錄。 這些使用者可以在運行 Windows Server 的電腦上使用 DNS 管理器主控台，該主控台已加入託管域來管理 DNS。 要使用 DNS 管理器主控台，請安裝*DNS 伺服器工具*，這是*伺服器上遠端伺服器管理工具*可選功能的一部分。 有關詳細資訊，請參閱在[Azure AD 域服務託管域中管理 DNS。](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>受控網域上的密碼存留期原則為何？
Azure AD Domain Services 受控網域上的預設密碼存留期為 90 天。 此密碼存留期不會與 Azure AD 中設定的密碼存留期同步。 因此，您可能會遇到使用者的密碼在您的受控網域中到期，但在 Azure AD 中卻仍然有效的情況。 在這種情況下，使用者必須變更他們在 Azure AD 中的密碼，而新密碼將會同步至您的受控網域。 此外，使用者帳戶的*密碼不會過期*和*使用者必須更改密碼的下一個登錄*屬性不會同步到託管域。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure Active Directory Domain Services 是否提供 AD 帳戶鎖定保護？
是。 2 分鐘內在受控網域中輸入不正確的密碼五次，即會導致使用者帳戶鎖定 30 分鐘。 30 分鐘後，使用者帳戶會自動解除鎖定。 託管域上的無效密碼嘗試不會鎖定 Azure AD 中的使用者帳戶。 Azure AD Domain Services 受控網域內的使用者帳戶才會遭到鎖定。 有關詳細資訊，請參閱[託管域上的密碼和帳戶鎖定策略](password-policy.md)。

## <a name="billing-and-availability"></a>計費與可用性

* [Azure AD 網域服務是付費服務嗎？](#is-azure-ad-domain-services-a-paid-service)
* [是否可以免費試用服務？](#is-there-a-free-trial-for-the-service)
* [我能否暫停 Azure AD Domain Services 受控網域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [我可以針對 DR 事件將 Azure AD Domain Services 容錯移轉到另一個區域嗎？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [是否可以將 Azure AD 域服務作為企業移動套件 （EMS） 的一部分？我是否需要 Azure AD 高級版才能使用 Azure AD 域服務？](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [哪些 Azure 區域提供此服務？](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 網域服務是付費服務嗎？
是。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

### <a name="is-there-a-free-trial-for-the-service"></a>是否可以免費試用服務？
Azure AD 域服務包含在 Azure 的免費試用版中。 您可以註冊以 [免費試用 Azure 一個月](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>我能否暫停 Azure AD Domain Services 受控網域？
否。 啟用 Azure AD 域服務託管域後，該服務在選定的虛擬網路中可用，直到您刪除託管域。 無法暫停服務。 除非您刪除受控網域，否則會以每小時計費。

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>我可以針對 DR 事件將 Azure AD Domain Services 容錯移轉到另一個區域嗎？
否。 Azure AD 域服務當前不提供異地冗余部署模型。 它僅限於 Azure 區域中的單個虛擬網路。 如果您想要利用多個 Azure 區域，您需要在 Azure IaaS VM 上執行 Active Directory 網域控制站。 有關體系結構指南，請參閱[將本地活動目錄域擴展到 Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>我可以從 Enterprise Mobility Suite (EMS) 中取得 Azure AD 網域服務嗎？ 我是否需要 Azure AD Premium 才能使用 Azure AD 網域服務？
否。 Azure AD 域服務是即用即付的 Azure 服務，不是 EMS 的一部分。 Azure AD 域服務可與所有版本的 Azure AD（免費和高級版）一起使用。 根據使用方式，按小時計費。

### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 區域提供此服務？
請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/)頁面，以查看可使用 Azure AD 網域服務的 Azure 區域清單。

## <a name="troubleshooting"></a>疑難排解

關於 Azure AD Domain Services 在設定或管理上常見問題的解決方案，請參閱[疑難排解指南](troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure AD 域服務的內容，請參閱[什麼是 Azure 活動目錄域服務？](overview.md)

要開始，請參閱[創建和配置 Azure 活動目錄域服務實例](tutorial-create-instance.md)。
