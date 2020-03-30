---
title: Azure VMware 解決方案（按雲簡單 ） - 在本地雲和私有雲之間配置 VPN
description: 描述如何配置本地網路和雲簡單私有雲之間的網站到網站或點對點 VPN 連接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087136"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>配置與雲簡單私有雲的 VPN 連接

VPN 閘道允許您從本地網路和用戶端電腦遠端連線到 CloudSimple 網路。  在本文中，您可以找到有關從 CloudSimple 門戶設置 VPN 閘道的資訊。  本地網路和 CloudSimple 網路之間的 VPN 連接提供對私有雲上的 vCenter 和工作負載的訪問。 CloudSimple 同時支援點對點 VPN 和網站到網站 VPN 閘道。

## <a name="vpn-gateway-types"></a>VPN 閘道類型

* **點對點 VPN**連接是從電腦連接到私有雲的最簡單方法。 使用點對點 VPN 連接遠端連線到私有雲。
* **網站到網站 VPN**連接允許您設置私有雲工作負載以訪問本機服務。 您還可以使用本地活動目錄作為標識源，以驗證您的私有雲 vCenter。  目前，支援**基於策略的 VPN**類型。

在區域中，您可以創建一個網站到網站 VPN 閘道和一個點對點 VPN 閘道。

## <a name="point-to-site-vpn"></a>點對站 VPN

要創建指向網站的 VPN 閘道，請參閱[創建指向網站 VPN 閘道](vpn-gateway.md#create-point-to-site-vpn-gateway)。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用點對點 VPN 輕鬆連接到雲

VPN 用戶端需要從您的電腦連接到雲簡單。  下載適用于 MacOS 和 OS X 的 Windows 或[粘度](https://www.sparklabs.com/viscosity/download/)的[OpenVPN 用戶端](https://openvpn.net/community-downloads/)。

1. 啟動雲簡單門戶並選擇**網路**。
2. 選擇**VPN 閘道**。
3. 從 VPN 閘道清單中，按一下指向網站 VPN 閘道。
4. 選擇**使用者**。
5. 點擊**下載我的 VPN 配置**

    ![下載 VPN 組態](media/download-p2s-vpn-configuration.png)

6. 在 VPN 用戶端上導入配置

    * 在[Windows 用戶端上導入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的說明
    * 有關在[macOS 或 OS X 上導入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的說明

7. 連接到雲簡單 VPN 閘道。

下面的示例顯示了使用**粘度用戶端**導入連接。

#### <a name="import-connection-on-viscosity-client"></a>粘度用戶端上的導入連接

1. 從下載的 .ZIP 檔案中提取 VPN 配置的內容。

2. 打開電腦上的粘度。

3. 按一下該**+** 圖示並選擇 **"從檔****導入連接** > "。

    ![從檔導入 VPN 配置](media/import-p2s-vpn-config.png)

4. 為要使用的協定選擇 OpenVPN 設定檔 （.ovpn），然後按一下"**打開**"。

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

連接現在顯示在"粘度"功能表中。

#### <a name="connect-to-the-vpn"></a>連線至 VPN

要使用粘度 OpenVPN 用戶端連接到 VPN，請從功能表中選擇連接。 功能表圖示將更新以指示已建立連接。

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>連接到多個私有雲

點對點 VPN 連接解析您創建的第一個私有雲的 DNS 名稱。 當您要訪問其他私有雲時，必須更新 VPN 用戶端上的 DNS 伺服器。

1. 啟動[雲簡單門戶](access-cloudsimple-portal.md)。

2. 導航到**資源** > **私有雲**，然後選擇要連接到的私有雲。

3. 在私有雲的**摘要**頁面上，在 **"基本資訊**"下複製私有雲 DNS 伺服器 IP 位址。

    ![私有雲 DNS 伺服器](media/private-cloud-dns-server.png)

4. 按右鍵電腦系統託盤中的粘度圖示，然後選擇 **"首選項**"。

    ![VPN](media/vis00.png)

5. 選擇雲簡單 VPN 連接。

    ![VPN 連線](media/viscosity-client.png)

6. 按一下 **"編輯"** 可更改連接屬性。

    ![編輯 VPN 連接](media/viscosity-edit-connection.png)

7. 按一下 **"網路**"選項卡並輸入私有雲 DNS 伺服器 IP 位址，該位址由逗號或空格```cloudsimple.io```分隔，域為 。  選擇 **"忽略 VPN 伺服器發送的 DNS 設置**"。

    ![VPN 網路](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 要連接到第一個私有雲，請刪除這些設置並連接到 VPN 伺服器。

## <a name="site-to-site-vpn"></a>站對站 VPN

要創建網站到網站 VPN 閘道，請參閱[創建網站到網站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。  從本地網路到私有雲的網站到網站 VPN 連接提供了這些好處。  

* 從本地網路中的任何工作站訪問私有雲 vCenter
* 將本地活動目錄用作 vCenter 標識源
* 方便地將 VM 範本、ISO 和其他檔從本地資源傳輸到私有雲 vCenter
* 從本地網路訪問在私有雲上運行的工作負載

要在高可用性模式下設置本地 VPN 閘道，請參閱[配置高可用性 VPN 連接](high-availability-vpn-connection.md)。

> [!IMPORTANT]
>    1. 在 VPN 設備上將 TCP MSS 夾緊設置為 1200。 或者，如果您的 VPN 設備不支援 MSS 夾緊，則可以將隧道介面上的 MTU 設置為 1240 位元組。
> 2. 佈建網站到網站 VPN 後，將 _.cloudsimple.io 的 DNS 請求轉發到私有雲 DNS 伺服器。  按照本地 DNS[設置](on-premises-dns-setup.md)中的說明操作。
