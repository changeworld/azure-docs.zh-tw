---
title: Azure VMware 解決方案（依 CloudSimple）-安全的私用雲端
description: 說明如何透過 CloudSimple 私用雲端來保護 Azure VMware 解決方案
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899162"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>如何保護您的私用雲端環境

針對 CloudSimple Service、CloudSimple 入口網站和 Azure 中的私人雲端，定義角色型存取控制 (RBAC) 。  存取私人雲端 vCenter 的使用者、群組和角色是使用 VMware SSO 指定的。  

## <a name="azure-rbac-for-cloudsimple-service"></a>適用于 CloudSimple 服務的 Azure RBAC

建立 CloudSimple 服務需要 Azure 訂用帳戶的 **擁有** 者或 **參與者** 角色。  依預設，所有擁有者和參與者都可以建立 CloudSimple 服務，並存取 CloudSimple 入口網站來建立和管理私人雲端。  每個區域只可建立一個 CloudSimple 服務。  若要限制特定系統管理員的存取權，請依照下列程式進行操作。

1. 在 Azure 入口網站上的新 **資源群組** 中建立 CloudSimple 服務
2. 指定資源群組的 Azure RBAC。
3. 購買節點並使用與 CloudSimple 服務相同的資源群組

只有擁有資源 **群組擁有者** 或 **參與者** 許可權的使用者，才會看到 CloudSimple 服務和啟動 CloudSimple 入口網站。

如需詳細資訊，請參閱 [什麼是 azure (AZURE RBAC) 的 azure 角色型存取控制 ](../role-based-access-control/overview.md)。

## <a name="rbac-for-private-cloud-vcenter"></a>私人雲端 vCenter 的 RBAC

`CloudOwner@cloudsimple.local`建立私人雲端時，會在 VCENTER SSO 網域中建立預設使用者。  CloudOwner 使用者具有管理 vCenter 的許可權。 額外的身分識別來源會新增至 vCenter SSO，以將存取權提供給不同的使用者。  預先定義的角色和群組會在 vCenter 上設定，可用來新增其他使用者。

### <a name="add-new-users-to-vcenter"></a>將新使用者新增至 vCenter

1. [提升](escalate-private-cloud-privileges.md) **CloudOwner \@ Cloudsimple** 的許可權。私用雲端上的本機使用者。
2. 使用 **CloudOwner \@ cloudsimple** 登入 vCenter
3. [新增 VCenter 單一 Sign-On 使用者](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)。
4. 將使用者新增至 [vCenter 單一登入群組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

如需預先定義之角色和群組的詳細資訊，請參閱 [VMware vCenter 的 CloudSimple 私用雲端許可權模型一](learn-private-cloud-permissions.md) 文。

### <a name="add-new-identity-sources"></a>新增身分識別來源

您可以為私人雲端的 vCenter SSO 網域新增其他識別提供者。  身分識別提供者提供驗證和 vCenter SSO 群組，可為使用者提供授權。

* [使用 Active Directory 作為](set-vcenter-identity.md) 私人雲端 vCenter 上的身分識別提供者。
* [使用 Azure AD 作為](azure-ad.md) 私人雲端 vCenter 上的身分識別提供者

1. [提升](escalate-private-cloud-privileges.md) **CloudOwner \@ Cloudsimple** 的許可權。私用雲端上的本機使用者。
2. 使用 **CloudOwner \@ cloudsimple** 登入 vCenter
3. 從身分識別提供者將使用者新增至 [vCenter 單一登入群組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

## <a name="secure-network-on-your-private-cloud-environment"></a>在私用雲端環境中保護網路安全

私用雲端環境的網路安全性是藉由保護網路存取，以及控制資源之間的網路流量來控制。

### <a name="access-to-private-cloud-resources"></a>存取私用雲端資源

私人雲端 vCenter 和資源的存取是透過安全的網路連線：

* **[ExpressRoute 連接](on-premises-connection.md)**。 ExpressRoute 提供來自內部部署環境的安全、高頻寬、低延遲的連線。  使用連接可讓您的內部部署服務、網路和使用者存取您的私人雲端 vCenter。
* **[站對站 VPN 閘道](vpn-gateway.md)**。 站對站 VPN 可讓您透過安全通道，從內部部署存取私人雲端資源。  您可以指定哪些內部部署網路可以傳送和接收您私人雲端的網路流量。
* **[點對站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**。 使用點對站 VPN 連線，以快速從遠端存取您的私用雲端 vCenter。

### <a name="control-network-traffic-in-private-cloud"></a>控制私人雲端中的網路流量

防火牆資料表和規則會控制私人雲端中的網路流量。  防火牆資料表可讓您根據資料表中定義的規則組合來控制來源網路或 IP 位址，以及目的地網路或 IP 位址之間的網路流量。

1. 建立 [防火牆資料表](firewall.md#add-a-new-firewall-table)。
2. [將規則新增](firewall.md#create-a-firewall-rule) 至防火牆資料表。
3. [將防火牆資料表附加至 VLAN/子網](firewall.md#attach-vlans-subnet)。
