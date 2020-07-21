---
title: 使用 RHEL 上的 Azure NetApp Files 以待命方式 SAP Hana 相應放大 |Microsoft Docs
description: 在 Red Hat Enterprise Linux 上使用 sap NetWeaver 的高可用性指南，適用于 SAP 應用程式的 Azure NetApp Files
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/15/2020
ms.author: radeltch
ms.openlocfilehash: e17dcf02c920dc1a824a165fb3d667833267cac7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507500"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點 \(部分機器翻譯\) 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文說明如何使用適用于共用存放磁片區的[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) ，在 azure Red Hat Enterprise Linux 虛擬機器（vm）上使用待命部署向外延展設定中的高可用性 SAP Hana 系統。  

在範例設定、安裝命令等等中，HANA 實例是**03** ，而 HANA 系統識別碼是**HN1**。 這些範例是以 HANA 2.0 SP4 和 SAP 7.6 Red Hat Enterprise Linux 為基礎。 

開始之前，請參閱下列 SAP 附注和論文：

* [Azure NetApp Files 文件][anf-azure-doc] 
* SAP 附注[1928533]包括：  
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* SAP 附注[2015553]：列出 AZURE 中 sap 支援的 sap 軟體部署的必要條件
* SAP 附注 [2002167] 具有 Red Hat Enterprise Linux 的建議作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP 附注[2178632]：包含在 Azure 中針對 SAP 回報的所有監視計量的詳細資訊
* SAP 附注[2191498]：包含 Azure 中適用于 Linux 的必要 SAP 主機代理程式版本
* SAP 附注[2243692]：包含 Azure 中 Linux 上的 sap 授權的相關資訊
* SAP 附注[1999351]：包含適用于 SAP 的 Azure 增強式監視擴充功能的其他疑難排解資訊
* SAP 附注[1900823]：包含 SAP Hana 儲存體需求的相關資訊
* [Sap 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含適用于 Linux 的所有必要 SAP 附注
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux 網路功能指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure 專用 RHEL 文件：
  * [在 Red Hat Enterprise Linux 上安裝 SAP HANA 以用於 Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式][anf-sap-applications-azure]


## <a name="overview"></a>概觀

達到 HANA 高可用性的其中一個方法是設定主機自動容錯移轉。 若要設定主機自動容錯移轉，您可以將一或多個虛擬機器新增至 HANA 系統，並將它們設定為待命節點。 當作用中節點失敗時，待命節點會自動接管。 在 Azure 虛擬機器的呈現設定中，您可以使用[Azure NetApp Files 上的 NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)來達成自動容錯移轉。  

> [!NOTE]
> 待命節點需要所有資料庫磁片區的存取權。 HANA 磁片區必須裝載為 NFSv4 磁片區。 NFSv4 通訊協定中改良的檔案租用型鎖定機制是用於隔離 `I/O` 。 

> [!IMPORTANT]
> 若要建立支援的設定，您必須將 HANA 資料和記錄磁片區部署為 NFSv 4.1 磁片區，並使用 NFSv 4.1 通訊協定掛接它們。 NFSv3 不支援具有待命節點的 HANA 主機自動容錯移轉設定。

![SAP NetWeaver 高可用性概觀](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

在上圖中，會遵循 SAP Hana 網路建議，在一個 Azure 虛擬網路中表示三個子網： 
* 針對用戶端通訊
* 與儲存系統通訊
* 適用于內部 HANA 節點間通訊

Azure NetApp 磁片區位於個別的子網中，並[委派給 Azure Netapp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)。  

針對此範例設定，子網為：  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 （委派的子網至 Azure NetApp Files）

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>設定 Azure NetApp Files 基礎結構 

在您繼續進行 Azure NetApp Files 基礎結構的設定之前，請先熟悉[Azure Netapp files][anf-azure-doc]檔。 

Azure NetApp Files 在數個[azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中都有提供。 查看您選取的 Azure 區域是否提供 Azure NetApp Files。  

如需有關 azure 區域的 Azure NetApp Files 可用性的詳細資訊，請參閱 azure [Netapp files By Azure 區域的可用性][anf-avail-matrix]。  

部署 Azure NetApp Files 之前，請前往[註冊 Azure Netapp files 指示][anf-register]，要求上線至 Azure netapp files。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp Files 資源  

下列指示假設您已部署您的[Azure 虛擬網路](../../../virtual-network/virtual-networks-overview.md)。 將裝載 Azure NetApp Files 資源的 Azure NetApp Files 資源和 Vm，必須部署在相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。  

1. 如果您尚未部署資源，請要求上[架至 Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)。  

2. 遵循[建立 netapp 帳戶](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的指示，在您選取的 Azure 區域中建立 netapp 帳戶。  

3. 遵循[設定 Azure Netapp files 容量](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)集區中的指示，設定 Azure netapp files 容量集區。  

   本文中所提供的 HANA 架構會使用*Ultra 服務*層級的單一 Azure NetApp Files 容量集區。 針對 Azure 上的 HANA 工作負載，我們建議使用 Azure NetApp Files *Ultra*或*Premium* [服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。  

4. 將子網委派給 Azure NetApp Files，如將[子網委派給 Azure Netapp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的指示所述。  

5. 遵循[建立適用于 Azure Netapp files 的 NFS 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)區中的指示，部署 Azure netapp files 磁片區。  

   當您要部署磁片區時，請務必選取**nfsv 4.1**版本。 在指定的 Azure NetApp Files [子網路](/rest/api/virtualnetwork/subnets)中部署磁碟區。 Azure NetApp 磁碟區的 IP 位址會自動指派。 
   
   請記住，Azure NetApp Files 資源和 Azure Vm 必須位於相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。 例如， **HN1**資料 Mnt00001、 **HN1**-log mnt00001 等等，是磁片區名稱和 nfs://10.9.0.4/**HN1**-data-mnt00001、nfs://10.9.0.4/**HN1**-Log-mnt00001 等等，是 Azure NetApp Files 磁片區的檔案路徑。  

   * 磁片區**HN1**-資料-mnt00001 （nfs://10.9.0.4/**HN1**-資料-mnt00001）
   * 磁片區**HN1**-資料-mnt00002 （nfs://10.9.0.4/**HN1**-資料-mnt00002）
   * 磁片區**HN1**-記錄-mnt00001 （nfs://10.9.0.4/**HN1**-log-mnt00001）
   * 磁片區**HN1**-記錄-mnt00002 （nfs://10.9.0.4/**HN1**-log-mnt00002）
   * 磁片區**HN1**-共用（nfs://10.9.0.4/**HN1**-共用）
   
   在此範例中，我們針對每個 HANA 資料和記錄磁片區使用個別的 Azure NetApp Files 磁片區。 若要在較小或非生產力的系統上進行更具成本優化的設定，可以將所有資料掛接到單一磁片區，並將所有記錄掛接在不同的單一磁片區上。  

### <a name="important-considerations"></a>重要考量

當您建立 Azure NetApp Files 以 SAP Hana 使用「待命節點」案例進行相應放大時，請注意下列重要考慮：

- 容量集區的最小值為 4 tib （TiB）。  
- 磁片區大小的最小值為 100 gib （GiB）。
- Azure NetApp Files 和所有將裝載 Azure NetApp Files 磁片區的虛擬機器，都必須位於相同的 Azure 虛擬網路或相同區域的[對等互連虛擬網路](../../../virtual-network/virtual-network-peering-overview.md)中。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網。
- Azure NetApp Files 磁片區的輸送量是磁片區配額和服務等級的功能，如[Azure Netapp files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 當您要調整 HANA Azure NetApp 磁片區的大小時，請確定產生的輸送量符合 HANA 系統需求。  
- 使用 Azure NetApp Files[匯出原則](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，您可以控制允許的用戶端、存取類型（讀寫、唯讀等等）。 
- Azure NetApp Files 功能尚無法感知區域。 目前，此功能不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。  

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁碟區已部署在附近。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp Files 磁片區的輸送量是磁片區大小和服務層級的功能，如[Azure Netapp files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

當您在 Azure 中設計 SAP 的基礎結構時，請留意 SAP 所需的一些最低儲存體需求，這會轉譯為最小的輸送量特性：

- 以 1 MB i/o 大小/hana/log 每秒 250 mb （MB/s）的讀寫。  
- 讀取至少 400 MB/s 的活動，適用于/hana/data 的 16 MB 和 64 MB i/o 大小。  
- 針對具有 16 MB 和 64 MB i/o 大小的/hana/data，至少需要 250 MB/s 的寫入活動。 

每 1 TiB 磁碟區配額的 [Azure NetApp Files 輸送量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)如下：
- 進階儲存體層-64 MiB/秒  
- Ultra 儲存層-128 MiB/秒  

為了符合資料和記錄的 SAP 最低輸送量需求，以及/hana/shared 的指導方針，建議的大小為：

| 磁碟區 | 大小<br>進階儲存層 | 大小<br>Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| HANA/shared | 每4個背景工作節點的1xRAM | 每4個背景工作節點的1xRAM | v3 或 4.1 版 |

本文中顯示的版面配置 SAP Hana 設定，使用 Azure NetApp Files Ultra 儲存層，將會是：

| 磁碟區 | 大小<br>Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| HANA/shared | 2 TiB | v3 或 4.1 版 |

> [!NOTE]
> 此處所述的 Azure NetApp Files 大小建議以符合 SAP 建議的基礎結構提供者的最低需求為目標。 在實際的客戶部署和工作負載案例中，這些大小可能不夠。 請將這些建議當作起點，並且根據特定工作負載的需求來調整。  

> [!TIP]
> 您可以動態調整 Azure NetApp Files 磁片區的大小，而不需要*卸載*磁片區、停止虛擬機器或停止 SAP Hana。 這種方法可讓您彈性地同時符合應用程式的預期和未預期的輸送量需求。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>透過 Azure 入口網站部署 Linux 虛擬機器

首先您需要建立 Azure NetApp Files 磁碟區。 然後執行下列步驟：
1. 在您的[azure 虛擬網路](../../../virtual-network/virtual-networks-overview.md)中建立[azure 虛擬網路子網](../../../virtual-network/virtual-network-manage-subnet.md)。 
1. 部署 VM。 
1. 建立額外的網路介面，並將網路介面連結至對應的 Vm。  

   每部虛擬機器都有三個網路介面，分別對應至三個 Azure 虛擬網路子網（ `client` `storage` 和 `hana` ）。 

   如需詳細資訊，請參閱[在具有多個網路介面卡的 Azure 中建立 Linux 虛擬機器](../../linux/multiple-nics.md)。  

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 為了達到低延遲，請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區會以接近的鄰近性進行部署。 當您將使用 SAP Hana Azure NetApp Files 的[新 SAP Hana 系統上架](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)時，請提交所需的資訊。 
 
下一個指示假設您已建立資源群組、Azure 虛擬網路和三個 Azure 虛擬網路子網： `client` `storage` 和 `hana` 。 當您部署 Vm 時，請選取用戶端子網，讓用戶端網路介面是 Vm 上的主要介面。 您也必須透過「儲存體子網閘道」，設定 Azure NetApp Files 委派子網的明確路由。 

> [!IMPORTANT]
> 請確定您選取的作業系統在您所使用的特定 VM 類型上有 SAP Hana 的 SAP 認證。 如需這些類型的 SAP Hana 認證的 VM 類型和作業系統版本清單，請移至[SAP Hana 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)網站。 按一下所列 VM 類型的詳細資料，以取得該類型的 SAP Hana 支援作業系統版本的完整清單。  

1. 建立 SAP Hana 的可用性設定組。 請務必設定更新網域上限。  

2. 執行下列步驟，以建立三部虛擬機器（**hanadb1**、 **hanadb2**、 **hanadb3**）：  

   a. 使用 Azure 資源庫中支援 SAP Hana 的 Red Hat Enterprise Linux 映射。 在此範例中，我們使用 RHEL-SAP-HA 7.6 映射。  

   b. 選取您稍早為 SAP Hana 建立的可用性設定組。  

   c. 選取用戶端 Azure 虛擬網路子網。 選取 [[加速網路](../../../virtual-network/create-vm-accelerated-networking-cli.md)]。  

   當您部署虛擬機器時，系統會自動產生網路介面名稱。 為了簡單起見，我們會參考自動產生的網路介面，其會連結至用戶端 Azure 虛擬網路子網，例如**hanadb1 客戶**端、 **hanadb2 用戶端**和**hanadb3 用戶端**。 

3. 為 `storage` 虛擬網路子網（在此範例中為**hanadb1 儲存體**、 **hanadb2 儲存體**和**hanadb3 儲存體**）建立三個網路介面，每個虛擬機器各一個。  

4. 針對 `hana` 虛擬網路子網（在此範例中為**hanadb1-hana**、 **hanadb2-hana**和**hanadb3-hana**），建立三個網路介面，每個虛擬機器各一個。  

5. 執行下列步驟，將新建立的虛擬網路介面附加至對應的虛擬機器：  

    a. 移至[Azure 入口網站](https://portal.azure.com/#home)中的虛擬機器。  

    b. 在左窗格中，選取 [**虛擬機器**]。 篩選虛擬機器名稱（例如， **hanadb1**），然後選取虛擬機器。  

    c. 在 [**總覽**] 窗格中，選取 [**停止**] 以解除配置虛擬機器。  

    d. 選取 [**網路**]，然後連接網路介面。 在 [**附加網路介面**] 下拉式清單中，為 `storage` 和子網選取已建立的網路介面 `hana` 。  
    
    e. 選取 [儲存]。 
 
    f. 針對其餘的虛擬機器（在我們的範例中為**hanadb2**和**hanadb3**），重複步驟 b 到 e。
 
    g. 暫時讓虛擬機器處於 [已停止] 狀態。 接下來，我們將為所有新連接的網路介面啟用[加速網路](../../../virtual-network/create-vm-accelerated-networking-cli.md)。  

6. `storage`執行下列步驟，為和子網的其他網路介面啟用加速網路 `hana` ：  

    a. 在[Azure 入口網站](https://portal.azure.com/#home)中開啟[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。  

    b. 執行下列命令，為附加至 `storage` 和子網的其他網路介面啟用加速網路 `hana` 。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 執行下列步驟來啟動虛擬機器：  

    a. 在左窗格中，選取 [**虛擬機器**]。 篩選虛擬機器名稱（例如， **hanadb1**），然後選取它。  

    b. 在 [**總覽**] 窗格中，選取 [**啟動**]。  

## <a name="operating-system-configuration-and-preparation"></a>作業系統設定和準備

下一節中的指示前面會加上下列其中一項：
* **[A]**：適用于所有節點
* **[1]**：僅適用于節點1
* **[2]**：僅適用于節點2
* **[3]**：僅適用于節點3

執行下列步驟來設定並準備您的 OS：

1. **[A]** 維護虛擬機器上的主機檔案。 包含所有子網的專案。 這個範例已加入下列專案 `/etc/hosts` 。  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** 新增網路路由，讓 Azure NetApp Files 的通訊會透過儲存體網路介面來進行。  

   在此範例中，將使用 `Networkmanager` 來設定額外的網路路由。 下列指示假設存放裝置網路介面為 `eth1` 。  
   首先，決定裝置的連線名稱 `eth1` 。 在此範例中，裝置的連線名稱 `eth1` 是 `Wired connection 1` 。  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   然後透過將其他路由設定至 Azure NetApp Files 委派的網路 `eth1` 。  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    重新開機 VM 以啟用變更。  

3. **[A]** 安裝 NFS 用戶端封裝。  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** 準備 OS 以在[使用 azure netapp Files 的 Microsoft Azure 上的 Netapp SAP 應用程式][anf-sap-applications-azure]中執行 SAP Hana 在 Azure netapp with NFS 上運作。 建立 NetApp configuration 設定的設定檔 */etc/sysctl.d/netapp-hana.conf* 。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 建立具有其他優化設定的設定檔 */etc/sysctl.d/ms-az.conf* 。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** 根據[Microsoft Azure 使用 Azure Netapp FILES 的 Netapp SAP 應用程式][anf-sap-applications-azure]中的建議，調整 sunrpc 設定。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** 適用于 HANA 設定的 Red Hat。

    如 SAP 附注[2292690]、 [2455582]、 [2593824]和中所述，設定 RHEL <https://access.redhat.com/solutions/2447641> 。

    > [!NOTE]
    > 如果安裝 HANA 2.0 SP04，您將需要安裝套件（ `compat-sap-c++-7` 如 SAP 附注[2593824]中所述），然後才能安裝 SAP Hana。 

## <a name="mount-the-azure-netapp-files-volumes"></a>裝載 Azure NetApp Files 磁片區

1. **[A]** 建立 HANA 資料庫磁片區的掛接點。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 在**HN1**上建立/usr/sap 的節點特定目錄-共用。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 確認 NFS 網域設定。 確認網域已設為預設 Azure NetApp Files 網域，即將 **`defaultv4iddomain.com`** 和對應設為 **nobody**。  

    > [!IMPORTANT]
    > 確認在 VM 上的 `/etc/idmapd.conf` 內設定 NFS 網域，使其與 Azure NetApp Files 上的預設網域設定相符： **`defaultv4iddomain.com`** 。 若 NFS 用戶端 (即 VM) 上網域設定和 NFS 伺服器的網域設定 (即 Azure NetApp 設定) 不相符，則掛接在 VM 上 Azure NetApp 磁碟區上檔案的權限將會顯示為 `nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 驗證 `nfs4_disable_idmapping`。 其應設為 **Y**。若要建立 `nfs4_disable_idmapping` 所在的目錄結構，請執行掛接命令。 您將無法手動在 /sys/modules 下建立目錄，因為其存取已保留給核心/驅動程式。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   如需如何變更參數的詳細資訊， `nfs4_disable_idmapping` 請參閱 https://access.redhat.com/solutions/1749883 。

6. **[A]** 掛接共用的 Azure NetApp Files 磁片區。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** 在**hanadb1**上掛接節點特定的磁片區。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 在**hanadb2**上掛接節點特定的磁片區。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 在**hanadb3**上掛接節點特定的磁片區。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 確認所有 HANA 磁片區都是以 NFS 通訊協定版本**NFSv4**來裝載。  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>安裝  

在此範例中，若要使用 Azure 的待命節點來部署向外延展設定中的 SAP Hana，我們已使用 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>準備 HANA 安裝

1. **[A]** 在 HANA 安裝之前，設定根密碼。 您可以在安裝完成後停用根密碼。 Execute as `root` 命令 `passwd` 。  

2. **[1]** 確認您可以透過 SSH 登入**hanadb2**和**hanadb3**，而不會收到輸入密碼的提示。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安裝 HANA 2.0 SP4 所需的其他套件。 如需詳細資訊，請參閱 SAP 附注[2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2]，[3]** 將 SAP Hana 和目錄的擁有權變更 `data` `log` 為**hn1**adm。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** 暫時停用防火牆，使其不會干擾 HANA 安裝。 完成 HANA 安裝之後，您可以重新啟用它。 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA 安裝

1. **[1]** 依照[SAP Hana 2.0 安裝和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的指示安裝 SAP Hana。 在此範例中，我們會使用 master、一個背景工作和一個待命節點來安裝 SAP Hana 相應放大。  

   a. 從 HANA 安裝軟體目錄啟動**hdblcm**程式。 使用 `internal_network` 參數，並傳遞用於內部 HANA 節點間通訊的子網位址空間。  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. 在提示字元中，輸入下列值：

     * 針對 **[選擇動作**]：輸入**1** （適用于安裝）
     * 如需**安裝的其他元件**：輸入**2、3**
     * 針對 [安裝路徑]：按 Enter （預設為/hana/shared）
     * 針對 [**本機主機名稱**]：按 enter 以接受預設值
     * 在 **[您要將主機新增到系統嗎？**] 底下，輸入**y**
     * **若要新增以逗號分隔的主機名稱**：輸入**hanadb2、hanadb3**
     * 若為**根使用者名稱**[根]：按 enter 以接受預設值
     * 針對 [主機] hanadb2 的角色：輸入**1** （適用于背景工作）
     * 主機 hanadb2 的**主機容錯移轉群組**[預設]：按 enter 以接受預設值
     * 針對主機 hanadb2 [<>] 的**儲存磁碟分割編號** <assign automatically> ：按 enter 以接受預設值
     * 針對 [主機 hanadb2 的背景**工作角色群組**[預設]：按 enter 以接受預設值
     * 針對 [主機 hanadb3 的**選取角色**]：輸入**2** （代表待命）
     * 主機 hanadb3 的**主機容錯移轉群組**[預設]：按 enter 以接受預設值
     * 針對 [主機 hanadb3 的背景**工作角色群組**[預設]：按 enter 以接受預設值
     * 針對**SAP Hana 系統識別碼**：輸入**HN1**
     * **實例號碼**[00]：輸入**03**
     * 針對**本機主機背景工作角色群組**[預設]：按 enter 以接受預設值
     * 針對 **[選取系統使用量/輸入索引 [4]]**：輸入**4** （適用于自訂）
     * 針對**資料磁片**區的位置 [/hana/data/HN1]：按 enter 以接受預設值
     * 針對**記錄檔磁片**區的位置 [/hana/log/HN1]：按 enter 以接受預設值
     * 是否**限制記憶體配置上限？** [n]：輸入**n**
     * 針對 [**主機 hanadb1 的憑證主機名稱**[hanadb1]]：按 enter 以接受預設值
     * 針對 [**主機 hanadb2 的憑證主機名稱**[hanadb2]]：按 enter 以接受預設值
     * 針對 [**主機 hanadb3 的憑證主機名稱**[hanadb3]]：按 enter 以接受預設值
     * 針對**系統管理員（hn1adm）密碼**：輸入密碼
     * **系統資料庫使用者（system）密碼**：輸入系統的密碼
     * 針對 **[確認系統資料庫使用者（系統）密碼**]：輸入系統的密碼
     * **電腦重新開機後是否要重新開機系統？** [n]：輸入**n** 
     * 針對 [**您要繼續嗎（y/n）**]：驗證摘要，如果一切看起來良好，請輸入**y**


2. **[1]** 確認 global.ini  

   顯示 global.ini，並確保內部 SAP Hana 節點間通訊的設定已就緒。 確認 [**通訊**] 區段。 它應該會有子網的位址空間 `hana` ，而且 `listeninterface` 應該設定為 `.internal` 。 確認**internal_hostname_resolution**區段。 它應該具有屬於子網之 HANA 虛擬機器的 IP 位址 `hana` 。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 新增主機對應，以確保用戶端 IP 位址用於用戶端通訊。 新增區段 `public_host_resolution` ，並從用戶端子網新增對應的 IP 位址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** 重新開機 SAP Hana 以啟用變更。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 確認用戶端介面將使用子網的 IP 位址 `client` 進行通訊。  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   如需有關如何驗證設定的詳細資訊，請參閱 SAP 附注[2183363-SAP Hana 內部網路的](https://launchpad.support.sap.com/#/notes/2183363)設定。  

5. **[A]** 重新啟用防火牆。  
   - 停止 HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - 重新啟用防火牆
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - 開啟必要的防火牆埠

       > [!IMPORTANT]
       > 建立防火牆規則，以允許 HANA 節點間通訊和用戶端流量。 [所有 SAP 產品的 TCP/IP 通訊埠](https://help.sap.com/viewer/ports)會列出必要的連接埠。 下列命令只是一個範例。 在此案例中，使用了系統號碼03。

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - 啟動 HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. 若要優化基礎 Azure NetApp Files 儲存體的 SAP Hana，請設定下列 SAP Hana 參數：

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**于**
   - `async_write_submit_active`**于**
   - `async_write_submit_blocks`**全部**

   如需詳細資訊，請參閱[使用 Azure Netapp Files Microsoft Azure 上的 NETAPP SAP 應用程式][anf-sap-applications-azure]。 

   從 SAP Hana 2.0 系統開始，您可以在中設定參數 `global.ini` 。 如需詳細資訊，請參閱 SAP 附注[1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   
   針對 SAP Hana 1.0 版和更早版本的 SPS12，您可以在安裝期間設定這些參數，如 SAP 附注[2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp Files 使用的儲存體具有 16 tb 的檔案大小限制。 SAP Hana 不會隱含察覺儲存體限制，而且當達到 16 TB 的檔案大小限制時，不會自動建立新的資料檔案。 當 SAP Hana 嘗試將檔案成長超過 16 TB 時，該嘗試會導致錯誤，最後在索引伺服器損毀時。 

   > [!IMPORTANT]
   > 若要防止 SAP Hana 嘗試擴大超過儲存子系統的[16 TB 限制](../../../azure-netapp-files/azure-netapp-files-resource-limits.md)的資料檔案，請在中設定下列參數 `global.ini` 。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 如需詳細資訊，請參閱 SAP 附注[2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 請留意 SAP 附注[2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>測試 SAP Hana 容錯移轉 

1. 模擬 SAP Hana 背景工作節點上的節點損毀。 執行下列動作： 

   a. 模擬節點損毀之前，請執行下列命令作為**hn1**adm，以捕捉環境的狀態：  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 若要模擬節點損毀，請在背景工作節點上以 root 身分執行下列命令，在此案例中為**hanadb2** ：  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 監視系統的容錯移轉完成。 當容錯移轉完成時，請捕捉狀態，如下所示：  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 當節點遇到核心異常時，請 `kernel.panic` 在*所有*HANA 虛擬機器上設定為20秒，以避免 SAP Hana 容錯移轉延遲。 設定是在中完成 `/etc/sysctl` 。 重新開機虛擬機器以啟用變更。 如果未執行這種變更，當節點發生核心異常時，容錯移轉可能需要10分鐘或更久的時間。  

2. 執行下列動作來終止名稱伺服器：

   a. 在測試之前，請以**hn1**adm 的身分執行下列命令來檢查環境的狀態：  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. 在使用中的主要節點上，以**hn1**adm 的形式執行下列命令，在此案例中為**hanadb1** ：  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    待命節點**hanadb3**會接管作為主要節點。 以下是容錯移轉測試完成之後的資源狀態：  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. 在**hanadb1**上重新開機 HANA 實例（也就是在已終止名稱伺服器的相同虛擬機器上）。 **Hanadb1**節點會重新加入環境，並會保留其待命角色。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**hanadb1**上啟動 SAP Hana 之後，預期會有下列狀態：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 同樣地，在目前作用中的主要節點（也就是在節點**hanadb3**上）上，刪除名稱伺服器。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   節點**hanadb1**會繼續主要節點的角色。 完成容錯移轉測試之後，狀態會如下所示：

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. 在**hanadb3**上啟動 SAP Hana，這將準備好做為待命節點。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**hanadb3**上啟動 SAP Hana 之後，狀態看起來會像下面這樣：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要瞭解如何建立高可用性並規劃 Azure Vm 上 SAP Hana 的嚴重損壞修復，請參閱[azure 虛擬機器（vm）上 SAP Hana 的高可用性][sap-hana-ha]。
