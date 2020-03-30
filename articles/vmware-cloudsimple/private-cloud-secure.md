---
title: Azure VMware 解決方案（按雲簡單 - 安全私有雲）
description: 描述如何通過雲簡單私有雲保護 Azure VMware 解決方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565973"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>如何保護您的私有雲環境

定義 Azure 雲簡單服務、雲簡單門戶和來自 Azure 的私有雲的基於角色的存取控制 （RBAC）。  使用 VMware SSO 指定用於訪問私有雲 vCenter 的使用者、組和角色。  

## <a name="rbac-for-cloudsimple-service"></a>用於雲簡單服務的 RBAC

創建雲簡單服務需要在 Azure 訂閱上**擔任擁有者**或**參與者**角色。  預設情況下，所有擁有者和參與者都可以創建雲簡單服務並訪問 CloudSimple 門戶以創建和管理私有雲。  每個區域只能創建一個 CloudSimple 服務。  要限制對特定管理員的訪問，請按照以下步驟操作。

1. 在 Azure 門戶上的新**資源組中**創建雲簡單服務
2. 為資源組指定 RBAC。
3. 購買節點並使用與 CloudSimple 服務相同的資源組

只有資源組中具有**擁有者**或**參與者**許可權的使用者才能看到雲簡單服務並啟動雲簡單門戶。

有關 RBAC 的詳細資訊，請參閱[什麼是 Azure 資源的基於角色的存取控制 （RBAC）。](../role-based-access-control/overview.md)

## <a name="rbac-for-private-cloud-vcenter"></a>用於私有雲 vCenter 的 RBAC

創建私有雲`CloudOwner@cloudsimple.local`時，在 vCenter SSO 域中創建預設使用者。  雲擁有者使用者具有管理 vCenter 的許可權。 其他標識源將添加到 vCenter SSO 中，以便向不同的使用者授予存取權限。  預定義的角色和組設置在 vCenter 上，可用於添加其他使用者。

### <a name="add-new-users-to-vcenter"></a>將新使用者添加到 vCenter

1. [升級](escalate-private-cloud-privileges.md)**雲擁有者\@雲簡單.** 私有雲上的本地使用者的許可權。
2. 使用**雲擁有者\@雲簡單**登錄 vCenter. 本地
3. [添加 vCenter 單登錄使用者](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)。
4. 將使用者添加到[vCenter 單一登入組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

有關預定義角色和組的詳細資訊，請參閱[VMware vCenter 的雲簡單私有雲許可權模型](learn-private-cloud-permissions.md)一文。

### <a name="add-new-identity-sources"></a>添加新標識源

您可以為私有雲的 vCenter SSO 域添加其他標識提供程式。  標識提供程式提供身份驗證，vCenter SSO 組為使用者提供授權。

* 在私有雲 vCenter 上[使用活動目錄作為標識提供程式](set-vcenter-identity.md)。
* 在私有雲 vCenter 上[使用 Azure AD 作為標識提供程式](azure-ad.md)

1. [升級](escalate-private-cloud-privileges.md)**雲擁有者\@雲簡單.** 私有雲上的本地使用者的許可權。
2. 使用**雲擁有者\@雲簡單**登錄 vCenter. 本地
3. 將使用者從標識提供程式添加到[vCenter 單一登入組](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

## <a name="secure-network-on-your-private-cloud-environment"></a>在私有雲環境中保護網路

私有雲環境的網路安全是通過保護網路訪問和控制資源之間的網路流量來控制的。

### <a name="access-to-private-cloud-resources"></a>訪問私有雲資源

私有雲 vCenter 和資源訪問通過安全網路連接進行：

* **[快速路由連接](on-premises-connection.md)**。 ExpressRoute 提供來自本地環境的安全、高頻寬、低延遲連接。  使用連接可以允許您的本機服務、網路和使用者訪問您的私有雲 vCenter。
* **[網站到網站 VPN 閘道](vpn-gateway.md)**。 網站到網站 VPN 通過安全隧道從本地訪問您的私有雲資源。  您可以指定哪些本地網路可以向私有雲發送和接收網路流量。
* **[點對點 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**。 使用點對點 VPN 連接快速遠端存取私有雲 vCenter。

### <a name="control-network-traffic-in-private-cloud"></a>控制私有雲中的網路流量

防火牆表和規則控制私有雲中的網路流量。  防火牆表允許您根據表中定義的規則的組合控制源網路或 IP 位址與目標網路或 IP 位址之間的網路流量。

1. 創建[防火牆表](firewall.md#add-a-new-firewall-table)。
2. [將規則添加到](firewall.md#create-a-firewall-rule)防火牆表。
3. [將防火牆表附加到 VLAN/子網](firewall.md#attach-vlans-subnet)。
