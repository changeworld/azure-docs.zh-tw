---
title: 快速入門 - 在私有雲上創建 Azure VMware VM - 雲簡單創建 Azure VMware 解決方案
description: 描述如何在雲簡單私有雲上創建 Azure VMware VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566143"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在私有雲上創建 VMware 虛擬機器

要在私有雲上創建虛擬機器，請首先從 Azure 門戶訪問雲簡單門戶。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選擇**所有服務**。
2. 搜索**雲簡單服務**。
3. 選擇要在其中創建私有雲的雲簡單服務。
4. 在 **"概述"** 頁上，按一下"**轉到雲簡單"門戶**可打開 CloudSimple 門戶的新瀏覽器選項卡。  如果出現提示，請使用 Azure 登錄憑據登錄。  

    ![啟動雲簡單門戶](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>啟動 vCenter Web-ui

您現在可以啟動 vCenter 來設置虛擬機器和策略。

要訪問 vCenter，從雲簡單門戶開始。 在主頁上，在 **"常見任務**"下，按一下 **"啟動 vSphere 用戶端**"。  選擇私有雲，然後按一下在私有雲上**啟動 vSphere 用戶端**。

   ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上傳 ISO 或 vSphere 範本

  > [!WARNING]
  > 對於 ISO 上傳，請使用 vSphere HTML5 用戶端。  使用 Flash 用戶端可能會導致錯誤。

1. 獲取要上載到 vCenter 以創建 VM 並在本地系統上可用的 ISO 或 vSphere 範本。
2. 在 vCenter 中，按一下 **"磁片"** 圖示並選擇 **"vsan Datastore**"。 按一下 **"檔**"，然後按一下 **"新建資料夾**"。
    ![vCenter ISO](media/vciso00.png)

3. 創建名為"IsO 和範本"的資料夾。

4. 導航到 ISO 和範本中的 ISO 資料夾，然後按一下 **"上傳檔**"。 按照螢幕上的說明上傳 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中創建虛擬機器

1. 在 vCenter 中，按一下 **"主機和群集"** 圖示。

2. 按右鍵 **"工作負載**"並選擇 **"新虛擬機器**"。
    ![新的 VM](media/vcvm01.png)

3. 選擇 **"創建新虛擬機器**"，然後按一下"**下一步**"。
    ![新的 VM](media/vcvm02.png)

4. 命名電腦，選擇工作負載**VM**的位置，然後按一下 **"下一步**"。
    ![新的 VM](media/vcvm03.png)

5. 選擇 **"工作負載**計算資源"，然後按一下 **"下一步**"。
    ![新的 VM](media/vcvm04.png)

6. 選擇**vsan Datastore，** 然後按一下 **"下一步**"。
    ![新的 VM](media/vcvm05.png)

7. 保留預設 ESXi 6.5 相容性選擇，然後按一下 **"下一步**"。
    ![新的 VM](media/vcvm06.png)

8. 為要創建的 VM 選擇 ISO 的客體作業系統，然後按一下"**下一步**"。
    ![新的 VM](media/vcvm07.png)

9. 選擇硬碟和網路選項。 對於新的 CD/DVD 光碟機，請選擇**資料存儲 ISO 檔**。  如果要允許流量從公共 IP 位址到此 VM，請選擇網路為**vm-1**。
    ![新的 VM](media/vcvm08.png)

10. 將打開一個選擇視窗。 選擇您以前上載到 ISO 和範本資料夾的檔，然後按一下"**確定**"。
    ![新的 VM](media/vcvm10.png)

11. 查看設置並按一下 **"確定"** 以創建 VM。
    ![新的 VM](media/vcvm11.png)

VM 現已添加到工作負載計算資源中，並可供使用。 
![新的 VM](media/vcvm12.png)

基本設置現已完成。 您可以開始使用私有雲，類似于使用本地 VM 基礎結構的方式。

以下各節包含有關為私有雲工作負載設置 DNS 和 DHCP 伺服器以及修改預設網路配置的可選資訊。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>將使用者和標識源添加到 vCenter（可選）

CloudSimple 分配了一個預設 vCenter`cloudowner@cloudsimple.local`使用者帳戶，該使用者帳戶具有使用者名 。 無需設置其他帳戶即可開始。  CloudSimple 通常會為管理員分配執行正常操作所需的許可權。  將本地活動目錄或 Azure AD 設置為私有雲上[的其他標識源](set-vcenter-identity.md)。

## <a name="create-a-dns-and-dhcp-server-optional"></a>創建 DNS 和 DHCP 伺服器（可選）

在私有雲環境中運行的應用程式和工作負載需要名稱解析和 DHCP 服務才能進行查找和 IP 位址分配。 需要適當的 DHCP 和 DNS 基礎結構來提供這些服務。 您可以在 vCenter 中配置虛擬機器，以在私有雲環境中提供這些服務。

Prerequisites

* 配置了 VLAN 的分散式埠組

* 將設置路由到本地或基於 Internet 的 DNS 伺服器

* 虛擬機器範本或 ISO 以創建虛擬機器

以下連結提供有關在 Linux 和 Windows 上設置 DHCP 和 DNS 伺服器的指導。

#### <a name="linux-based-dns-server-setup"></a>基於 Linux 的 DNS 伺服器設置

Linux 提供了用於設置 DNS 伺服器的各種套裝軟體。  下面是設置開源 BIND DNS 伺服器的說明的連結。

[範例設定](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>基於 Windows 的設置

這些 Microsoft 主題描述了如何將 Windows 伺服器設置為 DNS 伺服器和 DHCP 伺服器。

[視窗伺服器作為 DNS 伺服器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[視窗伺服器為 DHCP 伺服器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自訂網路設定（可選）

CloudSimple 門戶中的"網路"頁面允許您為 VM 指定防火牆表和公共 IP 位址的配置。

### <a name="allocate-public-ips"></a>分配公共 IP

1. 在雲簡單門戶中導航到**網路>公共 IP。**
2. 按一下 **"分配公共 IP**"。
3. 輸入名稱以標識 IP 位址條目。
4. 保留預設位置。
5. 如果需要，使用滑塊更改空閒超時。
6. 輸入要為其分配公共 IP 位址的本地 IP 位址。
7. 如果需要，輸入關聯的 DNS 名稱。
8. 按一下 [完成]****。

    ![公用 IP](media/quick-create-pc-public-ip.png)

分配公共 IP 位址的任務開始。 您可以在"**活動>任務**"頁上檢查任務的狀態。 分配完成後，新條目將顯示在公共 IP 頁上。

必須映射此 IP 位址的 VM 需要使用上面指定的本地位址進行配置。 配置 IP 位址的過程特定于 VM 作業系統。 有關正確的過程，請參閱 VM 作業系統的文檔。

#### <a name="example"></a>範例

例如，以下是 Ubuntu 16.04 的詳細資訊。

將靜態方法添加到檔 /etc/網路/介面中的 inet 位址系列配置。 更改位址、網碼和閘道值。 在此示例中，我們使用 eth0 介面、內部 IP 位址 192.168.24.10、閘道位址 192.168.24.1 和 netmask 255.255.0。 對於您的環境，歡迎電子郵件中提供了可用的子網資訊。

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
手動禁用介面。

```
sudo ifdown eth0
```
再次手動啟用介面。

```
sudo ifup eth0
```

預設情況下，所有來自 Internet 的傳入流量都**被拒絕**。 如果要打開任何其他埠，請創建[防火牆表](firewall.md)。

將內部 IP 位址配置為靜態 IP 位址後，請驗證是否可以從 VM 內到達 Internet。

```
ping 8.8.8.8
```
還要驗證您是否可以使用公共 IP 位址從 Internet 到達 VM。

確保 VM 上的任何 ip 可規則不會阻止埠 80 入站。
        
```
netstat -an | grep 80
```

啟動偵聽埠 80 的 HTTP 伺服器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```
在桌面上啟動瀏覽器，並將其指向埠 80，用於公共 IP 位址以流覽 VM 上的檔。

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>公共 IP 的預設雲簡單防火牆規則

* VPN 流量：允許 VPN 與所有工作負載網路和管理網路之間的所有流量。
* 私有雲內部流量：允許工作負載網路和管理網路之間的所有東西部流量（從/到）（如上所示）。
* 互聯網流量：
  * 從 Internet 傳入的所有流量都被拒絕到工作負載網路和管理網路。
  * 允許從工作負載網路或管理網路向 Internet 輸出所有流量。

您還可以使用防火牆規則功能修改流量的安全方式。 有關詳細資訊，請參閱[設置防火牆表和規則](firewall.md)。

## <a name="install-solutions-optional"></a>安裝解決方案（可選）

您可以在雲簡單私有雲上安裝解決方案，以充分利用私有雲 vCenter 環境。 您可以設置備份、災害復原、複製和其他功能來保護虛擬機器。 示例包括 VMware 網站恢復管理器 （VMware SRM） 和 Veeam 備份&複製。

要安裝解決方案，您必須在有限的時間內請求其他許可權。 請參閱[升級許可權](escalate-private-cloud-privileges.md)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure 快速路由連接到本地網路](on-premises-connection.md)
* [在雲簡單網路上設置 VPN 閘道](vpn-gateway.md)
