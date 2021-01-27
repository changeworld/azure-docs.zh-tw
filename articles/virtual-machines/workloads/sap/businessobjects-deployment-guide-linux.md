---
title: 適用于 Linux 的 Azure 上的 SAP BusinessObjects BI 平臺部署 |Microsoft Docs
description: 在適用于 Linux 的 Azure 上部署和設定 SAP BusinessObjects BI 平臺
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: a7361dafce30b07e76d971bdcda41cf4b3cd9e6e
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806177"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure 上適用於 linux 的 SAP BusinessObjects BI 平台部署指南

本文說明在適用于 Linux 的 Azure 上部署 SAP BOBI Platform 的策略。 在此範例中，會設定兩部具有進階 SSD 受控磁碟的虛擬機器作為其安裝目錄。 適用於 MySQL 的 Azure 資料庫用於 CMS 資料庫，而檔案存放庫伺服器的 Azure NetApp Files 則會在兩部伺服器之間共用。 預設 Tomcat JAVA web 應用程式和 BI 平臺應用程式會同時安裝在這兩部虛擬機器上。 若要對使用者要求進行負載平衡，則會使用應用程式閘道，其具有原生 TLS/SSL 卸載功能。

這種類型的架構適用于小型部署或非生產環境。 針對生產或大規模部署，您可以有不同的 Web 應用程式主機，也可以讓多個 BOBI 應用程式裝載，讓伺服器能夠處理更多的資訊。

![適用于 Linux 的 Azure 上的 SAP BOBI 部署](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

在此範例中，會使用下列產品版本和檔案系統配置

- SAP BusinessObjects Platform 4。3
- SUSE Linux Enterprise Server 12 SP5
- 適用於 MySQL 的 Azure 資料庫 (版本： 8.0.15) 
- MySQL C API 連接器-libmysqlclient (版本： 6.1.11) 

| 檔案系統        | 描述                                                                                                               | 大小 (GB)             | 擁有者  | Group  | 儲存體                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | 如有必要，可安裝 SAP BOBI 實例、預設 Tomcat Web 應用程式和資料庫驅動程式的檔案系統 ()  | SAP 調整大小指導方針 | bl1adm | sapsys | 受控 Premium 磁片-SSD |
| /usr/sap/frsinput  | 掛接目錄適用于所有將用來作為輸入檔案存放庫目錄的 BOBI 主機上的共用檔案  | 商務需求         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | 掛接目錄適用于所有將用來作為輸出檔案存放庫目錄的 BOBI 主機上的共用檔案 | 商務需求         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>透過 Azure 入口網站部署 linux 虛擬機器

在本節中，我們將建立兩部虛擬機器 (Vm) Linux 作業系統 (作業系統) 映射（適用于 SAP BOBI 平臺）。 建立虛擬機器的高階步驟如下：

1. 建立 [資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. 建立 [虛擬網路](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - 請勿將單一子網用於 SAP BI 平臺部署中的所有 Azure 服務。 根據 SAP BI 平臺架構，您需要建立多個子網。 在此部署中，我們將建立三個子網：應用程式子網、檔案儲存機制儲存子網，以及應用程式閘道子網。
   - 在 Azure 中，應用程式閘道和 Azure NetApp Files 一律必須位於不同的子網上。 如需詳細資訊，請參閱[Azure NetApp Files 網路規劃](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)文章[Azure 應用程式閘道](../../../application-gateway/configuration-overview.md)和指導方針。

3. 建立可用性設定組。

   - 若要針對多重實例部署中的每個層級達到冗余，請將每個階層的虛擬機器放在可用性設定組中。 請確定您根據您的架構來分隔每個階層的可用性設定組。

4. 建立虛擬機器 1 **(azusbosl1) 。**

   - 您可以使用自訂映射，或從 Azure Marketplace 選擇影像。 請參閱 [從適用于 sap 的 Azure Marketplace 部署 vm](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) ，或根據您的需求 [使用適用于 sap 的自訂映射來部署 vm](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) 。

5. 建立虛擬機器 2 **(azusbosl2) 。**
6. 新增一個進階 SSD 磁片。 它將用來作為 SAP BOBI 安裝目錄。

## <a name="provision-azure-netapp-files"></a>布建 Azure NetApp Files

繼續進行 Azure NetApp Files 的設定之前，請先熟悉 Azure [Netapp files](../../../azure-netapp-files/azure-netapp-files-introduction.md)檔。

Azure NetApp Files 可在數個 [azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中使用。 查看您選取的 Azure 區域是否提供 Azure NetApp Files。

使用 azure [區域的 Azure Netapp files 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) 頁面，依區域檢查 Azure netapp files 的可用性。

在部署 Azure NetApp Files 之前，請先 [註冊 Azure netapp files 指示](../../../azure-netapp-files/azure-netapp-files-register.md) ，要求將 Azure netapp files 上線。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp Files 資源

下列指示假設您已部署 [Azure 虛擬網路](../../../virtual-network/virtual-networks-overview.md)。 將裝載 Azure NetApp Files 資源的 Azure NetApp Files 資源和 Vm，必須部署在相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。

1. 如果您尚未部署資源，請要求在 [Azure NetApp Files 上架](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 遵循 [建立 netapp 帳戶](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的指示，在您選取的 Azure 區域中建立 NetApp 帳戶。

3. 遵循 [設定 Azure Netapp files 容量](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)集區中的指示，設定 Azure netapp files 容量集區。

   - 本文所提供的 SAP BI 平臺架構使用 *Premium* 服務層級的單一 Azure NetApp Files 容量集區。 針對 Azure 上的 SAP BI 檔案存放庫伺服器，我們建議使用 Azure NetApp Files *Premium* 或 *Ultra* [服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4. 將子網委派給 Azure NetApp Files，如將 [子網委派至 Azure Netapp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的指示所述。

5. 遵循 [建立適用于 Azure Netapp files 的 NFS 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)區中的指示，部署 Azure netapp files 磁片區。

   ANF 磁片區可以部署為 NFSv3 和 Nfsv4.1 4.1，因為 SAP BOBI Platform 支援這兩種通訊協定。 將磁片區部署在個別的 Azure NetApp Files 子網中。 系統會自動指派 Azure NetApp 磁片區的 IP 位址。

請記住，Azure NetApp Files 資源和 Azure Vm 必須位於相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。 例如，azusbobi-frsinput，azusbobi-frsoutput 是磁片區名稱和 nfs://10.31.2.4/azusbobi-frsinput，nfs://10.31.2.4/azusbobi-frsoutput 是 Azure NetApp Files 磁片區的檔案路徑。

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput) 
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput) 

### <a name="important-considerations"></a>重要考量

當您建立適用于 SAP BOBI Platform File 存放庫伺服器的 Azure NetApp Files 時，請注意下列考慮：

1. 容量集區的最小值是 4 tib (TiB) 。
2. 磁片區大小下限為 100 32,767 gib (GiB) 。
3. Azure NetApp Files 和將掛接 Azure NetApp Files 磁片區的所有虛擬機器，都必須位於相同的 Azure 虛擬網路或相同區域中的 [對等互連虛擬網路](../../../virtual-network/virtual-network-peering-overview.md) 中。 Azure NetApp Files 現在支援透過相同區域內的 VNET 對等互連進行存取。 尚不支援透過全球對等互連的 Azure NetApp access。
4. 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網。
5. 使用 Azure NetApp Files [匯出原則](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，您可以控制允許的用戶端、存取類型 (讀寫、唯讀等) 。
6. Azure NetApp Files 功能尚未感知區域。 目前，此功能不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。
7. Azure NetApp Files 磁碟區可作為 NFSv3 或 NFSv4.1 磁碟區部署。 這兩種通訊協定都支援 SAP BI 平臺應用程式。

## <a name="configure-file-systems-on-linux-servers"></a>在 linux 伺服器上設定檔案系統

本節中的步驟會使用下列首碼：

**[A]**：此步驟適用于所有主機

### <a name="format-and-mount-sap-file-system"></a>格式化和掛接 SAP 檔案系統

1. **[A]** 列出所有連接的磁片

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** 適用于/Usr/sap 的 Format 區塊裝置

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 建立裝載目錄

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 取得封鎖裝置的 UUID

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** 在/Etc/fstab 中維護檔案系統掛接專案

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 掛接檔案系統

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>掛接 Azure NetApp Files 磁片區

1. **[A]** 建立裝載目錄

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** 將用戶端作業系統設定為支援 Nfsv4.1 4.1 掛接 **(僅適用于使用 nfsv4.1 4.1)**

   如果您使用的是具有 Nfsv4.1 4.1 通訊協定的 Azure NetApp Files 磁片區，請在所有 Vm 上執行下列設定，其中必須裝載 Azure NetApp Files Nfsv4.1 4.1 磁片區。

   **確認 NFS 網域設定**

   請確定網域已設定為預設的 Azure NetApp Files 網域，也就是  **defaultv4iddomain.com** ，而且對應設定為無 **人**。

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > 請務必將 VM 上/etc/idmapd.conf 中的 NFS 網域設定為符合 Azure NetApp Files 上的預設網域設定： **defaultv4iddomain.com**。 如果 NFS 用戶端上的網域設定不符 (亦即 VM) 和 NFS 伺服器（亦即 Azure NetApp configuration），則裝載在 Vm 上的 Azure NetApp 磁片區上的檔案許可權將會顯示為未使用。

   驗證 `nfs4_disable_idmapping`。 其應設為 **Y**。若要建立 `nfs4_disable_idmapping` 所在的目錄結構，請執行掛接命令。 您將無法手動在 /sys/modules 下建立目錄，因為其存取已保留給核心/驅動程式。

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** 新增掛接項目

   如果使用 NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   如果使用 Nfsv4.1 4。1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** 掛接 NFS 磁片區

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>設定 CMS 資料庫-適用于 MySQL 的 Azure 資料庫

本節提供如何使用 Azure 入口網站布建適用於 MySQL 的 Azure 資料庫的詳細資料。 此外，它也提供如何建立 CMS 和審核資料庫以供 SAP BOBI Platform 使用的指示，以及用來存取資料庫的使用者帳戶。

只有當您使用適用于 MySQL 的 Azure DB 時，才適用這些指導方針。 針對其他) 的資料庫 (，請參閱 SAP 或資料庫特定的檔以取得指示。

### <a name="create-an-azure-database-for-mysql"></a>建立適用于 MySQL 的 Azure 資料庫

登入 Azure 入口網站，並依照適用於 MySQL 的 Azure 資料庫的《 [快速入門手冊》](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)中所述的步驟進行。 布建適用於 MySQL 的 Azure 資料庫時要注意的幾點：

1. 為您的 SAP BI 平臺應用程式伺服器執行所在的適用於 MySQL 的 Azure 資料庫選取相同的區域。

2. 根據產品可用性對照表選擇支援的 DB 版本， (您的 SAP BOBI 版本專用 [的 SAP BI 的 PAM) ](https://support.sap.com/pam) 。 遵循與在 SAP PAM 中針對 MySQL AB 所解決的相同相容性方針

3. 在 [計算 + 儲存體] 中，選取 [ **設定伺服器** ]，然後根據您的大小調整輸出來選取適當的定價層。

4. 預設會啟用 **儲存體** 自動成長。 請記住， [儲存體](../../../mysql/concepts-pricing-tiers.md#storage) 只能相應增加，無法相應減少。

5. 根據預設， **備份保留期限** 為7天，但您可以 [選擇性地將](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 它設定為35天。

6. 適用於 MySQL 的 Azure 資料庫的備份預設會在本機重複使用，因此，如果您想要在地理位置分散的儲存體中進行伺服器備份，請從 **備份冗余選項** 中選取 [**地理位置多餘**]。

> [!NOTE]
> 不支援在建立伺服器之後變更 [備份冗余選項](../../../mysql/concepts-backup.md#backup-redundancy-options) 。

### <a name="configure-connection-security"></a>設定連線安全性

依預設，建立的伺服器會受到防火牆保護，而無法公開存取。 若要提供存取權給 SAP BI 平臺應用程式伺服器執行所在的虛擬網路，請遵循下列步驟：  

1. 移至 Azure 入口網站中的 [伺服器資源]，然後從伺服器資源的左側功能表中選取 [ **連接安全性** ]。
2. 選取 **[是]** 以 **允許存取 Azure 服務**。
3. 在 [VNET 規則] 底下，選取 [ **新增現有的虛擬網路**]。 選取 SAP BI Platform application server 的虛擬網路和子網。 此外，您還需要提供跳躍方塊或其他伺服器的存取權，讓您可以將 [MySQL 工作臺](../../../mysql/connect-workbench.md) 連線至適用於 MySQL 的 Azure 資料庫。 MySQL 工作臺將用來建立 CMS 和 Audit 資料庫
4. 新增虛擬網路之後，請選取 [ **儲存**]。

### <a name="create-cms-and-audit-database"></a>建立 CMS 和 audit 資料庫

1. 從 [mysql 網站](https://dev.mysql.com/downloads/workbench/)下載並安裝 mysql 工作臺。 請確定您在可存取適用於 MySQL 的 Azure 資料庫的伺服器上安裝 MySQL 工作臺。

2. 使用 MySQL 工作臺來連接到伺服器。 遵循本文中所述的[指示。](../../../mysql/connect-workbench.md#get-connection-information) 如果連接測試成功，您將會收到下列訊息：

   ![SQL 工作臺連接](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. 在 [SQL 查詢] 索引標籤中，執行下列查詢以建立 CMS 和 Audit 資料庫的架構。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. 建立使用者帳戶以連接到架構

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. 檢查 MySQL 使用者帳戶的許可權和角色

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>在 linux 伺服器上安裝 MySQL C API 連接器 (libmysqlclient) 

若要讓 SAP BOBI 應用程式伺服器存取資料庫，則需要資料庫用戶端/驅動程式。 適用于 Linux 的 MySQL C API 連接器必須用來存取 CMS 和 Audit 資料庫。 不支援對 CMS 資料庫的 ODBC 連接。 本節提供如何在 Linux 上設定 MySQL C API 連接器的指示。

1. 請參閱 [與適用於 MySQL 的 Azure 資料庫文章相容的 MySQL 驅動程式和管理工具](../../../mysql/concepts-compatibility.md) ，其描述與適用於 MySQL 的 Azure 資料庫相容的驅動程式。 請在本文中檢查 **MySQL Connector/C (libmysqlclient)** 驅動程式。

2. 請參閱此 [連結](https://downloads.mysql.com/archives/c-c/) 以下載驅動程式。

3. 選取作業系統，並下載 MySQL 連接器的共用元件 rpm 套件。 在此範例中，會使用 mysql-connector-c-共用-6.1.11 連接器版本。

4. 在所有 SAP BOBI 應用程式實例中安裝連接器。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. 檢查 libmysqlclient.so 的路徑

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. 針對 `/usr/lib64` 將用於安裝的使用者帳戶，將 LD_LIBRARY_PATH 設定為指向 [目錄]。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>伺服器準備

本節中的步驟會使用下列首碼：

**[A]**：此步驟適用于所有主機。

1. **[A]** 根據 LINUX (SLES 或 RHEL) 的類別，您必須設定核心參數，並安裝必要的程式庫。 請參閱 [Unix 商務智慧平臺安裝指南中的](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)**系統需求** 一節。

2. **[A]** 確定已正確設定電腦上的時區。 請參閱安裝指南中的 [其他 Unix 和 Linux 需求一節](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) 。

3. **[A]** 建立使用者帳戶 (**bl1** adm) ，以及可在其下執行軟體背景進程的群組 (sapsys) 。 使用此帳戶來執行安裝並執行軟體。 帳戶不需要根許可權。

4. **[A]** 設定使用者帳戶 (**bl1** adm) 環境使用支援的 utf-8 地區設定，並確保您的主控台軟體支援 utf-8 字元集。 為確保您的作業系統使用正確的地區設定，請在 (**bl1** adm) 使用者環境中，將 LC_ALL 和 LANG 環境變數設定為您慣用的地區設定。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** 設定 (**bl1** adm) 的使用者帳戶。

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. 從 SAP Service Marketplace 下載並解壓縮 SAP BusinessObjects BI 平臺的媒體。

## <a name="installation"></a>安裝

檢查伺服器上的使用者帳戶 **bl1** adm 的地區設定

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

流覽至 SAP BusinessObjects BI 平臺的媒體，並使用 **bl1** adm 使用者執行以下命令-

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

遵循適用于 Unix 的 [SAP BOBI Platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) 安裝指南（適用于您的版本）。 安裝 SAP BOBI Platform 時，請注意幾點。

- 在 [ **設定產品註冊** 畫面] 上，您可以使用 sap Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) 中 sap BusinessObjects 解決方案的暫時授權金鑰，也可以在 sap Service Marketplace 中產生授權金鑰

- 在 [ **選取安裝類型** ] 畫面上，選取 [第一部伺服器上的 **完全** 安裝 (azusbosl1) ，針對其他伺服器 (Azusbosl2) 選取 [ **自訂/展開** ]，以擴充現有的 BOBI 設定。

- 在 [ **選取預設或現有的資料庫** ] 畫面上，選取 [ **設定現有的資料庫**]，這會提示您選取 CMS 和 Audit Database。 選取 [ **MySQL** ] 作為 [CMS 資料庫類型] 和 [Audit] 資料庫類型。

  如果您不想在安裝期間設定審核，也可以選取 [沒有任何審核資料庫]。

- 根據您的 SAP BOBI 架構，在 [ **選取 JAVA Web 應用程式伺服器] 畫面** 上選取適當的選項。 在此範例中，我們選取了選項1，這會在相同的 SAP BOBI 平臺上安裝 tomcat 伺服器。

- 在 [ **設定 Cms 儲存機制資料庫-MySQL**] 中輸入 CMS 資料庫資訊。 適用于 Linux 安裝的 CMS 資料庫資訊輸入範例。 預設通訊埠3306上使用適用於 MySQL 的 Azure 資料庫
  
  ![Linux 上的 SAP BOBI 部署-CMS 資料庫](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

-  (選擇性) 在 [ **設定 Audit 儲存機制資料庫-MySQL**] 中輸入 Audit database 資訊。 適用于 Linux 安裝的 Audit 資料庫資訊的輸入範例。

  ![Linux 上的 SAP BOBI 部署-Audit Database](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 依照指示進行，並輸入必要的輸入以完成安裝。

若為多重實例部署，請在第二部主機上執行安裝安裝 (azusbosl2) 。 在 [ **選取安裝類型** ] 畫面中，選取 [ **自訂/展開** ]，以擴充現有的 BOBI 設定。

在適用于 MySQL 的 Azure 資料庫供應專案中，會使用閘道將連接重新導向至伺服器實例。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MySQL 的版本，而非 MySQL 伺服器執行個體上執行的實際版本。 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。 因此，在中央管理主控台中 (CMC) ，您會發現基本上是在閘道上設定版本的不同資料庫版本。 如需詳細資訊，請查看 [支援的適用於 MySQL 的 Azure 資料庫伺服器版本](../../../mysql/concepts-supported-versions.md) 。

![Linux 上的 SAP BOBI 部署-CMC 設定](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>安裝後

### <a name="tomcat-clustering---session-replication"></a>Tomcat 叢集-會話複寫

Tomcat 支援將兩部以上的應用程式伺服器叢集化，以進行會話複寫和容錯移轉。 SAP BOBI platform 會話已序列化，使用者會話可以順暢地容錯移轉到另一個 tomcat 實例，即使應用程式伺服器失敗也是一樣。

例如，如果使用者連線到的 web 伺服器在使用者流覽 SAP BI 應用程式中的資料夾階層時失敗。 使用正確設定的叢集之後，使用者可以繼續流覽資料夾階層，而不會將其重新導向至登入頁面。

在 SAP 附注 [2808640](https://launchpad.support.sap.com/#/notes/2808640)中，使用多播提供設定 tomcat 叢集的步驟。 但在 Azure 中，不支援多播。 因此，若要讓 Tomcat 叢集在 Azure 中運作，您必須使用 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)) 。 在 SAP blog 上 [使用 Sap BUSINESSOBJECTS BI 平臺的靜態成員資格檢查 tomcat](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) 叢集，以在 Azure 中設定 tomcat 叢集。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 平臺的負載平衡 web 層

在 SAP BOBI 多重實例部署中， (Web 層) 的 JAVA Web 應用程式伺服器會在兩部或更多部主機上執行。 若要將使用者負載平均分散到 web 伺服器上，您可以在使用者和 web 伺服器之間使用負載平衡器。 在 Azure 中，您可以使用 Azure Load Balancer 或 Azure 應用程式閘道來管理 web 應用程式伺服器的流量。 下一節將說明每個供應專案的詳細資料。

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure 負載平衡器 (網路負載平衡器) 

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 是高效能、低延遲的第4層 (TCP、UDP) 負載平衡器，可在健康的虛擬機器之間分散流量。 負載平衡器健康狀態探查會監視每部 VM 上指定的埠，而且只會將流量分散至 () 的操作虛擬機器。 您可以根據是否想要從網際網路存取 SAP BI 平臺，選擇公用負載平衡器或內部負載平衡器。 它的區域是冗余的，確保可用性區域之間的高可用性。

請參閱下圖中的內部 Load Balancer 一節，其中 web 應用程式伺服器會在埠8080（預設 Tomcat HTTP 埠）上執行，其將由健康情況探查監視。 因此，任何來自終端使用者的連入要求都會被重新導向至後端集區中 (azusbosl1 或 azusbosl2) 的 web 應用程式伺服器。 負載平衡器不支援 TLS/SSL 終止 (也稱為 TLS/SSL 卸載) 。 如果您使用 Azure 負載平衡器將流量分散到 web 伺服器，我們建議使用 Standard Load Balancer。

> [!NOTE]
> 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資料，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](high-availability-guide-standard-load-balancer-outbound-connections.md)。

![在 Web 服務器之間平衡流量的 Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure 應用程式閘道 (web 應用程式負載平衡器) 

[Azure 應用程式 Gateway (AGW) ](../../../application-gateway/overview.md) 提供應用程式傳遞控制器 (ADC) 即服務，可用來協助應用程式將使用者流量導向至一或多個 web 應用程式伺服器。 它提供各種第7層負載平衡功能，例如 TLS/SSL 卸載、Web 應用程式防火牆 (WAF) 、以 Cookie 為基礎的會話親和性，以及您應用程式的其他專案。

在 SAP BI 平臺中，應用程式閘道會將應用程式網路流量導向至後端集區中的指定資源-azusbosl1 或 azusbos2。 您可以將接聽程式指派給埠、建立規則，並將資源新增至後端集區。 在下圖中，具有私人前端 IP 位址的應用程式閘道 (10.31.3.20) 作為使用者的進入點、處理連入的 TLS/SSL (HTTPS-TCP/443) 連線、將 TLS/SSL 解密，並將未加密的要求（HTTP-TCP/8080 (）傳遞給後端集區中的伺服器。 使用內建的 TLS/SSL 終止功能時，我們只需要在應用程式閘道上維護一個 TLS/SSL 憑證，以簡化作業。

![應用程式閘道以平衡 Web 服務器之間的流量](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

若要設定 SAP BOBI Web 服務器的應用程式閘道，您可以使用 SAP blog 上的 [Azure 應用程式閘道來參考 SAP BOBI Web 服務器的負載平衡](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) 。

> [!NOTE]
> 建議您使用 Azure 應用程式閘道來對 web 伺服器的流量進行負載平衡，因為它提供類似 SSL 卸載的功能，並集中進行 SSL 管理以減少伺服器上的加密和解密負荷、Round-Robin 演算法來分散流量、Web 應用程式防火牆 (WAF) 功能、高可用性等等。

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI 平臺-備份與還原

備份與還原是將資料和應用程式的定期複本建立至不同位置的流程。 如此一來，如果原始資料或應用程式遺失或損毀，它就可以還原或復原到先前的狀態。 它也是任何商務嚴重損壞修復策略的重要元件。

若要開發 SAP BOBI Platform 的完整備份和還原策略，請找出導致應用程式發生系統停機或中斷的元件。 在 SAP BOBI 平臺中，下列元件的備份是保護應用程式的關鍵。

-  (受控 Premium 磁片) 的 SAP BOBI 安裝目錄
- 檔案存放庫伺服器 (Azure NetApp Files 或 Azure Premium Files) 
- Azure VM 上的 CMS 資料庫 (適用於 MySQL 的 Azure 資料庫或資料庫) 

下一節說明如何針對 SAP BOBI Platform 上的每個元件執行備份和還原策略。

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>適用于 SAP BOBI 安裝目錄的備份 & 還原

在 Azure 中，備份應用程式伺服器和所有連接的磁片最簡單的方式，就是使用 [Azure 備份](../../../backup/backup-overview.md) 服務。 它提供獨立且隔離的備份，以防止非預期的資料在您的 Vm 上損毀。 備份會儲存在復原服務保存庫中，並進行內建的復原點管理。 設定和調整很簡單，備份已優化，可在需要時輕鬆地進行還原。

在備份過程中，會建立快照集，並將資料傳輸至復原服務保存庫，而不會影響生產工作負載。 快照集會提供不同層級的一致性，如 [快照一致性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) 文章中所述。 您也可以選擇使用選擇性磁片備份和還原功能來備份 VM 中的資料磁片子集。 如需詳細資訊，請參閱 [AZURE Vm 備份](../../../backup/backup-azure-vms-introduction.md) 檔和 [常見問題-備份 azure vm](../../../backup/backup-azure-vm-backup-faq.yml)。

#### <a name="backup--restore-for-file-repository-server"></a>檔案存放庫伺服器的備份 & 還原

針對 **Azure NetApp Files**，您可以建立隨選快照集，並使用快照集原則來排程自動快照集。 快照集複本提供 ANF 磁片區的時間點複本。 如需詳細資訊，請參閱 [使用 Azure NetApp Files 管理快照](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)集。

**Azure 檔案儲存體** 備份已與原生 [Azure 備份](../../../backup/backup-overview.md) 服務整合，此服務可集中備份和還原功能以及 vm 備份，並簡化作業工作。 如需詳細資訊，請參閱 [Azure 檔案共用備份](../../../backup/azure-file-share-backup-overview.md) 和 [常見問題-備份 Azure 檔案儲存體](../../../backup/backup-azure-files-faq.md)。

#### <a name="backup--restore-for-cms-database"></a>CMS 資料庫的備份 & 還原

Azure 的 MySQL 資料庫是 Azure 中的 DBaaS 供應專案，它會自動建立伺服器備份，並將其儲存在使用者設定的本機冗余或地理位置多餘的儲存體中。 MySQL 的 Azure 資料庫會取得資料檔案和交易記錄檔的備份。 根據支援的儲存體大小上限，它會取得完整和差異備份 (4 TB 的最大儲存體伺服器) 或快照集備份 (最多 16 TB 的儲存體伺服器) 。 這些備份可讓您在設定的備份保留期限內的任何時間點還原伺服器。 預設的備份保留期限是七天，您可以 [選擇性地將它](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 設定為最多三天。 所有備份皆會使用 AES 256 位元加密進行加密。

這些備份檔案不是由使用者公開，無法匯出。 這些備份只能用於適用於 MySQL 的 Azure 資料庫中的還原作業。 您可以使用 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 來複製資料庫。 如需詳細資訊，請參閱 [適用於 MySQL 的 Azure 資料庫中的備份與還原](../../../mysql/concepts-backup.md)。

針對安裝在虛擬機器上的資料庫，您可以使用適用于 HANA 資料庫的標準備份工具或 [Azure 備份](../../../backup/sap-hana-db-about.md) 。 此外，如果 Azure 服務和工具不符合您的需求，您可以使用其他備份工具或腳本來建立磁片備份。

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI 平臺可靠性

SAP BusinessObjects BI 平臺包含不同的層級，這些層級已針對特定工作和作業進行優化。 當某個階層中的元件變成無法使用時，SAP BOBI 應用程式會變成無法存取，或應用程式的某些功能將無法運作。 因此，您必須確定每個階層的設計都能可靠地維持應用程式運作，而不會發生任何業務中斷。

本節著重于 SAP BOBI Platform 的下列選項-

- **高可用性：** 高可用性平臺至少有兩個 Azure 區域內的兩個專案，可讓應用程式在其中一個伺服器變成無法使用時保持運作。
- 嚴重損壞 **修復：** 它是一種還原應用程式功能的程式，如果因為某些嚴重損壞而無法使用整個 Azure 區域，就會發生任何重大損失。

此解決方案的執行會根據 Azure 中系統設定的本質而有所不同。 因此，客戶需要根據其商務需求量身打造高可用性和嚴重損壞修復解決方案。

### <a name="high-availability"></a>高可用性

高可用性是指一組技術，藉由在相同資料中心內透過冗余、容錯或容錯移轉保護的元件，提供應用程式/服務的商務持續性，可將 IT 中斷降至最低。 在我們的案例中，資料中心位於一個 Azure 區域內。 適用于 [sap 的高可用性架構和案例](sap-high-availability-architecture-scenarios.md) 會提供針對 Sap 應用程式提供的不同高可用性技術和建議的初步見解，其將補充本節中的指示。

根據 SAP BOBI Platform 的調整結果，您需要設計環境，並判斷跨 Azure 虛擬機器和子網的 BI 元件分佈。 分散式架構中的冗余層級取決於商務所需的復原時間目標 (RTO) 和復原點目標 (RPO) 。 SAP BOBI Platform 包含每個階層的不同層級和元件，應設計為可達成冗余。 如此一來，如果一個元件失敗，您的 SAP BOBI 應用程式幾乎不會中斷。 例如，

- 多餘的應用程式伺服器，例如 BI 應用程式伺服器和網頁伺服器
- 獨特的元件，例如 CMS 資料庫、檔案存放庫伺服器、Load Balancer

下一節說明如何在 SAP BOBI Platform 的每個元件上達到高可用性。

#### <a name="high-availability-for-application-servers"></a>應用程式伺服器的高可用性

對於 BI 和 Web 應用程式伺服器，不論是個別安裝還是一起安裝，都不需要特定的高可用性解決方案。 您可以透過冗余來達到高可用性，也就是在各種 Azure 虛擬機器中設定多個 BI 和 Web 服務器實例。

若要減少由於一或多個事件而造成停機的影響，建議您遵循下列高可用性作法，讓應用程式伺服器在多部虛擬機器上執行。

- 使用可用性區域來保護資料中心失敗。
- 在可用性設定組中設定多個虛擬機器以進行冗余。
- 針對可用性設定組中的 Vm 使用受控磁碟。
- 將每個應用層設定為個別的可用性設定組。

如需詳細資訊，請參閱 [管理 Linux 虛擬機器的可用性](../../manage-availability.md)

#### <a name="high-availability-for-cms-database"></a>CMS 資料庫的高可用性

如果您使用的是 Azure 資料庫即服務 (適用于 CMS 資料庫的 DBaaS) Service，預設會提供高可用性架構。 您只需要選取區域和服務的固有高可用性、冗余和復原功能，您就不需要設定任何其他元件。 如需 Azure 上所支援 DBaaS 供應專案 SLA 的詳細資訊，請查看 [適用於 MySQL 的 Azure 資料庫的高可用性](../../../mysql/concepts-high-availability.md) 和 [Azure SQL Database 的高可用性](../../../azure-sql/database/high-availability-sla.md)

針對 CMS 資料庫的其他 DBMS 部署，請參閱 [適用于 SAP 工作負載的 dbms 部署指南](dbms_guide_general.md)，其中提供有關不同 DBMS 部署的深入解析，以及達成高可用性的方法。

#### <a name="high-availability-for-file-repository-server"></a>檔案存放庫伺服器的高可用性

檔案存放庫伺服器 (FRS) 指的是儲存報表、universe 和連接等內容的磁碟目錄。 它會在該系統的所有應用程式伺服器之間共用。 因此，您必須確定它具有高可用性。

在 Azure 上，您可以選擇 [Azure Premium](../../../storage/files/storage-files-introduction.md) 檔案或適用于檔案共用的 [azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) ，其設計為高度可用且本質上為高耐久性。 如需詳細資訊，請參閱 Azure 檔案儲存體的 [ [冗余](../../../storage/files/storage-files-planning.md#redundancy) ] 區段。

> [!NOTE]
> 適用于 Azure 檔案儲存體的 SMB 通訊協定已正式運作，但 Azure 檔案儲存體的 NFS 通訊協定支援目前為預覽狀態。 如需詳細資訊，請參閱 [Azure 檔案儲存體的 NFS 4.1 支援現供預覽](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

因為此檔案共用服務無法在所有區域中使用，請確定您參考 [區域網站提供的產品](https://azure.microsoft.com/en-us/global-infrastructure/services/) ，以找出最新的資訊。 如果您的區域中無法使用此服務，您可以建立 NFS 伺服器，讓您可以將檔案系統與 SAP BOBI 應用程式共用。 但您也必須考慮其高可用性。

#### <a name="high-availability-for-load-balancer"></a>負載平衡器的高可用性

若要將流量分散到 web 伺服器上，您可以使用 Azure Load Balancer 或 Azure 應用程式閘道。 您可以根據您為部署選擇的 SKU 來達成任何負載平衡器的冗余。

- 針對 Azure Load Balancer，可將 Standard Load Balancer 前端設定為區域冗余來達成冗余。 如需詳細資訊，請參閱 [Standard Load Balancer 和可用性區域](../../../load-balancer/load-balancer-standard-availability-zones.md)
- 針對應用程式閘道，您可以根據部署期間選取的層類型來達成高可用性。
  - 當您已部署兩個以上的實例時，v1 SKU 可支援高可用性案例。 Azure 會將這些執行個體分散到更新和容錯網域，以確保執行個體不會全部同時失敗。 因此，在此 SKU 中，可以在區域內達到冗余
  - v2 SKU 會自動確保新的實例會分散到多個容錯網域和更新網域。 如果您選擇區域備援，則最新的執行個體也會分散到各個可用性區域，以支援區域失敗復原能力。 如需詳細資訊，請參閱自動調整 [和區域多餘的應用程式閘道 v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>參考 SAP BusinessObjects BI 平臺的高可用性架構

以下的參考架構說明如何使用可用性設定組來設定 SAP BOBI Platform，以提供區域內的 Vm 冗余和可用性。 此架構會示範如何使用不同的 Azure 服務，例如 Azure 應用程式閘道、Azure NetApp Files 和適用于 SAP BOBI Platform 的適用於 MySQL 的 Azure 資料庫，以提供內建的冗余，進而降低管理不同高可用性解決方案的複雜度。

在下圖中，連入流量 (HTTPS-TCP/443) 使用 Azure 應用程式閘道 v1 SKU 來進行負載平衡，這在部署于兩個或多個實例上是高可用性。 Web 服務器、管理伺服器和處理伺服器的多個實例會部署在不同的虛擬機器中，以達到冗余，而且每一層都會部署在不同的可用性設定組中。 Azure NetApp Files 在資料中心內有內建的冗余功能，因此您的 ANF 磁片區可供檔案存放庫伺服器使用。 CMS 資料庫會布建在具有固有高可用性適用於 MySQL 的 Azure 資料庫 (DBaaS) 上。 如需詳細資訊，請參閱 [適用於 MySQL 的 Azure 資料庫指南中的高可用性](../../../mysql/concepts-high-availability.md) 。

![使用可用性設定組的 SAP BusinessObjects BI 平臺冗余](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上述架構可讓您深入瞭解 Azure 上的 SAP BOBI 部署如何完成。 但它並未涵蓋 Azure 上的 SAP BOBI Platform 所有可能的設定選項。 客戶可以根據其商務需求量身訂做部署，方法是為不同的元件選擇不同的產品/服務，例如 Load Balancer、檔案儲存機制伺服器和 DBMS。

在數個 Azure 區域中，會提供可用性區域，這表示它具有獨立的電源來源、冷卻和網路供應。 它可讓客戶跨二或三個可用性區域部署應用程式。 如果客戶想要跨 Az 達成高可用性，可以跨可用性區域部署 SAP BOBI 平臺，以確保應用程式中的每個元件都是區域冗余。

### <a name="disaster-recovery"></a>災害復原

本節中的指示說明針對 SAP BOBI Platform 提供嚴重損壞修復保護的策略。 它可補充 [sap](../../../site-recovery/site-recovery-sap.md) 檔的嚴重損壞修復，這代表整體 sap 嚴重損壞修復方法的主要資源。

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>適用于 SAP BusinessObjects BI 平臺的參考嚴重損壞修復架構

此參考架構會使用重複的應用程式伺服器來執行 SAP BOBI Platform 的多重實例部署。 針對嚴重損壞修復，您應該將所有層容錯移轉至次要區域。 每一層都會使用不同的策略來提供嚴重損壞修復保護。

![SAP BusinessObjects BI 平臺嚴重損壞修復](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>負載平衡器

Load Balancer 是用來將流量分散到 SAP BOBI 平臺的 Web 應用程式伺服器上。 若要達到 Azure 應用程式閘道的 DR，請在次要區域上執行應用程式閘道的平行設定。

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>執行 web 和 BI 應用程式伺服器的虛擬機器

Azure Site Recovery 服務可以用來複寫在次要區域上執行 Web 和 BI 應用程式伺服器的虛擬機器。 它會複寫次要區域上的伺服器，以便在發生災難和中斷時，您可以輕鬆地容錯移轉至複寫的環境並繼續工作

#### <a name="file-repository-servers"></a>檔案存放庫伺服器

- **Azure NetApp Files** 提供 NFS 和 SMB 磁片區，因此可以使用任何以檔案為基礎的複製工具，在 Azure 區域之間複寫資料。 如需如何在另一個區域中複製 ANF 磁片區的詳細資訊，請參閱 [關於 Azure NetApp Files 的常見問題](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  您可以使用 Azure NetApp Files 跨區域複寫，此複寫目前為使用 NetApp SnapMirror®技術的 [預覽](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) 版本。 因此，只有變更的區塊會以壓縮、有效率的格式透過網路傳送。 這項專屬技術可將跨區域複寫所需的資料量降到最低，以節省資料傳輸成本。 它也會縮短複寫時間，讓您可以達成較小的還原點目標 (RPO) 。 如需詳細資訊，請參閱 [使用跨區域複寫的需求和考慮](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) 。

- **Azure premium** 檔案僅支援在本機重複 (LRS) 和區域冗余儲存體 (ZRS) 。 針對 Azure Premium 檔案 DR 策略，您可以使用 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 或 [Azure PowerShell](/powershell/module/az.storage/) 將檔案複製到不同區域中的另一個儲存體帳戶。 如需詳細資訊，請參閱嚴重損壞 [修復和儲存體帳戶容錯移轉](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS 資料庫

如果發生任何嚴重損壞，適用於 MySQL 的 Azure 資料庫提供多個選項來復原資料庫。 選擇適用于您企業的適當選項。

- 啟用跨區域讀取複本以增強您的商務持續性和嚴重損壞修復規劃。 您可以從來源伺服器複製到最多五個複本。 讀取複本會使用 MySQL 的二進位記錄複寫技術以非同步方式更新。 複本是新伺服器，管理方式類似於一般適用於 MySQL 的 Azure 資料庫伺服器。 深入瞭解讀取複本、可用區域、限制，以及如何從 [讀取複本概念文章](../../../mysql/concepts-read-replicas.md)進行容錯移轉。

- 使用適用於 MySQL 的 Azure 資料庫的異地還原功能，以使用地理區域冗余備份來還原伺服器。 即使您的伺服器裝載所在的區域處於離線狀態，也可以存取這些備份。 您可以從這些備份還原至任何其他區域，並讓您的伺服器恢復上線。

  > [!NOTE]
  > 只有使用異地備援備份儲存體來佈建伺服器時，才可進行異地還原。 不支援在建立伺服器之後變更 **備份冗余選項** 。 如需詳細資訊，請參閱 [備份冗余](../../../mysql/concepts-backup.md#backup-redundancy-options) 文章。

以下是此範例中所使用之每一層的嚴重損壞修復建議。

| SAP BOBI 平台層級   | 建議                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure 應用程式閘道 | 次要區域上的應用程式閘道平行設定                                                |
| Web 應用程式伺服器   | 使用 Site Recovery 進行複寫                                                                         |
| BI 應用程式伺服器    | 使用 Site Recovery 進行複寫                                                                         |
| Azure NetApp Files        | 以檔案為基礎的複製工具，可將資料複寫到次要區域 **或** ANF 跨區域複寫 (預覽)  |
| 適用於 MySQL 的 Azure 資料庫  | 跨區域讀取複本 **或** 從異地冗余備份還原備份。                             |

## <a name="next-steps"></a>後續步驟

- [設定多層式 SAP 應用程式部署的嚴重損壞修復](../../../site-recovery/site-recovery-sap.md)
- [適用於 SAP 的 Azure 虛擬機器規劃和實作](planning-guide.md)
- [適用於 SAP 的 Azure 虛擬機器部署](deployment-guide.md)
- [適用於 SAP 的 Azure 虛擬機器 DBMS 部署](./dbms_guide_general.md)
