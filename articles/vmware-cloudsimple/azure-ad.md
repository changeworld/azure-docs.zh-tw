---
title: Azure VMware Solution by CloudSimple-使用 Azure AD 作為私用雲端上的身分識別來源
description: 說明如何將 Azure AD 新增為 CloudSimple 私人雲端上的身分識別提供者，以驗證從 Azure 存取 CloudSimple 的使用者
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 93922986dfe0b2b4e8ba0923931df601cc12428b
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532523"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>使用 Azure AD 作為 CloudSimple 私人雲端上的 vCenter 身分識別提供者

您可以設定 CloudSimple 私人雲端 vCenter，以使用 Azure Active Directory (Azure AD) 進行驗證，讓 VMware 系統管理員存取 vCenter。 設定單一登入身分識別來源之後， **cloudowner** 使用者可以將使用者從身分識別來源新增至 vCenter。  

您可以利用下列任何一種方式來設定 Active Directory 網域和網域控制站：

* Active Directory 在內部部署執行的網域和網域控制站
* Active Directory 在 Azure 上執行的網域和網域控制站作為 Azure 訂用帳戶中的虛擬機器
* CloudSimple 私人雲端中執行的新 Active Directory 網域和網域控制站
* Azure Active Directory 服務

本指南說明將 Azure AD 設定為身分識別來源所需的工作。  如需使用內部部署 Active Directory 或在 Azure 中執行 Active Directory 的相關資訊，請參閱 [設定 vCenter 身分識別來源以使用 Active Directory](set-vcenter-identity.md) ，以取得設定身分識別來源的詳細指示。

## <a name="about-azure-ad"></a>關於 Azure AD

Azure AD 是 Microsoft 多租使用者雲端式目錄和身分識別管理服務。  Azure AD 提供可調整、一致且可靠的驗證機制，讓使用者在 Azure 上驗證及存取不同的服務。  它也會為任何協力廠商服務提供安全的 LDAP 服務，以便使用 Azure AD 作為驗證/身分識別來源。  Azure AD 結合了核心目錄服務、先進的身分識別管理和應用程式存取管理，可用於為管理私用雲端的使用者提供私人雲端的存取權。

若要使用 Azure AD 作為 vCenter 的身分識別來源，您必須設定 Azure AD 和 Azure AD 網域服務。 遵循下列指示：

1. [如何設定 Azure AD 和 Azure AD 網域服務](#set-up-azure-ad-and-azure-ad-domain-services)
2. [如何在您的私人雲端 vCenter 上設定身分識別來源](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>設定 Azure AD 和 Azure AD 網域服務

開始之前，您需要具有全域管理員許可權的 Azure 訂用帳戶存取權。  下列步驟提供一般指導方針。 Azure 檔中包含詳細資料。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 如果您已經有 Azure AD，可以略過本節。

1. 在您的訂用帳戶上設定 Azure AD，如  [Azure AD 檔](../active-directory/fundamentals/active-directory-whatis.md)中所述。
2. 在訂用帳戶上啟用 Azure Active Directory Premium，如 [註冊 Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md)中所述。
3. 設定自訂功能變數名稱，並依照 [將自訂功能變數名稱新增至 Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md)所述，驗證自訂功能變數名稱。
    1. 使用 Azure 上提供的資訊，在您的網域註冊機構上設定 DNS 記錄。
    2. 將自訂功能變數名稱設定為主要網域。

您可以選擇性地設定其他 Azure AD 功能。  啟用 Azure AD 的 vCenter 驗證並不需要這些。

### <a name="azure-ad-domain-services"></a>Azure AD 網域服務

> [!NOTE]
> 這是啟用 Azure AD 作為 vCenter 身分識別來源的重要步驟。  若要避免任何問題，請確定已正確執行所有步驟。

1. 啟用 Azure AD 的網域服務，如 [使用 Azure 入口網站啟用 Azure Active Directory 網域服務](../active-directory-domain-services/tutorial-create-instance.md)中所述。
2. 設定 Azure AD 網域服務將使用的網路，如 [使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)中所述。
3. 設定系統管理員群組以管理 Azure AD Domain Services，如 [使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)中所述。
4. 如 [啟用 Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)所述，更新 AZURE AD DOMAIN SERVICES 的 DNS 設定。  如果您想要透過網際網路連線到 AD，請將 Azure AD 網域服務之公用 IP 位址的 DNS 記錄設定為功能變數名稱。
5. 為使用者啟用密碼雜湊同步處理。  此步驟可將 NT LAN Manager 所需的密碼雜湊同步處理 (NTLM) ，以及 Azure AD Domain Services 的 Kerberos 驗證。 設定密碼雜湊同步後，使用者即可使用他們的公司認證來登入受控網域。 請參閱 [啟用密碼雜湊同步處理以 Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)。
    1. 如果有僅限雲端的使用者，他們必須使用 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 存取面板</a> 來變更其密碼，以確保密碼雜湊以 NTLM 或 Kerberos 所要求的格式儲存。  遵循 [針對僅限雲端使用者帳戶啟用您受控網域的密碼雜湊同步處理的](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)指示。  此步驟必須針對個別使用者，以及使用 Azure 入口網站或 Azure AD PowerShell Cmdlet 在 Azure AD 目錄中建立的任何新使用者進行。 需要 Azure AD 網域服務存取權的使用者必須使用 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 存取面板</a> 並存取其設定檔，才能變更密碼。

        > [!NOTE]
        > 如果您的組織有僅限雲端使用者帳戶，則需要使用 Azure Active Directory Domain Services 的所有使用者必須變更其密碼。 僅限雲端使用者帳戶是您使用 Azure 入口網站或 Azure AD PowerShell Cmdlet 在 Azure AD 目錄中建立的帳戶。 這類使用者帳戶不是從內部部署目錄進行同步。

    2. 如果您要從內部部署 Active directory 同步處理密碼，請遵循 [Active Directory 檔](../active-directory-domain-services/tutorial-configure-password-hash-sync.md)中的步驟。

6.  在您的 Azure Active Directory Domain Services 上設定安全 LDAP，如 [針對 Azure AD Domain Services 受控網域設定安全 ldap (LDAPS) ](../active-directory-domain-services/tutorial-configure-ldaps.md)中所述。
    1. 上傳安全 LDAP 使用的憑證，如 Azure 主題 [取得安全 ldap 的憑證](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)所述。  CloudSimple 建議使用由憑證授權單位單位所簽發的已簽署憑證，以確保 vCenter 可以信任該憑證。
    2. 如所述啟用安全的 LDAP，請 [啟用 Azure AD Domain Services 受控網域的安全 ldap (LDAPS) ](../active-directory-domain-services/tutorial-configure-ldaps.md)。
    3. 在設定身分識別來源時，將憑證的公開部分 (不含私密金鑰的) 使用 .cer 格式來搭配 vCenter 使用。
    4. 如果需要 Azure AD 網域服務的網際網路存取，請啟用 [允許安全存取 LDAP over 網際網路] 選項。
    5. 為 TCP 通訊埠636新增 Azure AD 網域服務 NSG 的輸入安全性規則。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>在您的私人雲端 vCenter 上設定身分識別來源

1. [提升](escalate-private-cloud-privileges.md) 私用雲端 vCenter 的許可權。
2. 收集設定身分識別來源所需的設定參數。

    | **選項** | **說明** |
    |------------|-----------------|
    | **Name** | 身分識別來源的名稱。 |
    | **使用者的基底 DN** | 使用者的基底分辨名稱。  如 Azure AD，請使用： `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  範例： `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` 。|
    | **網域名稱** | 網域的 FQDN，例如，example.com。 請勿在此文字方塊中提供 IP 位址。 |
    | **網域別名** | * (選擇性) * 網域 NetBIOS 名稱。 如果您使用 SSPI 驗證，請將 Active Directory 網域的 NetBIOS 名稱新增為身分識別來源的別名。 |
    | **群組的基底 DN** | 群組的基底分辨名稱。 針對 Azure AD，請使用： `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  範例： `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **主伺服器 URL** | 網域的網域主控站 LDAP 伺服器。<br><br>使用此格式： `ldaps://hostname:port`。 針對 LDAPS 連線，埠通常是636。 <br><br>當您  `ldaps://`   在主要或次要 LDAP URL 中使用時，需要為 Active Directory 伺服器的 LDAPS 端點建立信任的憑證。 |
    | **次要伺服器 URL** | 用於容錯移轉的次要網域控制站 LDAP 伺服器位址。 |
    | **選擇憑證** | 如果您想要搭配 Active Directory LDAP 伺服器或 OpenLDAP 伺服器身分識別來源使用 LDAPS，在  `ldaps://` [URL] 文字方塊中輸入之後，會出現 [選擇憑證] 按鈕   。 不需要次要 URL。 |
    | **使用者名稱** | 網域中使用者的識別碼，其具有使用者和群組的基本 DN 的最小唯讀存取權。 |
    | **密碼** | 使用者名稱所指定之使用者的密碼。 |

3. 在許可權提升之後，登入您的私用雲端 vCenter。
4. 請依照上一個步驟中的值，使用上一個步驟中的值將 Azure Active Directory 設定為身分識別來源，依照在 [vCenter 上新增身分識別來源](set-vcenter-identity.md#add-an-identity-source-on-vcenter) 中的指示進行。
5. 將使用者/群組從 Azure AD 新增至 vCenter 群組（如 VMware 主題 [將成員新增至 Vcenter 單一登入群組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)中所述）。

> [!CAUTION]
> 新使用者只能新增至 *雲端擁有者群組*、 *雲端全域*叢集-系統管理群組、雲端-全域 *存放裝置-* 管理群組、 *雲端全域-網路-系統管理* 群組或 *雲端全域 VM-管理群組*。  新增至系統 *管理員* 群組的使用者將會自動移除。  只有服務帳戶必須加入至系統 *管理員* 群組。

## <a name="next-steps"></a>後續步驟

* [瞭解私人雲端許可權模型](learn-private-cloud-permissions.md)
