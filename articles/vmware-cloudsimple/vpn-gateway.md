---
title: Azure VMware 解決方案（按雲簡單 ） - 設置 VPN 閘道
description: 描述如何設置點對點 VPN 閘道和網站到網站 VPN 閘道，並在本地網路和雲簡單私有雲之間創建連接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279490"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>在雲簡單網路上設置 VPN 閘道

VPN 閘道允許您從本地網路和用戶端電腦遠端連線到 CloudSimple 網路。 本地網路和 CloudSimple 網路之間的 VPN 連接提供對私有雲上的 vCenter 和工作負載的訪問。 CloudSimple 支援網站到網站 VPN 和點對點 VPN 閘道。

## <a name="vpn-gateway-types"></a>VPN 閘道類型

* **網站到網站 VPN**連接允許您設置私有雲工作負載以訪問本機服務。 您還可以使用本地活動目錄作為標識源，以驗證您的私有雲 vCenter。  目前，僅支援**基於策略的 VPN**類型。
* **點對點 VPN**連接是從電腦連接到私有雲的最簡單方法。 使用點對點 VPN 連接遠端連線到私有雲。 有關為點對點 VPN 連接安裝用戶端的資訊，請參閱[配置到私有雲的 VPN 連接](set-up-vpn.md)。

在區域中，您可以創建一個點對點 VPN 閘道和一個網站到網站 VPN 閘道。

## <a name="automatic-addition-of-vlansubnets"></a>自動添加 VLAN/子網

CloudSimple VPN 閘道提供了將 VLAN/子網添加到 VPN 閘道的策略。  策略允許您為管理 VLAN/子網和使用者定義的 VLAN/子網指定不同的規則。  管理 VLAN/子網的規則適用于您創建的任何新私有雲。  使用者定義的 VLAN/子網規則允許您自動將任何新的 VLAN/子網添加到現有或新的私有雲中，用於網站到網站 VPN 閘道，您可以為每個連接定義策略。

將 VLAN/子網添加到 VPN 閘道的策略適用于網站到網站 VPN 和點對點 VPN 閘道。

## <a name="automatic-addition-of-users"></a>自動添加使用者

指向網站的 VPN 閘道允許您為新使用者定義自動添加策略。 預設情況下，訂閱的所有擁有者和參與者都有權訪問 CloudSimple 門戶。  僅當初次開機雲簡單門戶時，才會創建使用者。  選擇 **"自動添加**規則"使任何新使用者都可以使用點對點 VPN 連接訪問 CloudSimple 網路。

## <a name="set-up-a-site-to-site-vpn-gateway"></a>佈建網站到網站 VPN 閘道

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)並選擇 **"網路**"。
2. 選擇**VPN 閘道**。
3. 按一下 **"新 VPN 閘道**"。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 對於**閘道配置**，請指定以下設置，然後按一下 **"下一步**"。

    * 選擇**網站到網站 VPN**作為閘道類型。
    * 輸入名稱以標識閘道。
    * 選擇部署雲簡單服務的 Azure 位置。
    * 或者，啟用高可用性。

    ![創建網站到網站 VPN 閘道](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 啟用高可用性需要本地 VPN 設備支援連接到兩個 IP 位址。 部署 VPN 閘道後，無法禁用此選項。

5. 從本地網路創建第一個連接，然後按一下 **"下一步**"。

    * 輸入名稱以標識連接。
    * 對於對等 IP，請輸入本地 VPN 閘道的公共 IP 位址。
    * 輸入本地 VPN 閘道的對等識別碼。  對等識別碼通常是本地 VPN 閘道的公共 IP 位址。  如果您在閘道上配置了特定識別碼，請輸入該識別碼。
    * 複製共用金鑰以從本地 VPN 閘道進行連接。  要更改預設共用金鑰並指定新金鑰，請按一下編輯圖示。
    * 對於**本地首碼**，輸入將訪問 CloudSimple 網路的本地 CIDR 首碼。  創建連接時，可以添加多個 CIDR 首碼。

    ![創建網站到網站 VPN 閘道連接](media/create-vpn-gateway-s2s-connection.png)

6. 在私有雲網路上啟用將從本地網路訪問的 VLAN/子網，然後按一下"**下一步**"。

    * 要添加管理 VLAN/子網，啟用**添加私有雲的 VLAN/子網**。  vMotion 和 vSAN 子網需要管理子網。
    * 要添加 vMotion 子網，啟用**私有雲的添加 vMotion 網路**。
    * 要添加 vSAN 子網，啟用**私有雲的添加 vSAN 子網**。
    * 選擇或取消選擇特定的 VLAN。

    ![建立連線](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 查看設置並按一下"**提交**"。

    ![網站到網站 VPN 閘道審查和創建](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>創建點對點 VPN 閘道

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)並選擇 **"網路**"。
2. 選擇**VPN 閘道**。
3. 按一下 **"新 VPN 閘道**"。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 對於**閘道配置**，請指定以下設置，然後按一下 **"下一步**"。

    * 選擇**指向網站 VPN**作為閘道類型。
    * 輸入名稱以標識閘道。
    * 選擇部署雲簡單服務的 Azure 位置。
    * 指定指向網站閘道的用戶端子網。  連接時，將從用戶端子網提供 DHCP 位址。

5. 對於**連接/使用者**，指定以下設置，然後按一下 **"下一步**"。

    * 要自動允許所有當前和未來的使用者通過指向網站閘道訪問私有雲，請選擇"**自動添加所有使用者**"。 選擇該選項時，將自動選擇使用者清單中的所有使用者。 您可以通過取消清單中的單個使用者來覆蓋自動選項。
    * 要選擇單個使用者，請按一下使用者清單中的核取方塊。

6. VLAN/子網部分允許您為閘道和連接指定管理和使用者 VLAN/子網。

    * **"自動添加**"選項為閘道設置全域原則。 這些設置適用于當前閘道。 可以在 **"選擇"** 區域中覆蓋這些設置。
    * 選擇**添加私有雲的管理 VLAN/子網**。 
    * 要添加所有使用者定義的 VLAN/子網，請按一下"**添加使用者定義的 VLAN/子網**"。
    * "**選擇**"設置將覆蓋 **"自動添加**"下的全域設置。

7. 按一下 **"下一步**"查看設置。 按一下"編輯"圖示可進行任何更改。
8. 按一下"**創建**"以創建 VPN 閘道。

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>點對點 VPN 閘道的用戶端子網和協定

點對點 VPN 閘道允許 TCP 和 UDP 連接。  通過選擇 TCP 或 UDP 配置，選擇從電腦連接時要使用的協定。

配置的用戶端子網用於 TCP 和 UDP 用戶端。  CIDR 首碼分為兩個子網，一個用於 TCP，一個用於 UDP 用戶端。 根據將同時連接的 VPN 使用者數選擇首碼遮罩。  

下表列出了首碼遮罩的併發用戶端連接數。

| 首碼遮罩 | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| 併發 TCP 連接數 | 124 | 60 | 28 | 12 | 4 |
| 併發 UDP 連接數 | 124 | 60 | 28 | 12 | 4 |

要使用點對點 VPN 進行連接，請參閱[使用點對點 VPN 輕鬆連接到雲](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)。
