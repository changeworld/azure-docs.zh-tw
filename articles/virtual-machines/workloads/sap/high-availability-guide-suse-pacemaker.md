---
title: 在 Azure 中的 SLES 上設定 Pacemaker | Microsoft Docs
description: 在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: radeltch
ms.openlocfilehash: 28e53c5ca53f5be4aafc685445e67dcf4d558773
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073990"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

有兩個選項可以在 Azure 中設定 Pacemaker 叢集。 您可以使用隔離代理程式，這個代理程式會透過 Azure API 來重新啟動失敗的節點，或者您可以使用 SBD 裝置。

SBD 裝置至少需要一部額外的虛擬機器，作為 iSCSI 目標伺服器並且提供 SBD 裝置。 不過，這些 iSCSI 目標伺服器可以與其他 Pacemaker 叢集共用。 使用 SBD 裝置的優點是，如果您已在內部部署環境中使用 SBD 裝置，則不需要變更您操作 pacemaker 叢集的方式。 您可以對一個 Pacemaker 叢集使用最多三個 SBD 裝置，以容許在 iSCSI 目標伺服器的 OS 修補期間 (舉例來說)，有一個 SBD 裝置無法使用。 如果您想要使用一個以上的 SBD 裝置每 Pacemaker，請務必部署多部 iSCSI 目標伺服器，並從每部 iSCSI 目標伺服器連線一個 SBD。 我們建議使用一個或三個 SBD 裝置。 如果您只設定兩個 SBD 裝置，而且其中一個無法使用，Pacemaker 將無法自動隔離叢集節點。 如果您想要在其中一部 iSCSI 目標伺服器關閉時受到防護，您必須使用三個 SBD 裝置，因此有三個 iSCSI 目標伺服器，這是使用 SBDs 時最具彈性的設定。

Azure 隔離代理程式不需要部署額外的虛擬機器。   

![SLES 上的 Pacemaker 概觀](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> 規劃和部署 Linux Pacemaker 叢集節點與 SBD 裝置時，對於完整叢集設定的整體可靠性而言，最重要的是相關 VM 與裝載 SBD 裝置的 VM 之間，其路由傳送不會通過 [NVA](https://azure.microsoft.com/solutions/network-appliances/) 等任何其他裝置。 否則，與 NVA 相關的問題與維護事件，會對整體叢集設定的穩定性與可靠性造成負面影響。 為了避免這類障礙，在規劃和部署 Linux Pacemaker 叢集節點與 SBD 裝置時，請不要定義 NVA 路由規則，也不要定義透過 NVA 與類似裝置在叢集節點與 SBD 裝置之間路由傳送流量的[使用者定義的路由規則](../../../virtual-network/virtual-networks-udr-overview.md)。 
>

## <a name="sbd-fencing"></a>SBD 隔離

如果您想要使用 SBD 裝置進行隔離，請遵循以下步驟。

### <a name="set-up-iscsi-target-servers"></a>設定 iSCSI 目標伺服器

您必須先建立 iSCSI 目標虛擬機器。 iSCSI 目標伺服器可以與多個 Pacemaker 叢集共用。

1. 部署新的 SLES 12 SP1 或更新版本的虛擬機器，並透過 SSH 連線到這些機器。 這些機器不需要是大型機器。 諸如 Standard_E2s_v3 或 Standard_D2s_v3 的虛擬機器大小就已足夠。 請務必使用 OS 磁碟的進階儲存體。

在所有 **iSCSI 目標虛擬機器**上執行下列命令。

1. 更新 SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > 升級或更新 OS 之後，您可能需要重新開機作業系統。 

1. 移除套件

   若要避免 targetcli 和 SLES 12 SP3 的已知的問題，請解除安裝下列套件。 您可以忽略與找不到套件有關的錯誤

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. 安裝 iSCSI 目標套件

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. 啟用 iSCSI 目標服務

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>在 iSCSI 目標伺服器上建立 iSCSI 裝置

在所有 **iSCSI 目標虛擬機器**上執行下列命令，為 SAP 系統所用的叢集建立 iSCSI 磁碟。 在下列範例中，會建立多個叢集的 SBD 裝置。 它會顯示如何對多個叢集使用一部 iSCSI 目標伺服器。 SBD 裝置會置於 OS 磁碟上。 確定您有足夠的空間。

**`nfs`** 用來識別 NFS 叢集、**ascsnw1** 用來識別 **NW1** 的 ASCS 叢集、**dbnw1** 用來識別 **NW1** 的資料庫叢集、**nfs-0** 和 **nfs-1** 是 NFS 叢集節點的主機名稱、**nw1-xscs-0** 和 **nw1-xscs-1** 是 **NW1** ASCS 叢集節點的主機名稱，而 **nw1-db-0** 和 **nw1-db-1** 是資料庫叢集節點的主機名稱。 使用您的叢集節點主機名稱和 SAP 系統 SID 取代它們。

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

您可以檢查一切是否都已正確設定：

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>設定 SBD 裝置

從叢集連線到上一個步驟中建立的 iSCSI 裝置。
在您想要建立之新叢集的節點上，執行下列命令。
下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 連線到 iSCSI 裝置

   首先，啟用 iSCSI 和 SBD 服務。

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 變更第一個節點上的啟動器名稱

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   變更檔案內容，以符合您在 iSCSI 目標伺服器上建立 iSCSI 裝置時使用的 ACL，例如 NFS 伺服器。

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** 變更第二個節點上的啟動器名稱

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   變更檔案內容，以符合您在 iSCSI 目標伺服器上建立 iSCSI 裝置時使用的 ACL

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** 重新啟動 iSCSI 服務

   立即重新啟動 iSCSI 服務以套用變更

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   連線到 iSCSI 裝置。 在下列範例中，iSCSI 目標伺服器的 IP 位址是 10.0.0.17，預設連接埠是 3260。 <b>iqn.2006-04.nfs.local:nfs</b> 是當您執行以下第一個命令 (iscsiadm -m discovery) 時列出的其中一個目標名稱。

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   請確定 iSCSI 裝置可以使用，並且記下裝置名稱 (在下列範例中為 /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   現在，擷取 iSCSI 裝置的識別碼。

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   此命令會為每個 SBD 裝置列出三個裝置識別碼。 我們建議使用以 scsi-3 開頭的識別碼，在上述範例中即為

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** 建立 SBD 裝置

   使用 iSCSI 裝置的裝置識別碼，在第一個叢集節點上建立新的 SBD 裝置。

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** 調整 SBD 組態

   開啟 SBD 組態檔

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   變更 SBD 裝置的屬性，啟用 Pacemaker 整合，並且變更 SBD 的啟動模式。

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   建立 `softdog`組態檔

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   立即載入模組

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>叢集安裝

下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 更新 SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** 安裝叢集資源所需的元件

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** 安裝叢集資源所需的 azure-lb 元件

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > 請檢查套件 resource-agents 的版本，並確定符合最低版本需求：  
   > - 對於 SLES 12 SP4/SP5，版本必須至少為 resource-agents-4.3.018.a7fb5035-3.30.1。  
   > - 對於 SLES 15/15 SP1，版本必須至少為 resource-agents-4.3.0184.6ee15eb2-4.13.1。  

1. **[A]** 設定作業系統

   在某些情況下，Pacemaker 會建立許多程序，因此用盡允許的程序數目。 在此情況下，叢集節點之間的活動訊號可能會失敗，而導致您的資源容錯移轉。 我們建議設定下列參數，以增加允許的程序上限。

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   縮減已變更的快取大小。 如需詳細資訊，請參閱[使用大量 RAM 的 SLES 11/12 伺服器出現低寫入效能](https://www.suse.com/support/kb/doc/?id=7010287) (英文)。

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** 設定適用於 HA 叢集的 cloud-netconfig-azure

   >[!NOTE]
   > 執行 **zypper info cloud-netconfig-azure**，以檢查已安裝的套件 **cloud-netconfig-azure** 版本。 如果您環境中的版本是 1.3 或更高版本，則不再需要透過雲端網路外掛程式來抑制網路介面的管理。 如果版本低於 1.3，建議將套件 **cloud-netconfig-azure** 更新為最新的可用版本。  

   變更網路介面的組態檔 (如下所示)，以防止雲端網路外掛程式移除虛擬 IP 位址 (Pacemaker 必須控制 VIP 指派)。 如需詳細資訊，請參閱 [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)。 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** 啟用 SSH 存取

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** 啟用 SSH 存取

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** 啟用 SSH 存取

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** 使用以 Azure 隔離代理程式為基礎的 STONITH 裝置，安裝隔離代理程式套件。  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > 已安裝的套件隔離版本-如果需要圍住叢集節點，則**代理程式**必須至少是**4.4.0** ，才能受益于使用 Azure 隔離代理程式的更快速容錯移轉時間。 如果執行較舊的版本，我們建議您更新套件。  


1. **[A]** 安裝 AZURE Python SDK 
   - 在 SLES 12 SP4 或 SLES 12 SP5 上
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - SLES 15 和更新版本 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >根據您的版本和映射類型而定，您可能需要啟用作業系統版本的公用雲端擴充功能，才能安裝 Azure Python SDK。
   >您可以藉由執行 SUSEConnect---清單-延伸模組來檢查延伸模組。  
   >使用 Azure 隔離代理程式達到更快速的容錯移轉時間：
   > - 在 SLES 12 SP4 或 SLES 12 SP5 安裝 version **4.6.2**或更高版本的套件 python-azure-管理-計算  
   > - 在 SLES 15 上安裝**4.6.2**或更高版本的套件 python**3**-azure-管理-計算 

1. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   請取代下列命令中的 IP 位址和主機名稱。 使用 /etc/hosts 的好處在於，您的叢集會變成不受 DNS 影響，而 DNS 也可能是單一失敗點。

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** 安裝叢集
- 如果使用 SBD 裝置進行隔離
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- 如果*未使用*SBD 裝置進行隔離
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** 將節點新增至叢集

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** 將 hacluster 密碼變更為同一個密碼

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** 調整 corosync 設定。  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   將下列粗體內容新增至檔案 (如果檔案中沒有這些值或不同)。 請務必將權杖變更為 30000，以允許記憶體保留維護。 如需詳細資訊，請參閱 [Linux 文章][virtual-machines-linux-maintenance]或 [Windows 文章][virtual-machines-windows-maintenance]。

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   然後重新啟動 corosync 服務

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD 的預設 Pacemaker 組態

本節中的設定僅適用于使用 SBD STONITH 的情況。  

1. **[1]** 能夠啟用 STONITH 裝置及設定柵欄延遲

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>建立 Azure Fence 代理程式 STONITH 裝置

檔集的這一節僅適用于以 Azure 範圍代理程式為基礎的 STONITH。
STONITH 裝置會使用服務主體來對 Microsoft Azure 授權。 請遵循下列步驟來建立服務主體。

1. 移至 <https://portal.azure.com>。
1. 開啟 [Azure Active Directory] 刀鋒視窗  
   移至 [屬性]，並記下目錄識別碼。 這是「租用戶識別碼」。
1. 按一下 [應用程式註冊]
1. 按一下新增註冊
1. 輸入名稱，選取 [僅限此組織目錄中的帳戶] 
2. 選取應用程式類型 [Web]，輸入登入 URL (例如，http:\//localhost)，然後按一下 [新增]  
   登入 URL 並未使用，而且可以是任何有效的 URL
1. 選取 [憑證和祕密]，然後按一下 [新增用戶端秘密]
1. 輸入新金鑰的說明、選取 [永不過期]，然後按一下 [新增]
1. 記下值。 此值會用來做為服務主體的**密碼**
1. 選取 [概觀]。 記下應用程式識別碼。 此識別碼會用來做為服務主體的使用者名稱 (以下步驟中的「登入識別碼」)

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 為柵欄代理程式建立自訂角色

服務主體預設沒有存取您 Azure 資源的權限。 您需要為服務主體提供權限來啟動和停止 (解除配置) 叢集的所有虛擬機器。 如果您尚未建立自訂角色，您可以使用 [PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) 或 [Azure CLI](../../../role-based-access-control/custom-roles-cli.md) 來建立

針對輸入檔使用下列內容。 您必須調整訂用帳戶的內容，也就是使用您訂用帳戶的識別碼取代 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624。 如果您只有一個訂用帳戶，請在 AssignableScopes 中移除第二個項目。

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 將自訂角色指派給服務主體

將在上一章中建立的自訂角色「Linux 柵欄代理程式角色」指派給服務主體。 不要再使用「擁有者」角色！

1. 移至 [https://portal.azure.com](https://portal.azure.com)
1. 開啟 [所有資源] 刀鋒視窗
1. 選取第一個叢集節點的虛擬機器
1. 選取 [存取控制 (IAM)]
1. 按一下 [新增角色指派]
1. 選取「Linux 柵欄代理程式角色」角色
1. 輸入您先前建立的應用程式名稱
1. 按一下 [Save] \(儲存)。

針對第二個叢集節點重複上述步驟。

### <a name="1-create-the-stonith-devices"></a>**[1]** 建立 STONITH 裝置

當您編輯虛擬機器的權限之後，就可以在叢集中設定 STONITH 裝置。

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 \ 
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> 監視和隔離作業會解除序列化。 因此，如果有較長的執行中監視作業和同時隔離的事件，叢集容錯移轉就不會延遲，因為已經在執行監視作業。

> [!TIP]
>Azure 柵欄代理程式需要[使用標準 ILB 的 VM 公用端點連線](./high-availability-guide-standard-load-balancer-outbound-connections.md)中記載的公用端點輸出連線能力，以及可能的解決方案。  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Azure 已排定事件的 Pacemaker 設定

Azure 提供[已排定事件](../../linux/scheduled-events.md)。 已排定事件是透過中繼資料服務提供，讓應用程式有時間針對 VM 關機、VM 重新部署等事件進行準備。資源代理程式 **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** 會監視已排定的 Azure 事件。 如果偵測到事件，此代理程式將會嘗試停止受影響 VM 上的所有資源，並將其移至叢集中的另一個節點。 若要達到此目的，必須設定額外的 Pacemaker 資源。 

1. **[A]** 確定已安裝 **azure-events** 代理程式的套件，且為最新狀態。 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** 在 Pacemaker 中設定資源。 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > 設定 azure-events 代理程式的 Pacemaker 資源之後，當您將叢集放入或移出維護模式時，您可能會收到類似下列的警告訊息：  
     警告：cib-bootstrap-options：不明的屬性 'hostName_  <strong>hostname</strong>'  
     警告：cib-bootstrap-options：不明的屬性 'azure-events_globalPullState'  
     警告：cib-bootstrap-options：不明的屬性 'hostName_ <strong>hostname</strong>'  
   > 您可以忽略這些警告訊息。

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SUSE Linux Enterprise Server 上 Azure VM 的 NFS 高可用性][sles-nfs-guide]
* [SAP NetWeaver 在適用於 SAP 應用程式之 SUSE Linux Enterprise Server 上的 Azure VM 高可用性][sles-guide]
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
