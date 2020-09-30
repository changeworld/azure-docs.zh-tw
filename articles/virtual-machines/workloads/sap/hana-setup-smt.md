---
title: 如何設定 SAP HANA on Azure (大型執行個體) 適用的 SMT 伺服器 | Microsoft Docs
description: 如何設定 SAP HANA on Azure (大型執行個體) 適用的 SMT 伺服器。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c186b73cb00d03b731cd015b3ee06bf8f2233fa4
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541147"
---
# <a name="set-up-smt-server-for-suse-linux"></a>設定適用於 SUSE Linux 的 SMT 伺服器
SAP HANA 大型執行個體無法直接連線到網際網路。 它不是可向作業系統提供者註冊此類單元，並且下載與套用更新的簡單程序。 SUSE Linux 的其中一種解決方案是在 Azure 虛擬機器中設定 SMT 伺服器。 請將虛擬機器裝載於 Azure 虛擬網路中，該網路會連接到 HANA 大型執行個體。 有了這類的 SMT 伺服器，HANA 大型執行個體單位才可能註冊並下載更新。 

如需關於 SUSE 的文件，請參閱其 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) (SLES 12 SP2 訂用帳戶管理工具)。 

若要安裝可完成 HANA 大型執行個體工作的 SMT 伺服器，先決條件是：

- 連接到 HANA 大型執行個體 ExpressRoute 線路的 Azure 虛擬網路。
- 與組織相關聯的 SUSE 帳戶。 組織應該要有有效的 SUSE 訂用帳戶。

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>在 Azure 虛擬機器上安裝 SMT 伺服器

首先，登入 [SUSE 客戶中心](https://scc.suse.com/)。

移至**組織**  >  **組織認證**。 在該區段中，您應該會找到設定 SMT 伺服器所需的認證。

然後，在 Azure 虛擬網路中安裝 SUSE Linux VM。 若要部署虛擬機器，請使用 Azure 的 SLES 12 SP2 資源庫映像 (選取 BYOS SUSE 映像)。 在部署程序中，不要定義 DNS 名稱，也不要使用靜態 IP 位址。

![螢幕擷取畫面：SMT 伺服器的虛擬機器部署](./media/hana-installation/image3_vm_deployment.png)

已部署的虛擬機器較小，會取得 Azure 虛擬網路的內部 IP 位址 10.34.1.4。 虛擬機器的名稱是 smtserver**。 安裝之後，已檢查 HANA 大型執行個體單位的連線。 HANA 大型執行個體單位可能需要以 Azure 虛擬機器的 etc/hosts 設定解析，視您如何安排名稱解析而定。 

將磁碟新增至虛擬機器。 您會使用此磁碟來保存更新，開機磁碟本身可能會太小。 在此，磁碟已掛接到 /srv/www/htdocs，如下列螢幕擷取畫面所示。 100 GB 的磁碟應已足夠。

![螢幕擷取畫面顯示 [PuTTy] 視窗中新增的磁片。](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登入 HANA 大型執行個體單位、維護 /etc/hosts，並檢查是否可以連線到應該要透過網路執行 SMT 伺服器的 Azure 虛擬機器。

完成這項檢查之後，登入應該執行 SMT 伺服器的 Azure 虛擬機器。 如果您要使用 putty 登入虛擬機器，請在 bash 視窗中執行這一系列的命令：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

重新啟動您的 bash 來啟動設定。 然後啟動 YAST。

將 VM (smtserver) 連接至 SUSE 站台。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

虛擬機器連線到 SUSE 站台後，安裝 smt 套件。 使用下列 putty 命令安裝 smt 套件。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


您也可以使用 YAST 工具安裝 smt 套件。 在 YAST 中，移至 [軟體維護]**** 並搜尋 smt。 選取 [smt]****，以自動切換至 yast2-smt。

![螢幕擷取畫面：YAST 中的 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安裝選項。 安裝完成後，移至 [SMT 伺服器組態]。 輸入之前從 SUSE 客戶中心擷取的組織認證。 亦請輸入 Azure 虛擬機器主機名稱當作 SMT 伺服器 URL。 在此示範中，它是 HTTPs： \/ /smtserver。

![螢幕擷取畫面：SMT 伺服器組態](./media/hana-installation/image6_configuration_of_smtserver1.png)

現在，測試 SUSE 客戶中心的連線是否作用。 如您在下列螢幕擷取畫面中所見，此示範案例中的連線可以運作。

![螢幕擷取畫面：測試 SUSE 客戶中心連線](./media/hana-installation/image7_test_connect.png)

SMT 安裝程式啟動後，請提供資料庫密碼。 因為是新的安裝，所以您應該定義該密碼，如下列螢幕擷取畫面所示。

![螢幕擷取畫面：定義資料庫的密碼](./media/hana-installation/image8_define_db_passwd.PNG)

下一個步驟是建立憑證。

![螢幕擷取畫面：建立 SMT 伺服器的憑證](./media/hana-installation/image9_certificate_creation.PNG)

在組態設定結尾，可能需要幾分鐘的時間來執行同步處理檢查。 安裝和設定 SMT 伺服器之後，您應該會在掛接點 /srv/www/htdocs/ 下發現目錄存放庫。 存放庫下面還有一些子目錄。 

使用這些命令重新啟動 SMT 伺服器及其相關服務。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>將套件下載到 SMT 伺服器

重新啟動所有服務之後，使用 YAST 在 SMT 管理中選取適當的套件。 套件選項取決於 HANA 大型執行個體伺服器的作業系統映像。 套件選項不會取決於執行 SMT 伺服器的虛擬機器 SLES 版次或版本。 以下螢幕擷取畫面顯示選取畫面的範例。

![螢幕擷取畫面：選取套件](./media/hana-installation/image10_select_packages.PNG)

接下來，啟動所設定之 SMT 伺服器的選取套件初始複本。 在 shell 中使用命令 smt-mirror 觸發這個複本。

![螢幕擷取畫面：將套件下載到 SMT 伺服器](./media/hana-installation/image11_download_packages.PNG)

套件應該複製到建立在掛接點 /srv/www/htdocs 下的目錄。 視選取的套件數目而定，此程序可能需要一個小時以上的時間。 當此程序完成時，請移至 SMT 用戶端安裝程式。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型執行個體單位上設定 SMT 用戶端

本例中的用戶端是 HANA 大型執行個體單位。 SMT 伺服器安裝程式已將指令碼 clientSetup4SMT.sh 複製到 Azure 虛擬機器。 將該指令碼複製到您想與 SMT 伺服器連線的 HANA 大型執行個體單位。 使用 -h 選項啟動指令碼，並提供 SMT 伺服器的名稱當做參數。 在此範例中，此名稱為 smtserver**。

![螢幕擷取畫面：設定 SMT 用戶端](./media/hana-installation/image12_configure_client.PNG)

可能有用戶端從伺服器成功載入憑證，但註冊失敗，如下列螢幕擷取畫面所示。

![螢幕擷取畫面：用戶端註冊失敗](./media/hana-installation/image13_registration_failed.PNG)

如果註冊失敗，請參閱 [SUSE 支援文件](https://www.suse.com/de-de/support/kb/doc/?id=7006024)，並執行該文件所述的步驟。

> [!IMPORTANT] 
> 至於伺服器名稱，請提供沒有完整網域名稱的虛擬機器名稱 (在本例為 smtserver**)。 

在執行這些步驟之後，請在 HANA 大型執行個體單位上執行下列命令：

```
SUSEConnect –cleanup
```

> [!Note] 
> 執行該步驟後，請等候幾分鐘的時間。 如果您立即執行 clientSetup4SMT.sh，將可能收到錯誤。

如果碰到需要根據 SUSE 一文步驟來修正的問題，請在 HANA 大型執行個體單位上重新啟動 clientSetup4SMT.sh。 現在，它應該會成功完成。

![螢幕擷取畫面：用戶端註冊成功](./media/hana-installation/image14_finish_client_config.PNG)

您已設定 HANA 大型執行個體單位的 SMT 用戶端，使其連線到安裝在 Azure 虛擬機器中的 SMT 伺服器。 您現在可以利用 'zypper up' 或 'zypper in' 將作業系統更新安裝至 HANA 大型執行個體，或安裝其他套件。 您只能取得之前下載到 SMT 伺服器的更新。

## <a name="next-steps"></a>後續步驟
- [HLI 上的 HANA 安裝](hana-example-installation.md)。











