---
title: Azure VMware Solution by CloudSimple-在私人雲端上設定 vCenter 身分識別來源
description: 說明如何設定私人雲端 vCenter 以使用 Active Directory 進行驗證，以供 VMware 系統管理員存取 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f6f3b10219775adb02d47a91da2573ea99f30ac0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212257"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>設定要使用的 vCenter 身分識別來源 Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>關於 VMware vCenter 身分識別來源

VMware vCenter 支援不同的身分識別來源，以供存取 vCenter 的使用者進行驗證。  您的 CloudSimple 私人雲端 vCenter 可以設定為向 Active Directory 進行驗證，以供 VMware 系統管理員存取 vCenter。 當安裝程式完成時， **cloudowner** 使用者可以將使用者從身分識別來源新增至 vCenter。  

您可以利用下列任何一種方式來設定 Active Directory 網域和網域控制站：

* Active Directory 在內部部署執行的網域和網域控制站
* Active Directory 在 Azure 上執行的網域和網域控制站作為 Azure 訂用帳戶中的虛擬機器
* 在您的私人雲端中執行的新 Active Directory 網域和網域控制站
* Azure Active Directory 服務

本指南說明在您的訂用帳戶中，設定在內部部署或虛擬機器上執行的 Active Directory 網域和網域控制站的工作。  如果您想要使用 Azure AD 作為身分識別來源，請參閱 [使用 Azure AD 作為 CloudSimple 私人雲端上的 vCenter 身分識別提供者](azure-ad.md) ，以取得設定身分識別來源的詳細指示。

[新增身分識別來源](#add-an-identity-source-on-vcenter)之前，請暫時[提升您的 vCenter 許可權](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新使用者只能新增至 *雲端擁有者群組*、 *雲端全域*叢集-系統管理群組、雲端-全域 *存放裝置-* 管理群組、 *雲端全域-網路-系統管理* 群組或 *雲端全域 VM-管理群組*。  新增至系統 *管理員* 群組的使用者將會自動移除。  只有服務帳戶必須加入至系統 *管理員* 群組，而且服務帳戶不能用來登入 VSPHERE web UI。   


## <a name="identity-source-options"></a>身分識別來源選項

* [以單一登入身分識別來源的形式新增內部部署 Active Directory](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私人雲端上設定新的 Active Directory](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上設定 Active Directory](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory (不支援 Windows 整合式驗證) 。** 僅支援透過 LDAP 選項 Active Directory 作為身分識別來源。

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>將內部部署 Active Directory 新增為單一 Sign-On 身分識別來源

若要將內部部署 Active Directory 設定為單一 Sign-On 身分識別來源，您需要：

* 從內部部署資料中心到私人雲端的[站對站 VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)連線。
* 新增至 vCenter 的內部部署 DNS 伺服器 IP 和 Platform Services Controller (PSC) 。

設定 Active Directory 網域時，請使用下表中的資訊。

| **選項** | **說明** |
|------------|-----------------|
| **名稱** | 身分識別來源的名稱。 |
| **使用者的基底 DN** | 使用者的基底分辨名稱。 |
| **網域名稱** | 網域的 FQDN，例如，example.com。 請勿在此文字方塊中提供 IP 位址。 |
| **網域別名** | 網域 NetBIOS 名稱。 如果您使用 SSPI 驗證，請將 Active Directory 網域的 NetBIOS 名稱新增為身分識別來源的別名。 |
| **群組的基底 DN** | 群組的基底分辨名稱。 |
| **主伺服器 URL** | 網域的網域主控站 LDAP 伺服器。<br><br>使用格式  `ldap://hostname:port`   或  `ldaps://hostname:port` 。 埠通常是389（適用于 LDAP 連線）和636（適用于 LDAPS 連接）。 針對 Active Directory 多網域控制站部署，LDAP 的埠通常是3268，而針對 LDAPS 則是3269。<br><br>當您  `ldaps://`   在主要或次要 LDAP URL 中使用時，需要為 Active Directory 伺服器的 LDAPS 端點建立信任的憑證。 |
| **次要伺服器 URL** | 用於容錯移轉的次要網域控制站 LDAP 伺服器位址。 |
| **選擇憑證** | 如果您想要搭配 Active Directory LDAP 伺服器或 OpenLDAP 伺服器身分識別來源使用 LDAPS，在  `ldaps://` [URL] 文字方塊中輸入之後，會出現 [選擇憑證] 按鈕   。 不需要次要 URL。 |
| **使用者名稱** | 網域中使用者的識別碼，其具有使用者和群組的基本 DN 的最小唯讀存取權。 |
| **密碼** | 使用者名稱所指定之使用者的密碼。 |

當您擁有上表中的資訊時，您可以將內部部署 Active Directory 新增為 vCenter 上的單一 Sign-On 身分識別來源。

> [!TIP]
> 您可以在 [VMware 檔頁面](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)上找到單一 Sign-On 身分識別來源的詳細資訊。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私人雲端上設定新的 Active Directory

您可以在私人雲端上設定新的 Active Directory 網域，並使用它作為單一登入的身分識別來源。  Active Directory 網域可以是現有 Active Directory 樹系的一部分，也可以設定為獨立樹系。

### <a name="new-active-directory-forest-and-domain"></a>新 Active Directory 樹系和網域

若要設定新的 Active Directory 樹系和網域，您需要：

* 一或多部執行 Microsoft Windows Server 的虛擬機器，做為新 Active Directory 樹系和網域的網域控制站。
* 一或多個執行 DNS 服務的虛擬機器進行名稱解析。

如需詳細步驟，請參閱 [安裝新的 Windows Server 2012 Active Directory 樹](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) 系。

> [!TIP]
> 針對服務的高可用性，建議您設定多個網域控制站和 DNS 伺服器。

設定 Active Directory 樹系和網域之後，您可以 [在 vCenter 上](#add-an-identity-source-on-vcenter) 為新的 Active Directory 新增身分識別來源。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>現有 Active Directory 樹系中新的 Active Directory 網域

若要在現有的 Active Directory 樹系中設定新的 Active Directory 網域，您需要：

* 您 Active Directory 樹系位置的站對站 VPN 連線。
* DNS 伺服器，以解析現有 Active Directory 樹系的名稱。

如需詳細步驟，請參閱 [安裝新的 Windows Server 2012 Active Directory 子域或樹狀目錄網域](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) 。

設定 Active Directory 網域之後，您可以 [在 vCenter 上](#add-an-identity-source-on-vcenter) 為新的 Active Directory 新增身分識別來源。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上設定 Active Directory

在 Azure 上執行的 Active Directory 類似于在內部部署環境中執行的 Active Directory。  若要將在 Azure 上執行的 Active Directory 設定為 vCenter 上的單一 Sign-On 身分識別來源，vCenter server 和 PSC 必須具有與 Active Directory 服務執行所在的 Azure 虛擬網路的網路連線能力。  您可以使用 [Azure 虛擬網路](azure-expressroute-connection.md) 連線，從 Active Directory 服務執行所在的 azure 虛擬網路連線到 CloudSimple 私人雲端，以建立此連線能力。

建立網路連線之後，請遵循 [新增內部部署 Active Directory 中的步驟，作為單一 Sign-On 身分識別來源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) ，以將其新增為身分識別來源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上新增身分識別來源

1. [提升](escalate-private-cloud-privileges.md) 私用雲端上的許可權。

2. 登入您私人雲端的 vCenter。

3. 選取 [ **家用 > 系統管理**]。

    ![系統管理](media/OnPremAD01.png)

4. 選取 [ **單一登入 >** 設定]。

    ![單一登入](media/OnPremAD02.png)

5. 開啟 [身分 **識別來源** ] 索引標籤，然後按一下 **+** 以新增身分識別來源。

    ![身分識別來源](media/OnPremAD03.png)

6. 選取 **Active Directory 為 LDAP 伺服器** ，然後按 **[下一步]**。

    ![Active Directory](media/OnPremAD04.png)

7. 指定環境的身分識別來源參數，然後按 **[下一步]**。

    ![Active Directory](media/OnPremAD05.png)

8. 檢閱設定，然後按一下 [完成]。
