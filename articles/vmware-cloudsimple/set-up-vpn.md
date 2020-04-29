---
title: Azure VMware Solution by CloudSimple-設定內部部署與私人雲端之間的 VPN
description: 說明如何設定內部部署網路與 CloudSimple 私人雲端之間的站對站或點對站 VPN 連線
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77087136"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>設定 CloudSimple 私人雲端的 VPN 連線

VPN 閘道可讓您從內部部署網路和從遠端用戶端電腦連線到 CloudSimple 網路。  在本文中，您可以從 CloudSimple 入口網站找到設定 VPN 閘道的相關資訊。  內部部署網路和 CloudSimple 網路之間的 VPN 連線可讓您存取私人雲端上的 vCenter 和工作負載。 CloudSimple 支援點對站 VPN 和站對站 VPN 閘道。

## <a name="vpn-gateway-types"></a>VPN 閘道類型

* **點對站 VPN**連線是從電腦連線到私人雲端最簡單的方式。 使用點對站 VPN 連線能力，從遠端連線到私人雲端。
* **站對站 VPN**連線可讓您設定私人雲端工作負載，以存取內部部署服務。 您也可以使用內部部署 Active Directory 做為身分識別來源，以向您的私用雲端 vCenter 進行驗證。  目前支援以**原則為基礎的 VPN**類型。

在區域中，您可以建立一個站對站 VPN 閘道和一個點對站 VPN 閘道。

## <a name="point-to-site-vpn"></a>點對站 VPN

若要建立點對站 VPN 閘道，請參閱[建立點對站 vpn 閘道](vpn-gateway.md#create-point-to-site-vpn-gateway)。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用點對站 VPN 連接到 CloudSimple

需要 VPN 用戶端，才能從您的電腦連接到 CloudSimple。  下載[OpenVPN client](https://openvpn.net/community-downloads/) for Windows 或[Viscosity](https://www.sparklabs.com/viscosity/download/) For macOS and OS X。

1. 啟動 CloudSimple 入口網站，然後選取 [**網路**]。
2. 選取 [ **VPN 閘道**]。
3. 從 VPN 閘道清單中，按一下 [點對站 VPN 閘道]。
4. 選取 [**使用者**]。
5. 按一下 [**下載我的 VPN**設定]

    ![下載 VPN 組態](media/download-p2s-vpn-configuration.png)

6. 在您的 VPN 用戶端上匯入設定

    * [在 Windows 用戶端上匯入](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)設定的指示
    * [在 macOS 或 OS X 上匯入](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)設定的指示

7. 連接到 CloudSimple VPN 閘道。

以下範例顯示如何使用**Viscosity 用戶端**來匯入連接。

#### <a name="import-connection-on-viscosity-client"></a>在 Viscosity 用戶端上匯入連線

1. 從已下載的 .zip 檔案中，將 VPN 設定的內容解壓縮。

2. 在您的電腦上開啟 Viscosity。

3. 按一下**+** 圖示，然後選取 [**從**檔案匯**入連接** > ]。

    ![從檔案匯入 VPN 設定](media/import-p2s-vpn-config.png)

4. 針對您要使用的通訊協定選取 OpenVPN 設定檔（. vpnconfig.ovpn），然後按一下 [**開啟**]。

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

連接現在會出現在 [Viscosity] 功能表中。

#### <a name="connect-to-the-vpn"></a>連線至 VPN

若要使用 Viscosity OpenVPN 用戶端連線到 VPN，請從功能表中選取 [連接]。 功能表圖示會更新以指出已建立連接。

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>連接到多個私人雲端

點對站 VPN 連線會解析您所建立的第一個私人雲端的 DNS 名稱。 當您想要存取其他私人雲端時，您必須更新 VPN 用戶端上的 DNS 伺服器。

1. 啟動[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 流覽至 [**資源** > ] [**私人**雲端]，然後選取您想要連線的私人雲端。

3. 在私人雲端的 [**摘要**] 頁面上，複製 [**基本資訊**] 底下的 [私人雲端 DNS 伺服器 IP 位址]。

    ![私人雲端 DNS 伺服器](media/private-cloud-dns-server.png)

4. 以滑鼠右鍵按一下電腦系統匣中的 Viscosity 圖示，然後選取 [**喜好**設定]。

    ![VPN](media/vis00.png)

5. 選取 CloudSimple VPN 連線。

    ![VPN 連線](media/viscosity-client.png)

6. 按一下 [**編輯**] 以變更連接屬性。

    ![編輯 VPN 連線](media/viscosity-edit-connection.png)

7. 按一下 [**網路**] 索引標籤，並輸入以逗號或空格分隔的私人雲端 DNS 伺服器 IP 位址， ```cloudsimple.io```以及網域。  選取 [**略過 VPN 伺服器傳送的 DNS 設定**]。

    ![VPN 網路功能](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 若要連線到您的第一個私人雲端，請移除這些設定並聯機至 VPN 伺服器。

## <a name="site-to-site-vpn"></a>站對站 VPN

若要建立站對站 VPN 閘道，請參閱[建立站對站 vpn 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。  從內部部署網路到私人雲端的站對站 VPN 連線會提供這些優點。  

* 您的私人雲端 vCenter 從內部部署網路中的任何工作站存取
* 使用內部部署 Active Directory 作為 vCenter 身分識別來源
* 方便將 VM 範本、Iso 和其他檔案從您的內部部署資源傳輸到您的私用雲端 vCenter
* 從您的內部部署網路在私人雲端上執行之工作負載的協助工具

若要在高可用性模式中設定您的內部部署 VPN 閘道，請參閱設定[高可用性 vpn](high-availability-vpn-connection.md)連線。

> [!IMPORTANT]
>    1. 在 VPN 裝置上，將 TCP MSS 固定設定為1200。 或者，如果您的 VPN 裝置不支援 MSS 固定，您也可以改為將通道介面上的 MTU 設定為1240個位元組。
> 2. 設定站對站 VPN 之後，請將 *. cloudsimple.io 的 DNS 要求轉送到私人雲端 DNS 伺服器。  依照內部[部署 DNS 設定](on-premises-dns-setup.md)中的指示進行。
