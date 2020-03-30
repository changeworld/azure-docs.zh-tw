---
title: Azure VMware 解決方案（按雲簡單 ） - 在私有雲上設置 vCenter 標識源
description: 描述如何設置私有雲 vCenter 以使用活動目錄進行身份驗證，以便 VMware 管理員訪問 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564018"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>設置 vCenter 標識源以使用活動目錄

## <a name="about-vmware-vcenter-identity-sources"></a>關於 VMware vCenter 標識源

VMware vCenter 支援不同的標識源，用於對訪問 vCenter 的使用者進行身份驗證。  雲簡單私有雲 vCenter 可以設置為使用活動目錄進行身份驗證，以便 VMware 管理員訪問 vCenter。 設置完成後，**雲擁有者**使用者可以將使用者從標識源添加到 vCenter。  

您可以通過以下任何方式設置活動目錄域和網域控制站：

* 在本地運行的活動目錄域和網域控制站
* 在 Azure 訂閱中作為虛擬機器在 Azure 上運行的活動目錄域和網域控制站
* 在私有雲中運行的新活動目錄域和網域控制站
* Azure 活動目錄服務

本指南介紹了在訂閱中設置活動目錄域和在本地運行或作為虛擬機器運行的任務。  如果要使用 Azure AD 作為標識源，請參閱[使用 Azure AD 作為雲簡單私有雲上 vCenter 的標識提供程式](azure-ad.md)，以獲取設置標識源的詳細說明。

[在添加標識源](#add-an-identity-source-on-vcenter)之前，請暫時[升級 vCenter 許可權](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新使用者只能添加到*雲擁有者組*、*雲-全球群集-管理員組*、*雲-全球-存儲-管理員組*、*雲-全球-網路-管理員組*或*雲-全球-VM-管理員組*。  添加到*管理員*組的使用者將自動刪除。  只能將服務帳戶添加到*管理員*組，並且不得使用服務帳戶登錄到 vSphere Web UI。   


## <a name="identity-source-options"></a>標識源選項

* [將本地活動目錄添加為單個登錄標識源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私有雲上設置新的活動目錄](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上設置活動目錄](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>將本地活動目錄添加為單一登入標識源

要將本地活動目錄設置為單一登入標識源，您需要：

* 從本地資料中心到私有雲的[網站到網站 VPN 連接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* 本地 DNS 伺服器 IP 添加到 vCenter 和平臺服務控制器 （PSC）。

設置活動目錄域時，請使用下表中的資訊。

| **選項** | **描述** |
|------------|-----------------|
| **名稱** | 標識源的名稱。 |
| **使用者基本 DN** | 使用者的基本可分辨名稱。 |
| **功能變數名稱** | 例如，域的 FQDN example.com。 請勿在此文字方塊中提供 IP 位址。 |
| **域別名** | 域 NetBIOS 名稱。 如果使用 SSPI 身份驗證，則將 Active Directory 域的 NetBIOS 名稱添加為標識源的別名。 |
| **組的基本 DN** | 組的基本可分辨名稱。 |
| **主伺服器 URL** | 域的網域主控站 LDAP 伺服器。<br><br>使用格式 `ldap://hostname:port` 或 `ldaps://hostname:port`。 對於 LDAP 連接，埠通常為 389，LDAPS 連接為 636。 對於 Active Directory 多網域控制站部署，埠通常為 3268（用於 LDAP）和 3269（用於 LDAPS）。<br><br>當您 `ldaps://` 在主資料庫或輔助 LDAP URL 中使用時，需要為活動目錄伺服器的 LDAPS 終結點建立信任的證書。 |
| **次要伺服器 URL** | 用於容錯移轉的輔助網域控制站 LDAP 伺服器的位址。 |
| **選擇憑證** | 如果要將 LDAPS 與活動目錄 LDAP 伺服器或 OpenLDAP 伺服器標識源一起使用，則在 `ldaps://` URL 文字方塊中鍵入後將顯示"選擇證書"按鈕。 不需要輔助 URL。 |
| **使用者** | 域中使用者 ID，該使用者和組對基本 DN 具有最少的唯讀存取權限。 |
| **密碼** | 由使用者名指定的使用者的密碼。 |

在上表中具有資訊時，可以將本地活動目錄添加為 vCenter 上的單一登入標識源。

> [!TIP]
> 您可以在[VMware 文檔頁面上](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)找到有關單一登入標識源的詳細資訊。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私有雲上設置新的活動目錄

您可以在私有雲上設置新的活動目錄域，並將其用作單一登入的標識源。  活動目錄域可以是現有活動目錄林的一部分，也可以設置為獨立林。

### <a name="new-active-directory-forest-and-domain"></a>新的活動目錄林和域

要設置新的活動目錄林和域，您需要：

* 運行 Microsoft Windows 伺服器的一個或多個虛擬機器用作新活動目錄林和域的網域控制站。
* 運行 DNS 服務的一個或多個虛擬機器以進行名稱解析。

有關詳細步驟[，請參閱安裝新的 Windows 伺服器 2012 活動目錄林](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)。

> [!TIP]
> 對於高可用性服務，我們建議設置多個網域控制站和 DNS 伺服器。

設置活動目錄林和域後，可以在 vCenter 上為新的活動目錄[添加標識源](#add-an-identity-source-on-vcenter)。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>現有活動目錄林中的新活動目錄域

要在現有活動目錄林中設置新的活動目錄域，您需要：

* 網站到網站 VPN 連接到您的活動目錄林位置。
* DNS 伺服器解析現有活動目錄林的名稱。

有關詳細步驟[，請參閱安裝新的 Windows 伺服器 2012 活動目錄子目錄或樹域](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)。

設置活動目錄域後，可以在 vCenter 上為新的活動目錄[添加標識源](#add-an-identity-source-on-vcenter)。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上設置活動目錄

在 Azure 上運行的活動目錄類似于在本地運行的活動目錄。  要將在 Azure 上運行的活動目錄設置為 vCenter 上的單一登入標識源，vCenter 伺服器和 PSC 必須具有與運行活動目錄服務的 Azure 虛擬網路的網路連接。  您可以使用[Azure 虛擬網路連接建立此連接，使用](azure-expressroute-connection.md)來自 Azure 虛擬網路的 ExpressRoute，其中 Active 目錄服務運行到雲簡單私有雲。

建立網路連接後，按照[將本地活動目錄中的步驟添加為單一登入標識源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)，將其添加為標識源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上添加標識源

1. [升級](escalate-private-cloud-privileges.md)私有雲上的許可權。

2. 登錄到私有雲的 vCenter。

3. 選擇**家庭>管理**。

    ![系統管理](media/OnPremAD01.png)

4. 選擇 **"配置>上的單一符號**。

    ![單一登入](media/OnPremAD02.png)

5. 打開 **"標識源**"選項卡，**+** 然後按一下以添加新標識源。

    ![標識源](media/OnPremAD03.png)

6. 選擇**活動目錄作為 LDAP 伺服器**，然後按一下 **"下一步**"。

    ![Active Directory](media/OnPremAD04.png)

7. 指定環境的標識源參數，然後按一下 **"下一步**"。

    ![Active Directory](media/OnPremAD05.png)

8. 查看設置並按一下 **"完成**"。
