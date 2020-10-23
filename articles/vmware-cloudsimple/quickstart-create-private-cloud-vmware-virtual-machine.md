---
title: 快速入門-在私人雲端上建立 Azure VMware VM-Azure VMware Solution by CloudSimple
description: 瞭解如何在 CloudSimple 私用雲端上建立 VMware 虛擬機器。 從 Azure 入口網站存取 CloudSimple 入口網站。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fd67a5a7f1bf665333e06c66f73c7f18727a3e12
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427764"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在您的私人雲端上建立 VMware 虛擬機器

若要在您的私人雲端上建立虛擬機器，請從 Azure 入口網站存取 CloudSimple 入口網站開始。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選取 [所有服務]  。
2. 搜尋 **CloudSimple 服務**。
3. 選取您要在其上建立私人雲端的 CloudSimple 服務。
4. 在 [ **總覽** ] 頁面中，按一下 [ **移至 CloudSimple 入口網站** ] 以開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  如果出現提示，請使用您的 Azure 登入認證登入。  

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>啟動 vCenter web ui

您現在可以啟動 vCenter 來設定虛擬機器和原則。

若要存取 vCenter，請從 CloudSimple 入口網站開始。 在首頁的 [ **一般**工作] 底下，按一下 [ **啟動 vSphere 用戶端**]。  選取私人雲端，然後按一下 [在私人雲端上 **啟動 VSphere 用戶端** ]。

   ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上傳 ISO 或 vSphere 範本

  > [!WARNING]
  > 針對 ISO 上傳，請使用 vSphere HTML5 用戶端。  使用 Flash 用戶端可能會導致錯誤。

1. 取得您想要上傳至 vCenter 的 ISO 或 vSphere 範本以建立 VM，並讓它可在您的本機系統上使用。
2. 在 vCenter 中，按一下 **磁片** 圖示，然後選取 [ **vsanDatastore**]。 按一下 [檔案 **]，然後按一下 [** **新增資料夾**]。
    ![vCenter ISO](media/vciso00.png)

3. 建立標題為「Iso 和範本」的資料夾。

4. 流覽至 [Iso 和範本] 中的 [Iso] 資料夾，然後按一下 **[上傳**檔案]。 依照畫面上的指示上傳 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中建立虛擬機器

1. 在 vCenter 中，按一下 [ **主機和** 叢集] 圖示。

2. 以滑鼠右鍵按一下 [ **工作負載** ]，然後選取 [ **新增虛擬機器**]。
    ![反白顯示 [新增虛擬機器] 功能表選項的螢幕擷取畫面。](media/vcvm01.png)

3. 選取 [ **建立新的虛擬機器** ]，然後按 **[下一步]**。
    ![醒目顯示 [建立新的虛擬機器] 選項的螢幕擷取畫面。](media/vcvm02.png)

4. 為電腦命名、選取 **工作負載 VM 的** 位置，然後按 **[下一步]**。
    ![醒目顯示 [工作負載 Vm] 選項的螢幕擷取畫面。](media/vcvm03.png)

5. 選取 **工作負載** 計算資源，然後按一下 **[下一步]**。
    ![強調工作負載計算資源的螢幕擷取畫面。](media/vcvm04.png)

6. 選取 **vsanDatastore** ，然後按 **[下一步]**。
    ![醒目顯示 vsanDatastore 選項的螢幕擷取畫面。](media/vcvm05.png)

7. 保留預設的 ESXi 6.5 相容性選取專案，然後按 **[下一步]**。
    ![顯示所選 [ESXi 6.5 相容性] 選項的螢幕擷取畫面。](media/vcvm06.png)

8. 針對您要建立的 VM 選取 ISO 的來賓 OS，然後按 **[下一步]**。
    ![顯示如何為 VM 選取 ISO guese OS 的螢幕擷取畫面。](media/vcvm07.png)

9. 選取 [硬碟] 和 [網路選項]。 若為新的 CD/DVD 磁片磁碟機，請選取 [資料存放區 **ISO**檔案]。  如果您想要允許從公用 IP 位址到此 VM 的流量，請選取網路作為 **vm-1**。
    ![醒目顯示您選取資料存放區 ISO 檔案之位置的螢幕擷取畫面。](media/vcvm08.png)

10. [選取範圍] 視窗隨即開啟。 選取您先前上傳至 Iso 和 Templates 資料夾的檔案，然後按一下 **[確定]**。
    ![新的 VM](media/vcvm10.png)

11. 檢查設定，然後按一下 **[確定]** 以建立 VM。
    ![顯示您審核設定的螢幕擷取畫面。](media/vcvm11.png)

VM 現在已新增至工作負載計算資源，並已可供使用。 
![螢幕擷取畫面，顯示已新增至工作負載計算資源的 VM。](media/vcvm12.png)

基本設定現已完成。 您可以開始使用您的私人雲端，類似于您使用內部部署 VM 基礎結構的方式。

下列各節包含針對私人雲端工作負載設定 DNS 和 DHCP 伺服器，以及修改預設網路設定的選擇性資訊。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>將使用者和身分識別來源新增至 vCenter (選擇性) 

CloudSimple 會指派具有使用者名稱的預設 vCenter 使用者帳戶 `cloudowner@cloudsimple.local` 。 不需要額外的帳戶設定，就可以開始使用。  CloudSimple 通常會將執行正常作業所需的許可權指派給系統管理員。  將您的內部部署 active directory 或 Azure AD 設定為私用雲端上的 [其他身分識別來源](set-vcenter-identity.md) 。

## <a name="create-a-dns-and-dhcp-server-optional"></a>建立 DNS 和 DHCP 伺服器 (選用) 

在私用雲端環境中執行的應用程式和工作負載需要名稱解析和 DHCP 服務，以便進行查閱和 IP 位址指派。 必須要有適當的 DHCP 和 DNS 基礎結構，才能提供這些服務。 您可以在 vCenter 中設定虛擬機器，以在您的私人雲端環境中提供這些服務。

先決條件

* 已設定 VLAN 的分散式埠群組

* 將設定路由傳送至內部部署或以網際網路為基礎的 DNS 伺服器

* 用來建立虛擬機器的虛擬機器範本或 ISO

下列連結提供在 Linux 和 Windows 上設定 DHCP 和 DNS 伺服器的指引。

#### <a name="linux-based-dns-server-setup"></a>以 Linux 為基礎的 DNS 伺服器設定

Linux 提供各種套件來設定 DNS 伺服器。  以下是設定開放原始碼 BIND DNS 伺服器的指示連結。

[範例設定](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>以 Windows 為基礎的安裝程式

這些 Microsoft 主題說明如何將 Windows 伺服器設定為 DNS 伺服器和 DHCP 伺服器。

[作為 DNS 伺服器的 Windows Server](/windows-server/networking/dns/dns-top)

[Windows Server 做為 DHCP 伺服器](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自訂網路設定 (選用) 

CloudSimple 入口網站中的 [網路] 頁面可讓您指定防火牆資料表的設定，以及 Vm 的公用 IP 位址。

### <a name="allocate-public-ips"></a>配置公用 Ip

1. 在 CloudSimple 入口網站中，流覽至 **網路 > 公用 IP** 。
2. 按一下 [ **配置公用 IP**]。
3. 輸入名稱以識別 IP 位址專案。
4. 保留預設位置。
5. 如有需要，請使用滑杆來變更閒置的超時時間。
6. 輸入您想要為其指派公用 IP 位址的本機 IP 位址。
7. 如有需要，請輸入相關聯的 DNS 名稱。
8. 按一下 [完成]  。

    ![公用 IP](media/quick-create-pc-public-ip.png)

開始配置公用 IP 位址的工作。 您可以在 [ **活動 >** 工作] 頁面上檢查工作的狀態。 配置完成時，新的專案會顯示在 [公用 Ip] 頁面上。

必須以上述指定的本機位址設定此 IP 位址必須對應的 VM。 設定 IP 位址的程式是 VM 作業系統專用的。 如需正確的程式，請參閱您 VM 作業系統的檔。

#### <a name="example"></a>範例

例如，以下是 Ubuntu 16.04 的詳細資料。

將靜態方法新增至/etc/network/interfaces. 檔案中的 inet 位址系列設定 變更位址、網路遮罩和閘道值。 在此範例中，我們使用的是 eth0 介面、內部 IP 位址192.168.24.10、閘道位址192.168.24.1 和網路遮罩255.255.255.0。 針對您的環境，歡迎電子郵件中提供可用的子網資訊。

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
手動停用介面。

```
sudo ifdown eth0
```
再次手動啟用介面。

```
sudo ifup eth0
```

根據預設，所有來自網際網路的連入流量都會 **遭到拒絕**。 如果您想要開啟任何其他埠，請建立 [防火牆資料表](firewall.md)。

將內部 IP 位址設定為靜態 IP 位址之後，請確認您可以從 VM 內連接網際網路。

```
ping 8.8.8.8
```
也請確認您可以使用公用 IP 位址從網際網路連線至 VM。

確定 VM 上的任何 iptable 規則都不會封鎖埠80輸入。
        
```
netstat -an | grep 80
```

啟動在埠80上接聽的 HTTP 伺服器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```
在桌面上啟動瀏覽器，並將其指向公用 IP 位址的埠80，以流覽 VM 上的檔案。

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>公用 IP 的預設 CloudSimple 防火牆規則

* VPN 流量：允許 (之間的所有流量) VPN，以及所有的工作負載網路和管理網路。
* 私用雲端內部流量：允許 (之間的所有東部西部流量) 工作負載網路，以及) 所顯示的管理網路 (。
* 網際網路流量：
  * 所有來自網際網路的連入流量都會拒絕工作負載網路和管理網路。
  * 允許從工作負載網路或管理網路到網際網路的所有連出流量。

您也可以使用防火牆規則功能，修改流量的安全方式。 如需詳細資訊，請參閱 [設定防火牆資料表和規則](firewall.md)。

## <a name="install-solutions-optional"></a> (選用) 安裝解決方案

您可以在 CloudSimple 私用雲端上安裝解決方案，以充分利用您的私用雲端 vCenter 環境。 您可以設定備份、嚴重損壞修復、複寫和其他功能來保護您的虛擬機器。 範例包括 VMware Site Recovery Manager (VMware SRM) 和 Veeam 備份 & 複寫。

若要安裝解決方案，您必須在一段有限期間內要求額外的許可權。 請參閱 [提升許可權](escalate-private-cloud-privileges.md)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 連接到內部部署網路](on-premises-connection.md)
* [設定 CloudSimple 網路上的 VPN 閘道](vpn-gateway.md)
