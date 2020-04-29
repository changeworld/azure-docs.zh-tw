---
title: 依 CloudSimple 的 Azure VMware 解決方案-保護私人雲端
description: 說明如何藉由 CloudSimple 私用雲端來保護 Azure VMware 解決方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77565973"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>如何保護您的私用雲端環境

從 Azure 定義 CloudSimple 服務、CloudSimple 入口網站和私人雲端的角色型存取控制（RBAC）。  存取私人雲端 vCenter 的使用者、群組和角色是使用 VMware SSO 指定的。  

## <a name="rbac-for-cloudsimple-service"></a>CloudSimple 服務的 RBAC

建立 CloudSimple 服務需要 Azure 訂用帳戶的**擁有**者或**參與者**角色。  根據預設，所有擁有者和參與者都可以建立 CloudSimple 服務，並存取 CloudSimple 入口網站來建立和管理私人雲端。  每個區域只能建立一個 CloudSimple 服務。  若要限制對特定系統管理員的存取，請遵循下列程式。

1. 在 Azure 入口網站上的新**資源群組**中建立 CloudSimple 服務
2. 指定資源群組的 RBAC。
3. 購買節點並使用與 CloudSimple 服務相同的資源群組

只有在資源群組上擁有 **「擁有者」或「** **參與者**」許可權的使用者，才會看到 CloudSimple 服務和啟動 CloudSimple 入口網站。

如需 RBAC 的詳細資訊，請參閱[什麼是適用于 Azure 資源的角色型存取控制（RBAC）](../role-based-access-control/overview.md)。

## <a name="rbac-for-private-cloud-vcenter"></a>私用雲端 vCenter 的 RBAC

建立私人雲端`CloudOwner@cloudsimple.local`時，會在 vCenter SSO 網域中建立預設使用者。  CloudOwner 使用者有管理 vCenter 的許可權。 其他身分識別來源會新增至 vCenter SSO，以提供存取權給不同的使用者。  已在 vCenter 上設定預先定義的角色和群組，可用來新增其他使用者。

### <a name="add-new-users-to-vcenter"></a>將新的使用者新增至 vCenter

1. [提升](escalate-private-cloud-privileges.md) **CloudOwner\@Cloudsimple**的許可權。私人雲端上的本機使用者。
2. 使用**CloudOwner\@Cloudsimple**登入 vCenter
3. [新增 VCenter 單一登入使用者](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)。
4. 將使用者新增至[vCenter 單一登入群組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

如需預先定義的角色和群組的詳細資訊，請參閱[VMware vCenter 的 CloudSimple 私用雲端許可權模型一](learn-private-cloud-permissions.md)文。

### <a name="add-new-identity-sources"></a>加入新的身分識別來源

您可以為私人雲端的 vCenter SSO 網域新增其他身分識別提供者。  識別提供者提供驗證，而 vCenter SSO 群組則提供使用者的授權。

* [使用 Active Directory 做為](set-vcenter-identity.md)私人雲端 vCenter 上的身分識別提供者。
* [使用 Azure AD 做為](azure-ad.md)私人雲端 vCenter 上的身分識別提供者

1. [提升](escalate-private-cloud-privileges.md) **CloudOwner\@Cloudsimple**的許可權。私人雲端上的本機使用者。
2. 使用**CloudOwner\@Cloudsimple**登入 vCenter
3. 將使用者從識別提供者新增至[vCenter 單一登入群組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

## <a name="secure-network-on-your-private-cloud-environment"></a>保護私人雲端環境中的網路

私用雲端環境的網路安全性是藉由保護網路存取和控制資源之間的網路流量來控制。

### <a name="access-to-private-cloud-resources"></a>存取私用雲端資源

私用雲端 vCenter 和資源存取是透過安全的網路連線：

* **[ExpressRoute 連接](on-premises-connection.md)**。 ExpressRoute 提供來自內部部署環境的安全、高頻寬、低延遲的連線。  使用此連線可讓您的內部部署服務、網路和使用者存取您的私人雲端 vCenter。
* **[站對站 VPN 閘道](vpn-gateway.md)**。 站對站 VPN 可讓您透過安全通道，從內部部署存取私人雲端資源。  您可以指定哪些內部部署網路可以傳送和接收私人雲端的網路流量。
* **[點對站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**。 使用點對站 VPN 連線，以快速從遠端存取您的私用雲端 vCenter。

### <a name="control-network-traffic-in-private-cloud"></a>控制私人雲端中的網路流量

防火牆資料表和規則會控制私人雲端中的網路流量。  [防火牆] 資料表可讓您根據資料表中定義的規則組合，控制來源網路或 IP 位址與目的地網路或 IP 位址之間的網路流量。

1. 建立[防火牆資料表](firewall.md#add-a-new-firewall-table)。
2. [將規則新增](firewall.md#create-a-firewall-rule)至防火牆資料表。
3. [將防火牆資料表附加至 VLAN/子網](firewall.md#attach-vlans-subnet)。
