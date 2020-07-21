---
title: Azure VMware Solution by CloudSimple-在私人雲端上設定 vCenter 身分識別來源
description: 說明如何設定您的私用雲端 vCenter，以使用 Active Directory 進行驗證，讓 VMware 系統管理員存取 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e040f42db195760cf40f6dbdf651826b544b65a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507568"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>設定要使用的 vCenter 身分識別來源 Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>關於 VMware vCenter 身分識別來源

VMware vCenter 支援不同的身分識別來源，以驗證存取 vCenter 的使用者。  您的 CloudSimple 私人雲端 vCenter 可設定為使用 Active Directory 進行驗證，讓您的 VMware 系統管理員存取 vCenter。 當安裝程式完成時， **cloudowner**使用者可以將使用者從身分識別來源新增至 vCenter。  

您可以透過下列任何方式來設定您的 Active Directory 網域和網域控制站：

* Active Directory 在內部部署環境中執行的網域和網域控制站
* 以 Azure 訂用帳戶中的虛擬機器形式在 Azure 上執行的網域和網域控制站 Active Directory
* 在您的私人雲端中執行的新 Active Directory 網域和網域控制站
* Azure Active Directory 服務

本指南說明在您的訂用帳戶中設定執行于內部部署或作為虛擬機器之 Active Directory 網域和網域控制站的工作。  如果您想要使用 Azure AD 做為身分識別來源，請參閱[使用 Azure AD 作為 CloudSimple 私用雲端上的 vCenter 身分識別提供者](azure-ad.md)，以取得設定身分識別來源的詳細指示。

[新增身分識別來源](#add-an-identity-source-on-vcenter)之前，請暫時[提升您的 vCenter 許可權](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新使用者必須僅新增至*雲端擁有者群組*、*雲端全域叢集-管理群組*、雲端-全域*存放裝置-* 系統管理群組、雲端-全域*網路-* 系統管理群組或*雲端全域 VM-管理群組*。  新增至系統*管理員*群組的使用者將會自動移除。  只有服務帳戶必須新增至*Administrators*群組，而服務帳戶不能用來登入 VSPHERE web UI。   


## <a name="identity-source-options"></a>識別來源選項

* [將內部部署 Active Directory 新增為單一登入身分識別來源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私人雲端上設定新的 Active Directory](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上設定 Active Directory](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>將內部部署 Active Directory 新增為單一登入身分識別來源

若要將內部部署 Active Directory 設定為單一登入身分識別來源，您需要：

* 從內部部署資料中心到私人雲端的[站對站 VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)連線。
* 已將內部部署 DNS 伺服器 IP 新增至 vCenter 和平臺服務控制站（PSC）。

設定 Active Directory 網域時，請使用下表中的資訊。

| **選項** | **說明** |
|------------|-----------------|
| **名稱** | 身分識別來源的名稱。 |
| **使用者的基本 DN** | 使用者的基本辨別名稱。 |
| **網域名稱** | 網域的 FQDN，例如 example.com。 請勿在此文字方塊中提供 IP 位址。 |
| **網域別名** | 網域 NetBIOS 名稱。 如果您使用 SSPI 驗證，請將 Active Directory 網域的 NetBIOS 名稱新增為身分識別來源的別名。 |
| **群組的基底 DN** | 群組的基本辨別名稱。 |
| **主伺服器 URL** | 網域的主域控制站 LDAP 伺服器。<br><br>請使用  `ldap://hostname:port`   或格式  `ldaps://hostname:port` 。 針對 LDAP 連線，埠通常為389，而 LDAPS 連線則為636。 針對 Active Directory 多網域控制站部署，針對 LDAP，埠通常為3268，而針對 LDAPS 則為3269。<br><br>當您  `ldaps://`   在主要或次要 LDAP URL 中使用時，需要為 Active Directory 伺服器的 LDAPS 端點建立信任的憑證。 |
| **次要伺服器 URL** | 用於容錯移轉的次要網域控制站 LDAP 伺服器位址。 |
| **選擇憑證** | 如果您想要將 LDAPS 與您的 Active Directory LDAP 伺服器或 OpenLDAP 伺服器身分識別來源搭配使用，請在  `ldaps://` [URL] 文字方塊中輸入後出現 [選擇憑證] 按鈕   。 不需要次要 URL。 |
| **使用者名稱** | 網域中使用者的識別碼，其中至少具有使用者和群組的基本 DN 的唯讀存取權。 |
| **密碼** | 使用者名稱所指定的密碼。 |

當您擁有上表中的資訊時，您可以將內部部署 Active Directory 新增為 vCenter 上的單一登入身分識別來源。

> [!TIP]
> 您可以在[VMware 檔頁面](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)上找到有關單一登入識別來源的詳細資訊。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私人雲端上設定新的 Active Directory

您可以在私人雲端上設定新的 Active Directory 網域，並使用它做為單一登入的身分識別來源。  Active Directory 網域可以是現有 Active Directory 樹系的一部分，或可設定為獨立樹系。

### <a name="new-active-directory-forest-and-domain"></a>新增 Active Directory 樹系和網域

若要設定新的 Active Directory 樹系和網域，您需要：

* 一或多部執行 Microsoft Windows Server 的虛擬機器，做為新 Active Directory 樹系和網域的網域控制站。
* 一或多部執行 DNS 服務以進行名稱解析的虛擬機器。

如需詳細步驟，請參閱[安裝新的 Windows Server 2012 Active Directory 樹](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)系。

> [!TIP]
> 如需服務的高可用性，建議您設定多個網域控制站和 DNS 伺服器。

設定 Active Directory 樹系和網域之後，您可以[在 vCenter 上](#add-an-identity-source-on-vcenter)為新 Active Directory 新增身分識別來源。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>現有 Active Directory 樹系中的新 Active Directory 網域

若要在現有的 Active Directory 樹系中設定新的 Active Directory 網域，您需要：

* Active Directory 樹系位置的站對站 VPN 連線。
* DNS 伺服器，以解析現有 Active Directory 樹系的名稱。

如需詳細步驟，請參閱[安裝新的 Windows Server 2012 Active Directory 的子域或樹狀目錄網域](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)。

設定 Active Directory 網域之後，您可以[在 vCenter 上](#add-an-identity-source-on-vcenter)為新 Active Directory 新增身分識別來源。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上設定 Active Directory

在 Azure 上執行 Active Directory 類似于在內部部署環境中執行的 Active Directory。  若要將在 Azure 上執行的 Active Directory 設定為 vCenter 上的單一登入身分識別來源，vCenter server 和 PSC 必須能夠與 Active Directory 服務執行所在的 Azure 虛擬網路具有網路連線能力。  您可以使用 azure 虛擬網路連線，從執行 Active Directory 服務的 Azure 虛擬網路中[使用 ExpressRoute](azure-expressroute-connection.md)建立此連線，以 CloudSimple 私人雲端。

建立網路連線之後，請依照[將內部部署 Active Directory 新增為單一登入身分識別來源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)中的步驟，將它新增為身分識別來源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上新增身分識別來源

1. [提升](escalate-private-cloud-privileges.md)私人雲端上的許可權。

2. 登入私人雲端的 vCenter。

3. 選取 [**首頁] > [管理**]。

    ![系統管理](media/OnPremAD01.png)

4. 選取 [**單一登入] >** 設定]。

    ![單一登入](media/OnPremAD02.png)

5. 開啟 [身分**識別來源**] 索引標籤，然後按一下 **+** 以新增新的身分識別來源。

    ![身分識別來源](media/OnPremAD03.png)

6. 選取 [ **Active Directory] 做為 LDAP 伺服器**，然後按 **[下一步]**。

    ![Active Directory](media/OnPremAD04.png)

7. 指定環境的身分識別來源參數，然後按 **[下一步]**。

    ![Active Directory](media/OnPremAD05.png)

8. 檢閱設定，然後按一下 [完成]。
