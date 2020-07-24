---
title: 快速入門：建立私人雲端
titleSuffix: Azure VMware Solutions by CloudSimple
description: 瞭解如何透過 CloudSimple 使用 Azure VMware 解決方案來建立及設定私人雲端
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073911"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入門-設定私人雲端環境

在本文中，您將瞭解如何建立 CloudSimple 私人雲端，並設定您的私用雲端環境。

## <a name="before-you-begin"></a>開始之前

審查[網路必要條件](cloudsimple-network-checklist.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-private-cloud"></a>建立私人雲端

私用雲端是一種隔離的 VMware 堆疊，可支援 ESXi 主機、vCenter、vSAN 和 NSX。

私人雲端是透過 CloudSimple 入口網站來管理。 他們在自己的管理網域中有自己的 vCenter server。 堆疊會在專用節點和隔離的裸機硬體節點上執行。

1. 選取 [所有服務]。
2. 搜尋**CloudSimple Services**。
3. 選取您要在其上建立私人雲端的 CloudSimple 服務。
4. 在 **[總覽**] 中，按一下 [**建立私人雲端**] 以開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  若出現提示，請使用您的 Azure 登入認證登入。  

    ![從 Azure 建立私人雲端](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 入口網站中，提供私人雲端的 [名稱]。
6. 選取私人雲端的 [**位置**]。
7. 選取 [**節點類型**]，與您在 Azure 上布建的內容一致。
8. 指定**節點計數**。  至少需要三個節點，才能建立私人雲端。

    ![建立私用雲端-基本資訊](media/create-private-cloud-basic-info.png)

9. 按 **[下一步： Advanced Options]**。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 請確定 CIDR 範圍不會與您的任何內部部署或其他 Azure 子網（虛擬網路）或閘道子網重迭。

    **CIDR 範圍選項：** /24、/23、/22 或/21。 A/24 CIDR 範圍最多支援26個節點，/23 CIDR 範圍最多支援58個節點，而/22 和/21 CIDR 範圍支援64個節點（私人雲端中的節點數目上限）。  若要深入瞭解 Vlan 和子網，請參閱[vlan 和子網總覽](cloudsimple-vlans-subnets.md)。

      > [!IMPORTANT]
      > VSphere/vSAN CIDR 範圍中的 IP 位址會保留供私人雲端基礎結構使用。  請勿在任何虛擬機器上使用此範圍內的 IP 位址。

11. 按 **[下一步]： [審查並建立]**。
12. 檢查設定。 如果您需要變更任何設定，請按一下 [**上一步**]。
13. 按一下 [建立]。

私用雲端布建程式隨即啟動。  布建私人雲端最多可能需要兩個小時的時間。

## <a name="launch-cloudsimple-portal"></a>啟動 CloudSimple 入口網站

您可以從 Azure 入口網站存取 CloudSimple 入口網站。  CloudSimple 入口網站會使用「單一登入」（SSO），透過您的 Azure 登入認證來啟動。  存取 CloudSimple 入口網站需要您授權**CloudSimple 服務授權**應用程式。  如需授與許可權的詳細資訊，請參閱[同意 CloudSimple 服務授權應用程式](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)。

1. 選取 [所有服務]。
2. 搜尋**CloudSimple Services**。
3. 選取您要在其上建立私人雲端的 CloudSimple 服務。
4. 在 [總覽] 中，按一下 **[移至 CloudSimple 入口網站**] 以開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  若出現提示，請使用您的 Azure 登入認證登入。  

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>建立點對站 VPN

點對站 VPN 連線是從電腦連線到私人雲端最簡單的方式。 如果您是從遠端連線到私人雲端，請使用點對站 VPN 連線。  若要快速存取您的私人雲端，請遵循下列步驟。  您可以使用[站對站 VPN](vpn-gateway.md)或[Azure ExpressRoute](on-premises-connection.md)，從您的內部部署網路存取 CloudSimple 區域。

### <a name="create-gateway"></a>建立閘道

1. 啟動 CloudSimple 入口網站，然後選取 [**網路**]。
2. 選取 [ **VPN 閘道**]。
3. 按一下 [**新增 VPN 閘道**]。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 在 [**閘道**設定] 中指定下列設定，然後按 **[下一步]**。

    * 選取 [**點對站 VPN** ] 做為閘道類型。
    * 輸入用來識別閘道的名稱。
    * 選取您的 CloudSimple 服務部署所在的 Azure 位置。
    * 指定點對站閘道的用戶端子網。  當您連接時，將會從這個子網提供 DHCP 位址。

5. 針對 [**連接/使用者**]，指定下列設定，然後按 **[下一步]**。

    * 若要自動允許所有目前和未來的使用者透過此點對站閘道存取私人雲端，請選取 [**自動新增所有使用者**]。 當您選取此選項時，會自動選取 [使用者] 清單中的所有使用者。 您可以取消挑選清單中的個別使用者，以覆寫 [自動] 選項。
    * 若只要選取個別使用者，請按一下 [使用者] 清單中的核取方塊。

6. [Vlan/Subnet] 區段可讓您指定閘道和連線的管理和使用者 Vlan/子網。

    * [**自動新增**] 選項會為此閘道設定全域原則。 這些設定適用于目前的閘道。 這些設定可以在 [**選取**] 區域中覆寫。
    * 選取 [**新增私人雲端的管理 vlan/子網**]。
    * 若要新增所有使用者定義的 Vlan/子網，請按一下 [**新增使用者定義的 vlan/子網**]。
    * **選取**設定會覆寫 [**自動新增**] 底下的全域設定。

7. 按 **[下一步]** 以查看設定。 按一下 [編輯] 圖示進行任何變更。
8. 按一下 [**建立**] 以建立 VPN 閘道。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用點對站 VPN 連接到 CloudSimple

需要 VPN 用戶端，才能從您的電腦連接到 CloudSimple。  下載[OpenVPN client](https://openvpn.net/community-downloads/) for Windows 或[Viscosity](https://www.sparklabs.com/viscosity/download/) For macOS and OS X。

1. 啟動 CloudSimple 入口網站，然後選取 [**網路**]。
2. 選取 [ **VPN 閘道**]。
3. 從 VPN 閘道清單中，按一下 [點對站 VPN 閘道]。
4. 選取 [使用者]。
5. 按一下 [**下載我的 VPN**設定]。

    ![下載 VPN 組態](media/download-p2s-vpn-configuration.png)

6. 在您的 VPN 用戶端上匯入設定。

    * [在 Windows 用戶端上匯入](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)設定的指示
    * [在 macOS 或 OS X 上匯入](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)設定的指示

7. 連接到 CloudSimple。

## <a name="create-a-vlan-for-your-workload-vms"></a>建立工作負載 Vm 的 VLAN

建立私人雲端之後，請建立一個 VLAN，您將在其中部署工作負載/應用程式 Vm。

1. 在 CloudSimple 入口網站中，選取 [**網路**]。
2. 按一下 [ **VLAN/子網**]。
3. 按一下 [**建立 VLAN/子網**]。

    ![建立 VLAN/子網](media/create-new-vlan-subnet.png)

4. 為新的 VLAN/子網選取**私人雲端**。
5. 從清單中選取 [VLAN ID]。  
6. 輸入子網名稱以識別子網。
7. 指定子網 CIDR 範圍和遮罩。  此範圍不得與任何現有的子網重迭。
8. 按一下 [提交] 。

    ![建立 VLAN/子網詳細資料](media/create-new-vlan-subnet-details.png)

將會建立 VLAN/子網。  您現在可以使用此 VLAN ID 在私人雲端 vCenter 上建立分散式通訊埠群組。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>將您的環境連線至 Azure 虛擬網路

CloudSimple 為您的私用雲端提供 ExpressRoute 線路。 您可以將 Azure 上的虛擬網路連線到 ExpressRoute 線路。 如需設定連線的完整詳細資料，請遵循[使用 ExpressRoute 的 Azure 虛擬網路](./cloudsimple-azure-network-connection.md)連線中的步驟。

## <a name="sign-in-to-vcenter"></a>登入 vCenter

您現在可以登入 vCenter 以設定虛擬機器和原則。

1. 若要存取 vCenter，請從 CloudSimple 入口網站啟動。 在首頁的 [**一般**工作] 底下，按一下 [**啟動 vSphere 用戶端**]。  選取私人雲端，然後按一下 [在私人雲端上**啟動 VSphere 用戶端**]。

    ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 選取您慣用的 vSphere 用戶端來存取 vCenter，並使用您的使用者名稱和密碼登入。  預設為：
    * 使用者名稱：`CloudOwner@cloudsimple.local`
    * 密碼：`CloudSimple123!`

下一個程式中的 vCenter 畫面是來自 vSphere （HTML5）用戶端。

## <a name="change-your-vcenter-password"></a>變更您的 vCenter 密碼

CloudSimple 建議您在第一次登入 vCenter 時變更密碼。  
您所設定的密碼必須符合下列需求：

* 最長存留期：密碼必須每365天變更一次
* 限制重複使用：使用者無法重複使用前五個密碼
* 長度： 8-20 個字元
* 特殊字元：至少一個特殊字元
* 字母字元：至少一個大寫字元、a-z 和至少一個小寫字元 a-z
* 數位：至少一個數位字元，0-9
* 最大相同相鄰字元數：三

    範例： CC 或副本可接受作為密碼的一部分，但 CCCC 則不會。

如果您設定的密碼不符合需求：

* 如果您使用 vSphere Flash 用戶端，它會報告錯誤
* 如果您使用 HTML5 用戶端，則不會報告錯誤。 用戶端不接受變更，舊密碼仍可繼續運作。

## <a name="access-nsx-manager"></a>存取 NSX manager

使用預設密碼部署了「NSX 管理員」。 

* 使用者名稱： **admin**
* 密碼： **CloudSimple123！**

您可以在 CloudSimple 入口網站上找到 NSX manager 的完整功能變數名稱（FQDN）和 IP 位址。

1. 啟動 CloudSimple 入口網站，然後選取 [**資源**]。
2. 按一下您要使用的私人雲端。
3. 選取**vSphere 管理網路**
4. 使用「 **NSX 管理員**」的 FQDN 或 IP 位址，並使用網頁瀏覽器進行連接。

    ![尋找 NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>建立通訊埠群組

若要在 vSphere 中建立分散式通訊埠群組：

1. 遵循[VSphere 網路功能指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)中的「新增分散式通訊埠群組」中的指示。
2. 設定分散式通訊埠群組時，請提供在[為您的工作負載 Vm 建立 vlan](#create-a-vlan-for-your-workload-vms)中建立的 vlan ID。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 連接到內部部署網路](on-premises-connection.md)
* [從內部部署設定站對站 VPN](vpn-gateway.md)
