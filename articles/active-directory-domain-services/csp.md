---
title: 雲端解決方案提供者的 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何啟用及管理 Azure 雲端解決方案提供者的 Azure Active Directory Domain Services 受控網域
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519096"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>適用于 Azure 雲端解決方案提供者的 Azure Active Directory Domain Services 部署和管理

Azure 雲端解決方案提供者（CSP）是適用于 Microsoft 合作夥伴的方案，並提供各種 Microsoft 雲端服務的授權通道。 Azure CSP 使得合作夥伴能夠管理銷售、擁有計費關係、提供技術及計費支援，並成為客戶的單一連絡窗口。 此外，Azure CSP 會提供一組完整的工具，包括自助服務入口網站和隨附的 API。 這些工具使得 CSP 合作夥伴能夠輕鬆地佈建及管理 Azure 資源，以及提供客戶和其訂用帳戶的帳單。

[合作夥伴中心入口網站](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)是所有 Azure CSP 合作夥伴的進入點，並提供豐富的客戶管理功能、自動化處理等等。 Azure CSP 合作夥伴可以使用網頁型 UI 或使用 PowerShell 與各種 API 呼叫來使用合作夥伴中心的功能。

下圖說明 CSP 模型高層的運作方式。 在這裡，Contoso 有一個 Azure Active Directory （Azure AD）租使用者。 它們與某個 CSP 有合作關係，該 CSP 會在其 Azure CSP 訂用帳戶中部署和管理資源。 Contoso 可能也有定期 (直接) 的 Azure 訂用帳戶，這部分是直接向 Contoso 計費。

![CSP 模型概觀](./media/csp/csp_model_overview.png)

CSP 合作夥伴的租使用者有三個特殊的代理程式群組-*管理員*代理程式、*技術服務人員*代理程式和*銷售*代理人。

系統*管理員*代理程式群組會指派給 Contoso 的 Azure AD 租使用者中的租使用者系統管理員角色。 如此一來，屬於 CSP 合作夥伴的管理代理程式群組的使用者，會在 Contoso 的 Azure AD 租使用者中具有租使用者系統管理員許可權。

當 CSP 合作夥伴為 Contoso 佈建 Azure CSP 訂用帳戶時，其管理專員群組即獲指派該訂用帳戶的擁有者角色。 如此一來，CSP 合作夥伴的管理專員具有代表 Contoso 佈建 Azure 資源 (例如：虛擬機器、虛擬網路以及 Azure AD Domain Services) 的必要權限。

如需詳細資訊，請參閱 [Azure CSP 概觀](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>在 Azure CSP 訂用帳戶中使用 Azure AD DS 的優點

Azure Active Directory Domain Services （Azure AD DS）提供受控網域服務，例如網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory Domain Services 完全相容。 十多年來，已經建置許多應用程式以對使用這些功能的 AD 運作。 許多獨立軟體廠商 (ISV) 已在其客戶的內部部署建置和部署應用程式。 這些應用程式很難支援，因為您通常需要存取部署應用程式的不同環境。 有了 Azure CSP 訂用帳戶，您便擁有 Azure 調整與彈性較簡單的替代方案。

Azure AD DS 支援 Azure CSP 訂用帳戶。 您可以將應用程式部署在與客戶的 Azure AD 租使用者系結的 Azure CSP 訂用帳戶中。 因此，您的員工（支援人員）可以使用貴組織的公司認證來管理、管理和服務您的應用程式部署所在的 Vm。

您也可以在客戶的 Azure AD 租使用者中部署 Azure AD DS 受控網域。 接著，您的應用程式會連線到客戶的受控網域。 應用程式內依賴 Kerberos/NTLM、LDAP 或[MICROSOFT.DIRECTORYSERVICES API](/dotnet/api/system.directoryservices)的功能，可順暢地與客戶的網域搭配運作。 終端客戶受益于取用應用程式即服務，而不需要擔心維護應用程式部署所在的基礎結構。

您在該訂用帳戶中使用的 Azure 資源的所有計費（包括 Azure AD DS）都會向您收取費用。 在銷售、帳單、技術支援等方面，您可以完全掌控與客戶之間的關係。有了 Azure CSP 平臺的彈性，一小組的支援代理程式可以服務許多已部署您的應用程式實例的客戶。

## <a name="csp-deployment-models-for-azure-ad-ds"></a>適用于 Azure AD DS 的 CSP 部署模型

有兩種方式可將 Azure AD DS 與 Azure CSP 訂用帳戶搭配使用。 根據客戶的安全性和簡單性考量挑選適合的一個。

### <a name="direct-deployment-model"></a>直接部署模型

在此部署模型中，會在屬於 Azure CSP 訂用帳戶的虛擬網路內啟用 Azure AD DS。 CSP 合作夥伴的管理專員擁有下列權限：

* 客戶 Azure AD 租使用者中的*全域管理員*許可權。
* Azure CSP 訂用帳戶的訂用帳戶*擁有*者許可權。

![直接部署模型](./media/csp/csp_direct_deployment_model.png)

在此部署模型中，CSP 提供者的管理專員可以管理客戶的身分識別。 這些系統管理員代理程式可以執行工作，例如布建新的使用者或群組，或在客戶的 Azure AD 租使用者內新增應用程式。

此部署模型可能適用于沒有專用身分識別管理員的小型組織，或不想讓 CSP 合作夥伴代表他們管理識別身分。

### <a name="peered-deployment-model"></a>對等部署模型

在此部署模型中，會在屬於客戶的虛擬網路中啟用 Azure AD DS-客戶所支付的直接 Azure 訂用帳戶。 CSP 合作夥伴可以在屬於客戶 CSP 訂用帳戶的虛擬網路內部署應用程式。 之後可以使用 Azure 虛擬網路對等互連來連接虛擬網路。

在此部署中，由 CSP 合作夥伴在 Azure CSP 訂用帳戶中部署的工作負載或應用程式，可連線至客戶的直接 Azure 訂用帳戶中布建的客戶受控網域。

![對等部署模型](./media/csp/csp_peered_deployment_model.png)

此部署模型提供權限的區隔，讓 CSP 合作夥伴的技術服務人員專員管理 Azure 訂用帳戶，以及在其內部署和管理資源。 不過，CSP 合作夥伴的技術服務人員代理程式不需要擁有客戶 Azure AD 目錄的全域管理員許可權。 客戶的身分識別系統管理員可以繼續為其組織管理身分識別。

此部署模型可能適用于 ISV 提供其內部部署應用程式裝載版本的案例，而這也需要連接到客戶的 Azure AD。

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>管理 CSP 訂用帳戶中的 Azure AD DS

在 Azure CSP 訂用帳戶中管理受控網域時，適用下列重要事項：

* **CSP 管理代理程式可以使用其認證來布建受控網域：** Azure AD DS 支援 Azure CSP 訂用帳戶。 屬於 CSP 合作夥伴管理代理程式群組的使用者可以布建新的 Azure AD DS 受控網域。

* **Csp 可以使用 PowerShell 為其客戶建立新受控網域的腳本：** 如需詳細資訊，請參閱[如何使用 PowerShell 啟用 AZURE AD DS](powershell-create-instance.md) 。

* **CSP 管理代理程式無法使用其認證在受控網域上執行進行中的管理工作：** CSP 管理使用者無法使用其認證在受控網域內執行例行管理工作。 這些使用者是客戶的 Azure AD 租使用者外部，而其認證無法在客戶的 Azure AD 租使用者內使用。 Azure AD DS 無法存取這些使用者的 Kerberos 和 NTLM 密碼雜湊，因此無法在 Azure AD DS 受控網域上驗證使用者。

  > [!WARNING]
  > 您必須在客戶的目錄內建立使用者帳戶，才能在受控網域上執行進行中的管理工作。
  >
  > 您無法使用 CSP 管理使用者的認證來登入受控網域。 請使用屬於客戶 Azure AD 租使用者之使用者帳戶的認證來執行此動作。 您需要這些認證來進行工作，例如將 Vm 加入受控網域、管理 DNS 或管理群組原則。

* **針對進行中的管理建立的使用者帳戶必須新增至*AAD dc 系統管理員*群組：** *aad dc administrators*群組具有在受控網域上執行特定委派系統管理工作的許可權。 這些工作包括設定 DNS、建立組織單位和管理群組原則。
    
    若要讓 CSP 合作夥伴在受控網域上執行這些工作，您必須在客戶的 Azure AD 租使用者內建立使用者帳戶。 您必須將此帳戶的認證與 CSP 合作夥伴的管理專員分享。 此外，您必須將此使用者帳戶新增至*AAD DC 系統管理員*群組，才能使用此使用者帳戶來執行受控網域上的設定工作。

## <a name="next-steps"></a>後續步驟

若要開始使用，請[註冊 AZURE CSP 方案](/partner-center/enrolling-in-the-csp-program)。 接著，您可以使用[Azure 入口網站](tutorial-create-instance.md)或[Azure PowerShell](powershell-create-instance.md)來啟用 Azure AD Domain Services。
