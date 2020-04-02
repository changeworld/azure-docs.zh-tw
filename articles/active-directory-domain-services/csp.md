---
title: 面向雲端解決方案供應商的 Azure AD 網域服務 |微軟文件
description: 瞭解如何為 Azure 雲端解決方案供應商啟用與管理 Azure 活動目錄網域服務託管域
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
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519096"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure 雲端解決方案供應商的 Azure 活動目錄功能服務部署及管理

Azure 雲端解決方案供應商 (CSP) 是 Microsoft 合作夥伴的計畫,為各種 Microsoft 雲端服務提供許可證通道。 Azure CSP 使得合作夥伴能夠管理銷售、擁有計費關係、提供技術及計費支援，並成為客戶的單一連絡窗口。 此外，Azure CSP 會提供一組完整的工具，包括自助服務入口網站和隨附的 API。 這些工具使得 CSP 合作夥伴能夠輕鬆地佈建及管理 Azure 資源，以及提供客戶和其訂用帳戶的帳單。

[合作夥伴中心門戶](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)是所有 Azure CSP 合作夥伴的入口點,並提供豐富的客戶管理功能、自動處理等。 Azure CSP 合作夥伴可以使用網頁型 UI 或使用 PowerShell 與各種 API 呼叫來使用合作夥伴中心的功能。

下圖說明 CSP 模型高層的運作方式。 在這裡,Contoso 具有 Azure 活動目錄 (Azure AD) 租戶。 它們與某個 CSP 有合作關係，該 CSP 會在其 Azure CSP 訂用帳戶中部署和管理資源。 Contoso 可能也有定期 (直接) 的 Azure 訂用帳戶，這部分是直接向 Contoso 計費。

![CSP 模型概觀](./media/csp/csp_model_overview.png)

CSP 合作夥伴的租戶有三個特殊代理組 -*管理*代理、*幫助台*代理*和銷售*代理。

*管理*代理組分配給 Contoso 的 Azure AD 租戶中的租戶管理員角色。 因此,屬於 CSP 合作夥伴的管理員代理組的使用者在 Contoso 的 Azure AD 租戶中具有租戶管理員許可權。

當 CSP 合作夥伴為 Contoso 佈建 Azure CSP 訂用帳戶時，其管理專員群組即獲指派該訂用帳戶的擁有者角色。 如此一來，CSP 合作夥伴的管理專員具有代表 Contoso 佈建 Azure 資源 (例如：虛擬機器、虛擬網路以及 Azure AD Domain Services) 的必要權限。

如需詳細資訊，請參閱 [Azure CSP 概觀](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>在 Azure CSP 訂閱中使用 Azure AD DS 的好處

Azure 活動目錄域服務 (Azure AD DS) 提供與 Windows 伺服器活動目錄域服務完全相容的託管域服務,如域聯接、組策略、LDAP、Kerberos/NTLM 身份驗證。 十多年來，已經建置許多應用程式以對使用這些功能的 AD 運作。 許多獨立軟體廠商 (ISV) 已在其客戶的內部部署建置和部署應用程式。 這些應用程式很難支援,因為您通常需要訪問部署應用程式的不同環境。 有了 Azure CSP 訂用帳戶，您便擁有 Azure 調整與彈性較簡單的替代方案。

Azure AD DS 支援 Azure CSP 訂閱。 可以在綁定到客戶的 Azure AD 租戶的 Azure CSP 訂閱中部署應用程式。 因此,您的員工(支援人員)可以使用組織的公司認證管理、管理和服務部署應用程式的 VM。

您還可以在客戶的 Azure AD 租戶中部署 Azure AD 託管域。 然後,您的應用程式將連接到客戶的託管域。 應用程式中依賴於 Kerberos / NTLM、LDAP 或[System.Directory Services API](/dotnet/api/system.directoryservices)的功能可針對客戶的網域無縫工作。 最終客戶受益於將應用程式用作服務,而無需擔心維護應用程式部署的基礎結構。

該訂閱中使用的所有 Azure 資源計費(包括 Azure AD DS)將向您收取費用。 在銷售、計費、技術支援等方面,您可以完全控制與客戶的關係。借助 Azure CSP 平台的靈活性,小型支援代理團隊可以為部署應用程式的實例的許多此類客戶提供服務。

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS 的 CSP 部署模型

有兩種方法可以將 Azure AD DS 與 Azure CSP 訂閱一起使用。 根據客戶的安全性和簡單性考量挑選適合的一個。

### <a name="direct-deployment-model"></a>直接部署模型

在此部署模型中,Azure AD DS 在屬於 Azure CSP 訂閱的虛擬網路中啟用。 CSP 合作夥伴的管理專員擁有下列權限：

* 客戶 Azure AD 租戶中的*全域管理員*許可權。
* Azure CSP 訂閱上的*訂閱擁有者*許可權。

![直接部署模型](./media/csp/csp_direct_deployment_model.png)

在此部署模型中，CSP 提供者的管理專員可以管理客戶的身分識別。 這些管理員代理可以執行預配新使用者或組等任務,或在客戶的 Azure AD 租戶中添加應用程式。

此部署模型可能適用於沒有專用標識管理員或希望 CSP 合作夥伴代表其管理標識的小型組織。

### <a name="peered-deployment-model"></a>對等部署模型

在此部署模型中,Azure AD DS 在屬於客戶的虛擬網路中啟用 - 由客戶支付的直接 Azure 訂閱。 CSP 合作夥伴可以在屬於客戶的 CSP 訂閱的虛擬網路中部署應用程式。 之後可以使用 Azure 虛擬網路對等互連來連接虛擬網路。

使用此部署,Azure CSP 訂閱中 CSP 合作夥伴部署的工作負載或應用程式可以連接到客戶直接 Azure 訂閱中預配的客戶託管域。

![對等部署模型](./media/csp/csp_peered_deployment_model.png)

此部署模型提供權限的區隔，讓 CSP 合作夥伴的技術服務人員專員管理 Azure 訂用帳戶，以及在其內部署和管理資源。 但是,CSP 合作夥伴的幫助台代理不需要在客戶的 Azure AD 目錄中具有全域管理員許可權。 客戶的身分識別系統管理員可以繼續為其組織管理身分識別。

此部署模型可能適用於 ISV 提供其本地應用程式的託管版本的情況,該版本還需要連接到客戶的 Azure AD。

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>在 CSP 訂閱中管理 Azure AD DS

在 Azure CSP 訂用帳戶中管理受控網域時，適用下列重要事項：

* **CSP 管理員代理可以使用其認證預配託管域:** Azure AD DS 支援 Azure CSP 訂閱。 屬於 CSP 合作夥伴的管理員代理組的用戶可以預配新的 Azure AD DS 託管域。

* **CSP 可以使用 PowerShell 為其客戶編寫新的託管域文稿:** 有關詳細資訊[,請參閱如何使用 PowerShell 啟用 Azure AD DS。](powershell-create-instance.md)

* **CSP 管理員代理無法使用其認證在託管網域執行持續管理任務:** CSP 管理員用戶無法使用其憑據在託管域內執行常規管理任務。 這些使用者是客戶的 Azure AD 租戶的外部,其憑據在客戶的 Azure AD 租戶中不可用。 Azure AD DS 無法存取這些使用者的 Kerberos 和 NTLM 密碼哈希,因此無法在 Azure AD DS 託管網域上對使用者進行身份驗證。

  > [!WARNING]
  > 您必須在客戶的目錄內建立使用者帳戶，才能在受控網域上執行進行中的管理工作。
  >
  > 不能使用 CSP 管理員使用者的認證登錄到託管域。 使用屬於客戶的 Azure AD 租戶的使用者帳戶的憑據執行此操作。 對於將 VM 加入託管域、管理 DNS 或管理組策略等任務,需要這些認證。

* **為持續管理創建的使用者帳戶必須添加到*AAD DC 管理員*組:AAD** *DC 管理員*組具有在託管域上執行某些委派管理任務的許可權。 這些任務包括配置 DNS、創建組織單位和管理組策略。
    
    對於 CSP 合作夥伴在託管域上執行這些任務,必須在客戶的 Azure AD 租戶中創建使用者帳戶。 您必須將此帳戶的認證與 CSP 合作夥伴的管理專員分享。 此外,必須將此使用者帳戶添加到*AAD DC 管理員*組,以便使用此使用者帳戶執行託管域上的配置任務。

## <a name="next-steps"></a>後續步驟

要開始,[要註冊 Azure CSP 計劃](/partner-center/enrolling-in-the-csp-program)。 然後,可以使用[Azure 門戶](tutorial-create-instance.md)或[Azure PowerShell](powershell-create-instance.md)啟用 Azure AD 域服務。
