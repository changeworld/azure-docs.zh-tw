---
title: Azure VMware 解決方案（按雲簡單 ） - 使用 Azure AD 作為私有雲上的身份源
description: 描述如何在雲簡單私有雲上添加 Azure AD 作為標識提供程式，以驗證從 Azure 訪問雲簡單的使用者
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564579"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>使用 Azure AD 作為雲簡單私有雲上 vCenter 的標識提供程式

您可以設置雲簡單私有雲 vCenter，以便使用 Azure 活動目錄 （Azure AD） 進行身份驗證，以便 VMware 管理員訪問 vCenter。 設置單一登入標識源後，**雲擁有者**使用者可以將使用者從標識源添加到 vCenter。  

您可以通過以下任何方式設置活動目錄域和網域控制站：

* 在本地運行的活動目錄域和網域控制站
* 在 Azure 訂閱中作為虛擬機器在 Azure 上運行的活動目錄域和網域控制站
* 在雲簡單私有雲中運行的新活動目錄域和網域控制站
* Azure 活動目錄服務

本指南介紹了將 Azure AD 設置為標識源所需的任務。  有關使用在 Azure 中運行的本地活動目錄或活動目錄的資訊，請參閱[設置 vCenter 標識源以在](set-vcenter-identity.md)設置標識源時使用 Active Directory 以獲取詳細說明。

## <a name="about-azure-ad"></a>關於 Azure AD

Azure AD 是 Microsoft 多租戶、基於雲的目錄和標識管理服務。  Azure AD 為使用者提供了可擴展、一致和可靠的身份驗證機制，以便使用者對 Azure 上的不同服務進行身份驗證和訪問。  它還為任何協力廠商服務提供安全的 LDAP 服務，以便使用 Azure AD 作為身份驗證/標識源。  Azure AD 結合了核心目錄服務、高級標識治理和應用程式訪問管理，可用於為管理私有雲的使用者授予對私有雲的存取權限。

要使用 Azure AD 作為具有 vCenter 的標識源，必須設置 Azure AD 和 Azure AD 域服務。 遵循下列指示：

1. [如何設置 Azure AD 和 Azure AD 域服務](#set-up-azure-ad-and-azure-ad-domain-services)
2. [如何在私有雲 vCenter 上設置標識源](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>設置 Azure AD 和 Azure AD 域服務

在開始之前，您需要使用全域管理員許可權訪問 Azure 訂閱。  以下步驟給出了一般準則。 詳細資訊包含在 Azure 文檔中。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 如果已有 Azure AD，則可以跳過此部分。

1. 在訂閱上設置 Azure AD，如[Azure AD 文檔中](../active-directory/fundamentals/get-started-azure-ad.md)所述。
2. 在訂閱上啟用 Azure 活動目錄高級版，如[註冊 Azure 活動目錄高級版](../active-directory/fundamentals/active-directory-get-started-premium.md)中所述。
3. 設置自訂功能變數名稱並驗證自訂功能變數名稱，如[將自訂功能變數名稱添加到 Azure 活動目錄](../active-directory/fundamentals/add-custom-domain.md)中所述。
    1. 使用 Azure 上提供的資訊在域註冊商上設置 DNS 記錄。
    2. 將自訂功能變數名稱設置為主域。

您可以選擇配置其他 Azure AD 功能。  使用 Azure AD 啟用 vCenter 身份驗證不需要這些身份驗證。

### <a name="azure-ad-domain-services"></a>Azure AD 域服務

> [!NOTE]
> 這是啟用 Azure AD 作為 vCenter 標識源的重要步驟。  為避免任何問題，請確保正確執行所有步驟。

1. 使用 Azure 門戶 啟用[Azure 活動目錄域服務](../active-directory-domain-services/active-directory-ds-getting-started.md)中所述的 Azure AD 域服務。
2. 設置 Azure AD 域服務將使用的網路，如使用 Azure[門戶啟用 Azure 活動目錄域服務](../active-directory-domain-services/active-directory-ds-getting-started-network.md)中所述。
3. 建構管理員組以管理 Azure AD 域服務，如[啟用 Azure 活動目錄域服務時使用 Azure 門戶](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)。
4. 更新 Azure AD 域服務的 DNS 設置，如[啟用 Azure 活動目錄域服務](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)中所述。  如果要通過 Internet 連接到 AD，請將 Azure AD 域服務的公共 IP 位址的 DNS 記錄設置為功能變數名稱。
5. 為使用者啟用密碼雜湊同步。  此步驟支援將 NT LAN 管理器 （NTLM） 和 Kerberos 身份驗證所需的密碼雜湊同步到 Azure AD 域服務。 設定密碼雜湊同步後，使用者即可使用他們的公司認證來登入受控網域。 請參閱[將密碼雜湊同步連接到 Azure 活動目錄域服務](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。
    1. 如果存在僅雲使用者，則必須使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 訪問面板</a>更改其密碼，以確保密碼雜湊以 NTLM 或 Kerberos 所需的格式存儲。  按照[啟用密碼雜湊同步到僅雲使用者帳戶的託管域中的](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)說明。  此步驟必須針對使用 Azure 門戶或 Azure AD PowerShell Cmdlet 在 Azure AD 目錄中創建的任何新使用者執行。 需要訪問 Azure AD 域服務的使用者必須使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 訪問面板</a>並訪問其設定檔以更改密碼。

        > [!NOTE]
        > 如果您的組織有僅限雲端使用者帳戶，則需要使用 Azure Active Directory Domain Services 的所有使用者必須變更其密碼。 僅限雲端使用者帳戶是您使用 Azure 入口網站或 Azure AD PowerShell Cmdlet 在 Azure AD 目錄中建立的帳戶。 這類使用者帳戶不是從內部部署目錄進行同步。

    2. 如果要從本地活動目錄中同步密碼，請按照[活動目錄文檔中](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)的步驟操作。

6.  按照[為 Azure AD 域服務託管域配置安全 LDAP （LDAPS）](../active-directory-domain-services/tutorial-configure-ldaps.md)中所述，在 Azure 活動目錄域服務上配置安全 LDAP。
    1. 上傳證書供安全 LDAP 使用，如 Azure 主題所述，[獲取安全 LDAP 的證書](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)。  CloudSimple 建議使用憑證授權單位頒發的簽章憑證，以確保 vCenter 可以信任證書。
    2. 啟用安全 LDAP，如所述[為 Azure AD 域服務託管域啟用安全 LDAP （LDAPS）。](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. 以 .cer 格式保存證書的公共部分（沒有私密金鑰），以便在配置標識源時與 vCenter 一起使用。
    4. 如果需要 Internet 訪問 Azure AD 域服務，請啟用"允許通過互聯網安全訪問 LDAP"選項。
    5. 為 TCP 埠 636 添加 Azure AD 域服務 NSG 的入站安全規則。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>在私有雲 vCenter 上設置標識源

1. [升級](escalate-private-cloud-privileges.md)私有雲 vCenter 的許可權。
2. 收集設置標識源所需的配置參數。

    | **選項** | **描述** |
    |------------|-----------------|
    | **名稱** | 標識源的名稱。 |
    | **使用者基本 DN** | 使用者的基本可分辨名稱。  對於 Azure AD，`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`請使用：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`示例： 。|
    | **功能變數名稱** | 例如，域的 FQDN example.com。 請勿在此文字方塊中提供 IP 位址。 |
    | **域別名** | *（可選）* 域 NetBIOS 名稱。 如果使用 SSPI 身份驗證，則將 Active Directory 域的 NetBIOS 名稱添加為標識源的別名。 |
    | **組的基本 DN** | 組的基本可分辨名稱。 對於 Azure AD，`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`請使用：示例：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **主伺服器 URL** | 域的網域主控站 LDAP 伺服器。<br><br>使用格式 `ldaps://hostname:port`。 對於 LDAPS 連接，埠通常為 636。 <br><br>當您 `ldaps://` 在主資料庫或輔助 LDAP URL 中使用時，需要為活動目錄伺服器的 LDAPS 終結點建立信任的證書。 |
    | **次要伺服器 URL** | 用於容錯移轉的輔助網域控制站 LDAP 伺服器的位址。 |
    | **選擇憑證** | 如果要將 LDAPS 與活動目錄 LDAP 伺服器或 OpenLDAP 伺服器標識源一起使用，則在 `ldaps://` URL 文字方塊中鍵入後將顯示"選擇證書"按鈕。 不需要輔助 URL。 |
    | **使用者** | 域中使用者 ID，該使用者和組對基本 DN 具有最少的唯讀存取權限。 |
    | **密碼** | 由使用者名指定的使用者的密碼。 |

3. 許可權升級後登錄到私有雲 vCenter。
4. 使用上一步中的值在[vCenter 上添加標識源](set-vcenter-identity.md#add-an-identity-source-on-vcenter)中的說明將 Azure 活動目錄設置為標識源。
5. 將使用者/組從 Azure AD 添加到 vCenter 組，如 VMware 主題中所述，[將成員添加到 vCenter 單一登入組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

> [!CAUTION]
> 新使用者只能添加到*雲擁有者組*、*雲-全球群集-管理員組*、*雲-全球-存儲-管理員組*、*雲-全球-網路-管理員組*或*雲-全球-VM-管理員組*。  添加到*管理員*組的使用者將自動刪除。  只能將服務帳戶添加到*管理員*組。

## <a name="next-steps"></a>後續步驟

* [瞭解私有雲許可權模型](learn-private-cloud-permissions.md)
