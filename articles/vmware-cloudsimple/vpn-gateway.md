---
title: Azure VMware Solution by CloudSimple-設定 VPN 閘道
description: 說明如何設定點對站 VPN 閘道和站對站 VPN 閘道，並在您的內部部署網路與 CloudSimple 私用雲端之間建立連線
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79279490"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>在 CloudSimple 網路上設定 VPN 閘道

VPN 閘道可讓您從內部部署網路和從遠端用戶端電腦連線到 CloudSimple 網路。 內部部署網路和 CloudSimple 網路之間的 VPN 連線可讓您存取私人雲端上的 vCenter 和工作負載。 CloudSimple 支援站對站 VPN 和點對站 VPN 閘道。

## <a name="vpn-gateway-types"></a>VPN 閘道類型

* **站對站 VPN**連線可讓您設定私人雲端工作負載，以存取內部部署服務。 您也可以使用內部部署 Active Directory 做為身分識別來源，以向您的私用雲端 vCenter 進行驗證。  目前僅支援以**原則為基礎的 VPN**類型。
* **點對站 VPN**連線是從電腦連線到私人雲端最簡單的方式。 使用點對站 VPN 連線能力，從遠端連線至私人雲端。 如需安裝點對站 VPN 連線用戶端的詳細資訊，請參閱[設定私人雲端的 VPN](set-up-vpn.md)連線。

在區域中，您可以建立一個點對站 VPN 閘道和一個站對站 VPN 閘道。

## <a name="automatic-addition-of-vlansubnets"></a>自動新增 VLAN/子網

CloudSimple VPN 閘道提供將 VLAN/子網新增至 VPN 閘道的原則。  原則可讓您為管理 VLAN/子網和使用者定義的 VLAN/子網指定不同的規則。  管理 VLAN/子網的規則適用于您所建立的任何新私人雲端。  使用者定義 Vlan/子網的規則可讓您針對站對站 VPN 閘道，將任何新的 VLAN/子網自動新增到現有或新的私人雲端，您可以為每個連線定義原則。

將 Vlan/子網新增至 VPN 閘道的原則，適用于站對站 VPN 和點對站 VPN 閘道。

## <a name="automatic-addition-of-users"></a>自動新增使用者

點對站 VPN 閘道可讓您為新的使用者定義自動新增原則。 根據預設，訂用帳戶的所有擁有者和參與者都可以存取 CloudSimple 入口網站。  只有在第一次啟動 CloudSimple 入口網站時，才會建立使用者。  選取 [**自動新增**規則] 可讓任何新使用者使用點對站 VPN 連線來存取 CloudSimple 網路。

## <a name="set-up-a-site-to-site-vpn-gateway"></a>設定站對站 VPN 閘道

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)，然後選取 [**網路**]。
2. 選取 [ **VPN 閘道**]。
3. 按一下 [**新增 VPN 閘道**]。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 在 [**閘道**設定] 中指定下列設定，然後按 **[下一步]**。

    * 選取 [**站對站 VPN** ] 作為 [閘道類型]。
    * 輸入用來識別閘道的名稱。
    * 選取您的 CloudSimple 服務部署所在的 Azure 位置。
    * （選擇性）啟用高可用性。

    ![建立站對站 VPN 閘道](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 若要啟用高可用性，您的內部部署 VPN 裝置必須支援連接到兩個 IP 位址。 一旦部署 VPN 閘道，就無法停用此選項。

5. 建立內部部署網路的第一個連接，然後按 **[下一步]**。

    * 輸入名稱以識別連接。
    * 針對 [對等 IP]，輸入您的內部部署 VPN 閘道的公用 IP 位址。
    * 輸入內部部署 VPN 閘道的對等識別碼。  對等識別碼通常是內部部署 VPN 閘道的公用 IP 位址。  如果您已在閘道上設定特定識別碼，請輸入識別碼。
    * 複製共用金鑰，以用於來自內部部署 VPN 閘道的連線。  若要變更預設的共用金鑰並指定一個新的，請按一下 [編輯] 圖示。
    * 針對 [內部**部署首碼**]，輸入將存取 CloudSimple 網路的內部部署 CIDR 首碼。  當您建立連接時，可以新增多個 CIDR 首碼。

    ![建立站對站 VPN 閘道連線](media/create-vpn-gateway-s2s-connection.png)

6. 在您的私人雲端網路上，啟用將從內部部署網路存取的 VLAN/子網，然後按 **[下一步]**。

    * 若要新增管理 VLAN/子網，請啟用 [**新增私人雲端的管理 vlan/子網**]。  VMotion 和 vSAN 子網需要管理子網。
    * 若要新增 vMotion 子網，請啟用 [**新增私人雲端的 vMotion 網路**]。
    * 若要新增 vSAN 子網，請啟用 [**新增私人雲端的 vSAN 子網**]。
    * 選取或取消選取特定的 Vlan。

    ![建立連線](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 檢查設定，然後按一下 [**提交**]。

    ![站對站 VPN 閘道審查和建立](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>建立點對站 VPN 閘道

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)，然後選取 [**網路**]。
2. 選取 [ **VPN 閘道**]。
3. 按一下 [**新增 VPN 閘道**]。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 在 [**閘道**設定] 中指定下列設定，然後按 **[下一步]**。

    * 選取 [**點對站 VPN** ] 做為閘道類型。
    * 輸入用來識別閘道的名稱。
    * 選取您的 CloudSimple 服務部署所在的 Azure 位置。
    * 指定點對站閘道的用戶端子網。  當您連線時，會從用戶端子網提供 DHCP 位址。

5. 針對 [**連接/使用者**]，指定下列設定，然後按 **[下一步]**。

    * 若要自動允許所有目前和未來的使用者透過點對站閘道存取私人雲端，請選取 [**自動新增所有使用者**]。 當您選取此選項時，會自動選取 [使用者] 清單中的 [所有使用者]。 您可以取消挑選清單中的個別使用者，以覆寫 [自動] 選項。
    * 若要選取個別使用者，請按一下 [使用者] 清單中的核取方塊。

6. [Vlan/Subnet] 區段可讓您指定閘道和連線的管理和使用者 Vlan/子網。

    * [**自動新增**] 選項會設定閘道的全域原則。 這些設定適用于目前的閘道。 這些設定可以在 [**選取**] 區域中覆寫。
    * 選取 [**新增私人雲端的管理 vlan/子網**]。 
    * 若要新增所有使用者定義的 Vlan/子網，請按一下 [**新增使用者定義的 vlan/子網**]。
    * **選取**設定會覆寫 [**自動新增**] 底下的全域設定。

7. 按 **[下一步]** 以查看設定。 按一下 [編輯] 圖示進行任何變更。
8. 按一下 [**建立**] 以建立 VPN 閘道。

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>點對站 VPN 閘道的用戶端子網和通訊協定

點對站 VPN 閘道允許 TCP 和 UDP 連接。  選取 [TCP] 或 [UDP] 設定，選擇從電腦連線時所要使用的通訊協定。

設定的用戶端子網用於 TCP 和 UDP 用戶端。  CIDR 首碼會分成兩個子網，一個用於 TCP，另一個用於 UDP 用戶端。 根據將同時連接的 VPN 使用者數目，選擇首碼遮罩。  

下表列出前置詞遮罩的並行用戶端連接數目。

| 首碼遮罩 | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| 並行 TCP 連線數目 | 124 | 60 | 28 | 12 | 4 |
| 同時 UDP 連線數 | 124 | 60 | 28 | 12 | 4 |

若要使用點對站 VPN 進行連線，請參閱[使用點對站 Vpn 連接到 CloudSimple](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)。
