---
title: 使用 HSR 和 Pacemaker 在 RHEL 上進行相應放大 | SAP HanaMicrosoft Docs
description: 在 RHEL 上使用 HSR 和 Pacemaker SAP Hana 向外延展
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: c97975d6920cd0f04a7d2d4e73c00104a2b13235
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685607"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上 SAP Hana 相應放大系統的高可用性 

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


本文說明如何使用 HANA 系統複寫，在相應放大設定中部署高可用性 SAP Hana 系統 (HSR) 和 Azure 上的 Pacemaker Red Hat Enterprise Linux 虛擬機器 (Vm) 。 所呈現架構中的共用檔案系統會由 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 提供，並透過 NFS 掛接。  

在範例設定、安裝命令等等中，HANA 實例為 **03** ，而 HANA 系統識別碼是 **HN1**。 這些範例是以 HANA 2.0 SP4 和 SAP 7.6 Red Hat Enterprise Linux 為基礎。 

開始之前，請參閱下列 SAP 附注和論文：

* SAP 附注 [1928533] 包含：  
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上的 Windows 和 Linux 所需的 SAP 核心版本
* SAP 附注 [2015553]：列出 AZURE 中 sap 支援的 sap 軟體部署的必要條件
* SAP Note [2002167] 有 Red Hat Enterprise Linux 的建議作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP 附注 [2178632]：包含 Azure 中針對 SAP 回報的所有監視計量的詳細資訊
* SAP 附注 [2191498]：包含 Azure 中 Linux 所需的 SAP Host Agent 版本
* SAP 附注 [2243692]：包含 Azure 中 LINUX 上 sap 授權的相關資訊
* SAP 附注 [1999351]：包含適用于 SAP 的 Azure 增強型監視延伸模組的其他疑難排解資訊
* SAP 附注 [1900823]：包含 SAP Hana 儲存體需求的相關資訊
* [Sap 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含適用于 Linux 的所有必要 SAP 附注
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SAP Hana 網路需求](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux 網路功能指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [如何? 在 NFS 共用上使用 HANA 檔案系統在 Pacemaker 叢集中設定 SAP Hana Scale-Out 系統複寫](https://access.redhat.com/solutions/5423971)
* Azure 專用 RHEL 文件：
  * [在 Red Hat Enterprise Linux 上安裝 SAP HANA 以用於 Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [適用于 SAP Hana Scale-Out 和系統複寫的 Red Hat Enterprise Linux 解決方案](https://access.redhat.com/solutions/4386601)
* [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式][anf-sap-applications-azure]
* [Azure NetApp Files 文件][anf-azure-doc] 


## <a name="overview"></a>概觀

針對 HANA 向外延展安裝達成 HANA 高可用性的其中一個方法是設定 HANA 系統複寫，並使用 Pacemaker 叢集保護解決方案，以允許自動容錯移轉。 當作用中節點失敗時，叢集會將 HANA 資源容錯移轉至另一個網站。  
顯示的設定會在每個網站上顯示三個 HANA 節點，加上多數 maker 節點，以防止分裂式案例。 您可以調整指示，以包含更多 Vm 作為 HANA DB 節點。  

顯示架構中的 HANA 共用檔案系統 `/hana/shared` 是由 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)所提供。 它是透過 Nfsv4.1 4.1 在相同 HANA 系統複寫網站中的每個 HANA 節點上掛接。 檔案系統 `/hana/data` 和 `/hana/log` 本機檔案系統，並不會在 HANA DB 節點之間共用。 SAP Hana 將會以非共用模式安裝。 

> [!TIP]
> 如需建議的 SAP Hana 儲存體設定，請參閱 [SAP Hana Azure vm 儲存體](./hana-vm-operations-storage.md)設定。   

[![使用 HSR 和 Pacemaker 叢集 SAP Hana 相應放大](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

在上圖中，在一個 Azure 虛擬網路內表示三個子網，並遵循 SAP Hana 的網路建議： 
* 針對用戶端通訊- `client` 10.23.0.0/24  
* 內部 HANA 節點間通訊- `inter` 10.23.1.128/26  
* 針對 HANA 系統複寫- `hsr` 10.23.1.192/26  

當 `/hana/data` 和 `/hana/log` 都部署在本機磁片上時，不需要部署個別的子網和個別的虛擬網路卡來與儲存體通訊。  

Azure NetApp 磁片區會部署在個別的子網中，[委派給 Azure NetApp Files] (https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26。   

## <a name="set-up-the-infrastructure"></a>設定基礎結構

在接下來的指示中，我們假設您已建立資源群組、具有三個 Azure 網路子網的 Azure 虛擬網路： `client` `inter` 和 `hsr` 。

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>透過 Azure 入口網站部署 Linux 虛擬機器
1. 部署 Azure Vm。  
針對本檔中顯示的設定，部署七部虛擬機器： 
   - 三部虛擬機器作為 HANA 複寫網站1的 HANA DB 節點： **hana-s1-db1**、 **hana-s1-db2** 和 **hana-s1-db3**  
   - 三部虛擬機器作為 HANA 複寫網站2的 HANA DB 節點： **hana-s2-db1**、 **hana-s2-db2** 和 **HANA-s2-db3**  
   - 作為 *多數 maker* 的小型虛擬機器： **hana-s-mm**

   部署為 SAP DB HANA 節點的 Vm 應受 SAP for HANA 認證，如同在 [SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中發佈一樣。 部署 HANA DB 節點時，請確定已選取 [ [加速網路](../../../virtual-network/create-vm-accelerated-networking-cli.md) ]。  
  
   針對大部分的 maker 節點，您可以部署小型 VM，因為此 VM 不會執行任何 SAP Hana 資源。 在叢集設定中，大部分的 maker VM 會用來在分裂的情況下達到奇數的叢集節點數目。 在此範例中，多數 maker VM 只需要子網中的一個虛擬網路介面 `client` 。        

   部署和的本機受控 `/hana/data` 磁片 `/hana/log` 。 的最小建議儲存體設定 `/hana/data` ， `/hana/log` 在 [SAP Hana Azure vm 儲存體](./hana-vm-operations-storage.md)設定中有相關說明。

   為虛擬網路子網中的每個 VM 部署主要網路介面 `client` 。  
   當 VM 透過 Azure 入口網站部署時，會自動產生網路介面名稱。 為了簡單起見，我們將參考自動產生的主要網路介面，這些介面會附加至 `client` Azure 虛擬網路子網，以作為 **hana-s1-db1-client**、hana-s1- **db2-client**、 **hana-s1-db3-client** 等等。  


   > [!IMPORTANT]
   > 請確定您選取的作業系統已通過 SAP 認證，可 SAP Hana 您所使用的特定 VM 類型。 如需這些類型的 SAP Hana 認證的 VM 類型和作業系統版本清單，請移至 [SAP Hana 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 網站。 按一下所列 VM 類型的詳細資料，取得該類型的 SAP Hana 支援 OS 版本的完整清單。  
  

2. 建立六個網路介面，一個適用于虛擬網路子網中的每個 HANA DB 虛擬機器， `inter` (在此範例中為 **hana-s1-db1-**) **db3**、hana- **s1**---  ------------------------ **--**--------- **--**-  

3. 建立六個網路介面，其中一個用於虛擬網路子網中的每個 HANA DB 虛擬機器， `hsr` (在此範例中為 **db1-hsr**、 **hana-s1-db2-hsr**、 **hana-s1-db3-hsr**、 **hana-s2-db1-hsr**、 **hana-s2-db2-** hsr 和 **hana-s2-db3-hsr**) 。  

4. 將新建立的虛擬網路介面連結至對應的虛擬機器：  

    a. 移至 [Azure 入口網站](https://portal.azure.com/#home)中的虛擬機器。  

    b. 在左窗格中，選取 [ **虛擬機器**]。 篩選虛擬機器名稱 (例如， **db1**) ，然後選取虛擬機器。  

    c. 在 [ **總覽** ] 窗格中，選取 [ **停止** ] 將虛擬機器解除配置。  

    d. 選取 [ **網路**]，然後連接網路介面。 在 [ **附加網路介面** ] 下拉式清單中，選取已建立 `inter` 和子網的網路介面 `hsr` 。  
    
    e. 選取 [儲存]  。 
 
    f. 針對其餘的虛擬機器重複步驟 b 至 e (在我們的範例中為  **hana-s1-db2**、 **hana-s1-db3**、 **hana-s2-db1**、 **hana-s2-db2** 和 **hana-s2-db3**) 。
 
    g. 暫時讓虛擬機器處於停止狀態。 接下來，我們會為所有新連接的網路介面啟用 [加速網路](../../../virtual-network/create-vm-accelerated-networking-cli.md) 。  

5. 藉 `inter` 由執行下列步驟，為和子網的額外網路介面啟用加速網路 `hsr` ：  

    a. 開啟[Azure 入口網站](https://portal.azure.com/#home)中的[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。  

    b. 執行下列命令，以針對附加至 `inter` 和子網的其他網路介面啟用加速網路 `hsr` 。  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. 啟動 HANA DB 虛擬機器

### <a name="deploy-azure-load-balancer"></a>部署 Azure Load Balancer

1. 我們建議使用標準負載平衡器。 遵循這些設定步驟來部署標準負載平衡器：
   1. 首先，建立前端 IP 集區：

      1. 開啟負載平衡器，選取 [前端 IP 集區]，然後選取 [新增]。
      1. 輸入新前端 IP 集區的名稱 (例如 **hana-frontend**)。
      1. 將 [ **指派** ] 設定為 [ **靜態** ]，然後輸入 IP 位址 (例如 **10.23.0.18**) 。
      1. 選取 [確定]。
      1. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

   1. 接下來，建立後端集區，並將所有叢集 Vm 新增至後端集區：

      1. 開啟負載平衡器，選取 [後端集區]，然後選取 [新增]。
      1. 輸入新後端集區的名稱 (例如 **hana-backend**)。
      1. 選取 [新增虛擬機器]。
      1. 選取 [虛擬機器]。
      1. 選取 SAP Hana 叢集的虛擬機器，以及其子網的 IP 位址 `client` 。
      1. 選取 [新增]。

   1. 接下來，建立健康情況探查：

      1. 開啟負載平衡器，選取 [健康情況探查]，然後選取 [新增]。
      1. 輸入新健康情況探查的名稱 (例如 **hana-hp**)。
      1. 選取 [TCP] 通訊協定和連接埠 **62503**。 讓 [間隔] 值保持設定為 5，而讓 [狀況不良閾值] 值保持設定為 2。
      1. 選取 [確定]。

   1. 接下來，建立負載平衡規則：
   
      1. 開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
      1. 輸入新負載平衡器規則的名稱 (例如 **hana-lb**)。
      1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如，**hana-frontend**、**hana-backend** 和 **hana-hp**)。
      1. 選取 [HA 連接埠]。
      1. 將 [閒置逾時] 增加為 30 分鐘。
      1. 務必 **啟用浮動 IP**。
      1. 選取 [確定]。

   > [!IMPORTANT]
   > 負載平衡案例中的 NIC 次要 IP 設定不支援浮動 IP。 如需詳細資訊，請參閱 [Azure 負載平衡器的限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果您需要 VM 的其他 IP 位址，請部署第二個 NIC。    
   
   > [!Note]
   > 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資料，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  


   > [!IMPORTANT]
   > 請勿在位於 Azure Load Balancer 後方的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康狀態探查失敗。 將參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 如需詳細資料，請參閱[負載平衡器健康情況探查](../../../load-balancer/load-balancer-custom-probe-overview.md)。
   > 另請參閱 SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421)。  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>部署 Azure NetApp Files 基礎結構 

部署檔案系統的 ANF 磁片區 `/hana/shared` 。 `/hana/shared`每個 HANA 系統複寫網站都需要個別的磁片區。 如需詳細資訊，請參閱 [設定 Azure NetApp Files 基礎結構](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)。

在此範例中，使用下列 Azure NetApp Files 磁片區： 

* volume **HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1) 
* volume **HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2) 

## <a name="operating-system-configuration-and-preparation"></a>作業系統設定和準備

下一節中的指示前面會加上下列其中一個縮寫：
* **[A]**：適用于所有節點
* **[AH]**：適用于所有 HANA DB 節點
* **[M]**：適用于多數 maker 節點
* **[AH1]**：適用于網站1上的所有 HANA DB 節點
* **[AH2]**：適用于網站2上的所有 HANA DB 節點
* **[1]**：僅適用于 HANA DB 節點1，網站1
* **[2]**：僅適用于 HANA DB 節點1、網站2


藉由執行下列步驟來設定和準備您的作業系統：

1. **[A]** 維護虛擬機器上的主機檔案。 包含所有子網的專案。 此範例已新增下列專案 `/etc/hosts` 。  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** 安裝 NFS 用戶端套件。  

    ```yum install nfs-utils ```


3. **[AH]** 適用于 HANA 設定的 Red Hat。  

    如 <https://access.redhat.com/solutions/2447641> 和下列 SAP 附注所述，設定 RHEL：  
   - [2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP Hana DB：適用于 RHEL 8 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux：執行以 GCC 7.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>準備檔案系統
### <a name="mount-the-shared-file-systems"></a>掛接共用檔案系統

在此範例中，共用的 HANA 檔案系統會部署在 Azure NetApp Files 上，並裝載于 NFSv4 上。  

1. **[AH]** 建立 HANA 資料庫磁片區的掛接點。  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** 確認 NFS 網域設定。 請確定網域已設定為預設的 Azure NetApp Files 網域，也就是將 **`defaultv4iddomain.com`** 對應設定為無 **人**。  
   只有在使用 Azure NetAppFiles Nfsv4.1 4.1 時，才需要此步驟。  

    > [!IMPORTANT]
    > 確認在 VM 上的 `/etc/idmapd.conf` 內設定 NFS 網域，使其與 Azure NetApp Files 上的預設網域設定相符： **`defaultv4iddomain.com`** 。 若 NFS 用戶端 (即 VM) 上網域設定和 NFS 伺服器的網域設定 (即 Azure NetApp 設定) 不相符，則掛接在 VM 上 Azure NetApp 磁碟區上檔案的權限將會顯示為 `nobody`。  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** 確認 `nfs4_disable_idmapping` 。 其應設為 **Y**。若要建立 `nfs4_disable_idmapping` 所在的目錄結構，請執行掛接命令。 您將無法手動在 /sys/modules 下建立目錄，因為其存取已保留給核心/驅動程式。  
   只有在使用 Azure NetAppFiles Nfsv4.1 4.1 時，才需要此步驟。  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   如需如何變更參數的詳細資訊 `nfs4_disable_idmapping` ，請參閱 https://access.redhat.com/solutions/1749883 。

4. **[AH1]** 將共用的 Azure NetApp Files 磁片區掛接至 SITE1 HANA DB Vm。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** 將共用的 Azure NetApp Files 磁片區掛接至 SITE2 HANA DB Vm。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[AH]** 確認對應的 `/hana/shared/` 檔案系統已裝載在所有具有 NFS 通訊協定版本 **NFSV4** 的 HANA DB vm 上。  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>準備資料並記錄本機檔案系統
在呈現的設定中，檔 `/hana/data` 系統 `/hana/log` 會部署在受控磁片上，並在本機連接到每個 HANA DB VM。 您將需要執行這些步驟，以在每個 HANA DB 虛擬機器上建立本機資料和記錄磁片區。 

使用邏輯磁片區管理員設定磁片配置  **(LVM)**。 下列範例假設每個 HANA 虛擬機器都有三個連接的資料磁片，可用來建立兩個磁片區。

1. **[AH]** 列出所有可用的磁片：
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   範例輸出︰

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** 為您要使用的所有磁片建立實體磁片區：
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** 建立資料檔案的磁片區群組。 一個磁碟區群組用於記錄檔，一個用於 SAP HANA 的共用目錄：
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** 建立邏輯磁片區。 
   當您使用 `lvcreate` 卻未搭配 `-i` 參數時，會建立線性磁碟區。 建議您建立等量磁碟區以提高 I/O 效能，並將等量大小調整為 [SAP Hana VM 儲存體設定](./hana-vm-operations-storage.md)中記載的值。 `-i` 引數應該為基礎實體磁碟區的數目，`-I` 引數是等量大小。 本文件會使用 2 個實體磁碟區來作為資料磁碟區，因此 `-i` 參數引數會設定為 **2**。 資料磁片區的等量大小為 **256 KiB**。 有一個實體磁碟區作為記錄磁碟區，因此，記錄磁碟區命令中不需要明確使用 `-i` 或 `-I` 參數。  

   > [!IMPORTANT]
   > `-i`當您針對每個資料或記錄磁片區使用一個以上的實體磁片區時，請使用參數並將它設定為基礎實體磁片區的數目。 建立等量磁碟區時，請使用 `-I` 參數來指定等量大小。  
   > 請參閱 [SAP Hana VM 儲存體設定](./hana-vm-operations-storage.md)，以取得建議的儲存體設定，包括等量大小和磁碟數目。  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** 建立掛接目錄，並複製所有邏輯磁片區的 UUID：
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** 建立 `fstab` 邏輯磁片區和掛接的專案：
    ```bash
    sudo vi /etc/fstab
    ```

   在 `/etc/fstab` 檔案中插入下列行：

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   掛接新的磁碟區：

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>安裝  

在此範例中，若要在 Azure Vm 上使用 HSR 在向外延展設定中部署 SAP Hana，我們使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>準備 HANA 安裝

1. **[AH]** 在安裝 HANA 之前，請先設定根密碼。 您可以在安裝完成之後停用根密碼。 Execute as `root` 命令 `passwd` 。  

2. **[1，2]** 變更的許可權 `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** 確認您可以透過 SSH 登入此網站 **hana-s1-db2** 和 **hana-S1-Db3** 中的 HANA DB vm，而不會提示您輸入密碼。  
   如果不是這種情況，請 [使用以金鑰為基礎的驗證](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)所述的方式來交換 ssh 金鑰。  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** 確認您可以透過 SSH 登入此網站 **hana-s2-db2** 和 **hana-S2-Db3** 中的 HANA DB vm，而不會提示您輸入密碼。  
   如果不是這種情況，請 [使用以金鑰為基礎的驗證](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)所述的方式來交換 ssh 金鑰。  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** 安裝 HANA 2.0 SP4 所需的其他套件。 如需詳細資訊，請參閱 SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) （適用于 RHEL 7）。 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** 暫時停用防火牆，使其不會干擾 HANA 安裝。 完成 HANA 安裝之後，您可以重新啟用它。 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>在每個網站上的第一個節點上安裝 HANA

1. **[1]** 依照 [SAP Hana 2.0 安裝和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的指示來安裝 SAP Hana。 在接下來的指示中，我們會在網站1上的第一個節點上顯示 SAP Hana 安裝。   

   a.  `root` 從 HANA 安裝軟體目錄啟動 hdblcm 程式。 使用 `internal_network` 參數並傳遞子網的位址空間（用於內部 HANA 節點間通訊）。  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. 在提示字元中，輸入下列值：

     * 針對 **[選擇動作**]：輸入 **1** (安裝) 
     * 如需 **安裝的其他元件**：輸入 **2、3**
     * 針對安裝路徑：按下 Enter (預設為/hana/shared) 
     * 針對 **本機主機名稱**：按 enter 接受預設值
     * **您是否要將主機新增至系統？**：輸入 **n**
     * 針對 **SAP Hana 系統識別碼**：輸入 **HN1**
     * **實例號碼**[00]：輸入 **03**
     * 針對 **本機主機背景工作角色群組** [default]：按下 enter 以接受預設值
     * 針對 **[選取系統使用量/輸入索引 [4]**：輸入 **4** (自訂) 
     * 針對 **資料磁片區的位置** [/hana/data/HN1]：按下 enter 以接受預設值
     * 針對 **記錄磁片區的位置** [/hana/log/HN1]：按下 enter 以接受預設值
     * 針對 **限制最大記憶體配置嗎？** [n]：輸入 **n**
     * 針對 **主機 hana-s1-db1** [hana-s1-db1] 的憑證主機名稱：按 enter 接受預設值
     * 針對 **SAP 主機代理程式使用者 (sapadm) 密碼**：輸入密碼
     * 若要 **確認 SAP 主機代理程式使用者 (sapadm) 密碼**：輸入密碼
     * **系統管理員 (hn1adm) 密碼**：輸入密碼
     * 針對 **系統管理員主目錄** [/usr/sap/HN1/home]：按下 enter 以接受預設值
     * **系統管理員登入 Shell** [/bin/sh]：按下 enter 以接受預設值
     * **系統管理員使用者識別碼**[1001]：按下 enter 以接受預設值
     * 針對 [ **使用者群組的輸入識別碼] (sapsys)** [79]：按下 enter 以接受預設值
     * **系統資料庫使用者 (系統) 密碼**：輸入系統的密碼
     * 若要 **確認系統資料庫使用者 (系統) 密碼**：輸入系統的密碼
     * **電腦重新開機後重新開機系統？** [n]：輸入 **n** 
     * 如果 **您想要繼續 (y/n)**：驗證摘要，如果一切看起來正確，請輸入 **y**

2. **[2]** 重複上述步驟，在網站2上的第一個節點上安裝 SAP Hana。   

3. **[1，2]** 確認 global.ini  

   顯示 global.ini，並確定內部 SAP Hana 節點間通訊的設定已就緒。 確認 [ **通訊** ] 區段。 它應該要有子網的位址空間 `inter` ，而且 `listeninterface` 應該設定為 `.internal` 。 確認 **internal_hostname_resolution** 區段。 它應該有屬於子網的 HANA 虛擬機器的 IP 位址 `inter` 。  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1，2]** `global.ini` 在非共用環境中準備安裝，如 SAP 附注 [2080991](https://launchpad.support.sap.com/#/notes/0002080991)所述。  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1，2]** 重新開機 SAP Hana 以啟用變更。  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1，2]** 確認用戶端介面將使用子網中的 IP 位址 `client` 進行通訊。  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   如需有關如何驗證設定的詳細資訊，請參閱 SAP 附注 [2183363-SAP Hana 內部網路的](https://launchpad.support.sap.com/#/notes/2183363)設定。  

7. **[AH]** 變更資料和記錄檔目錄的許可權，以避免 HANA 安裝錯誤。  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** 安裝次要 HANA 節點。 此步驟中的範例指示適用于網站1。  
   a. 將常駐 **hdblcm** 程式啟動為 `root` 。    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. 在提示字元中，輸入下列值：

     * 針對 **[選擇動作**：輸入 **2** (新增主機]) 
     * **輸入以逗號分隔的主機名稱以新增**： hana-s1-db2、hana-s1-db3
     * 如需 **安裝的其他元件**：輸入 **2、3**
     * 針對 **[輸入根使用者名稱 [根目錄]**：按下 enter 以接受預設值
     * 適用于主控制項的 **Select role ' hana-s1-db2 ' [1]**： 1 (用於背景工作) 
     * 針對 **主機 ' hana-s1-db2 ' 的輸入主機容錯移轉群組 [預設]**：按下 enter 以接受預設值
     * 針對 **主機 ' hana-s1-db2 ' [<<assign automatically>>] 的輸入存放裝置分割區編號**：按 enter 接受預設值
     * 針對 **主機 ' hana-s1-db2 ' 的輸入背景工作角色群組 [預設]**：按下 enter 以接受預設值
     * 針對適用于 **主機 ' hana-s1-db3 ' [1] 的 Select 角色**，背景工作) 1 (
     * 針對 **主機 ' hana-s1-db3 ' [預設] 的輸入主機容錯移轉群組**：按下 enter 以接受預設值
     * 針對 **主機 ' hana-s1-db3 ' [<<assign automatically>>] 的輸入存放裝置分割區編號**：按下 enter 接受預設值
     * 若為 **主機 ' hana-s1-db3 ' [預設] 的輸入背景工作角色群組**，請按 enter 以接受預設值。
     * **系統管理員 (hn1adm) 密碼**：輸入密碼
     * 針對 [ **輸入 SAP 主機代理程式使用者] (sapadm) 密碼**：輸入密碼
     * 若要 **確認 SAP 主機代理程式使用者 (sapadm) 密碼**：輸入密碼
     * 針對 **主機 hana-s1-db2 的憑證主機名稱** [hana-s1-db2]：按 enter 接受預設值
     * 針對 **主機 hana-s1-db3** [hana-s1-db3] 的憑證主機名稱：按 enter 接受預設值
     * 如果 **您想要繼續 (y/n)**：驗證摘要，如果一切看起來正確，請輸入 **y**

9. **[2]** 重複上述步驟，在網站2上安裝次要 SAP Hana 節點。   

## <a name="configure-sap-hana-20-system-replication"></a>設定 SAP HANA 2.0 系統複寫

1. **[1]** 在網站1上設定系統複寫：

   將資料庫備份為 **hn1** adm：

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   將系統 PKI 檔案複製到次要網站：

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   建立主要網站：

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** 在網站2上設定系統複寫：
    
   註冊第二個網站以啟動系統複寫。 以 <hanasid\>adm 身分執行下列命令：

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** 檢查複寫狀態

   檢查複寫狀態，並等到所有資料庫都同步為止。

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1，2]** 變更 hana 設定，以便在透過 hana 系統複寫虛擬網路介面導向 hana 系統複寫時進行通訊。   
   - 在兩個網站上停止 HANA
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - 編輯 global.ini 以新增 HANA 系統複寫的主機對應：使用子網中的 IP 位址 `hsr` 。  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - 在兩個網站上啟動 HANA
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   如需詳細資訊，請參閱 [系統複寫的主機名稱解析](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)。  

5. **[AH]** 重新啟用防火牆。  
   - 重新啟用防火牆
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - 開啟必要的防火牆埠。 您將需要調整 HANA 實例編號的埠。  

       > [!IMPORTANT]
       > 建立防火牆規則，以允許 HANA 節點間通訊和用戶端流量。 [所有 SAP 產品的 TCP/IP 通訊埠](https://help.sap.com/viewer/ports)會列出必要的連接埠。 下列命令只是一個範例。 在此案例中，使用了系統編號03。

       ```bash
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
       ```

## <a name="create-a-pacemaker-cluster"></a>建立 Pacemaker 叢集

遵循[在 Azure 的 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 HANA 伺服器的基本 Pacemaker 叢集。
包含所有虛擬機器，包括叢集中的多數 maker。  

> [!IMPORTANT]
> 請勿設定 `quorum expected-votes` 為2，因為這不是兩個節點的叢集。  
> 請確定已啟用 [叢集] 屬性 `concurrent-fencing`  ，以便還原序列化節點隔離。   

## <a name="create-file-system-resources"></a>建立檔案系統資源

1. **[1，2]** 停止兩個複寫網站上的 SAP Hana。 以 <sid adm 的形式執行 \> 。  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[AH]** 卸載檔案系統 `/hana/shared` ，這會暫時掛接到所有 HANA DB vm 上的安裝。 您必須停止任何使用檔案系統的進程和會話，才能取消掛接。 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** 建立 `/hana/shared` 處於停用狀態的檔案系統叢集資源。 系統會使用選項來建立資源 `--disabled` ，因為您必須先定義位置條件約束，才能啟用裝載。  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` 屬性會加入至監視作業，讓監視作業在檔案系統上執行讀取/寫入測試。 如果沒有這個屬性，監視器作業只會驗證檔案系統是否已裝載。 這可能會造成問題，因為當連線中斷時，檔案系統可能會維持掛接狀態，儘管無法存取。  

   `on-fail=fence` 屬性也會新增至監視作業。 使用這個選項時，如果節點上的監視作業失敗，則會立即隔離該節點。 如果沒有此選項，預設行為是停止相依于失敗資源的所有資源，然後重新開機失敗的資源，然後啟動相依于失敗資源的所有資源。 當 SAPHana 資源相依于失敗的資源時，這種行為不只會花很長的時間，但它也可以全部失敗。 如果無法存取保存 HANA 二進位檔的 NFS 共用，SAPHana 資源就無法順利停止。  

4. **[1]** 設定並確認節點屬性。 複寫網站1上的所有 SAP Hana DB 節點都會被指派屬性 `S1` ，而複寫網站2上的所有 SAP HANA db 節點都會被指派屬性 `S2` 。  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** 設定條件約束，以決定裝載 NFS 檔案系統的位置，並啟用檔案系統資源。  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   當您啟用檔案系統資源時，叢集將會掛接 `/hana/shared` 檔案系統。
 
6. **[AH]** 確認 ANF 磁片區已裝載在 `/hana/shared` 兩個網站上的所有 HANA DB vm 下。

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** 設定屬性資源。 設定條件約束， `true` 如果裝載的 NFS 裝載，則會將屬性設定為 `hana/shared` 。  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 如果您的設定包含其他檔案系統（除了/ `hana/shared` ，也就是裝載 NFS），則包含 `sequential=false` 選項，因此檔案系統之間沒有順序相依性。 所有 NFS 掛接的檔案系統在對應的屬性資源之前都必須先啟動，但不需要以相對於彼此的任何順序啟動。 如需詳細資訊，請參閱 [當 HANA 檔案系統為 NFS 共用時，如何? 在 pacemaker 叢集中設定 SAP Hana Scale-Out HSR](https://access.redhat.com/solutions/5423971)。  

8. **[1]** 將 pacemaker 置於維護模式，以準備建立 HANA 叢集資源。  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>建立 SAP HANA 叢集資源

1. **[A]** 在所有叢集節點上安裝 HANA 相應放大資源代理程式，包括多數 maker。    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > 請參閱 [RHEL HA 叢集的支援原則-管理叢集中的 SAP Hana](https://access.redhat.com/articles/3397471) ，以取得作業系統版本的最低支援套件版本 `resource-agents-sap-hana-scaleout` 。  

2. **[1，2]** 安裝 HANA 「系統複寫攔截」。 您必須在每個系統複寫網站上的一個 HANA DB 節點上安裝攔截。 SAP Hana 應該仍會關閉。        

   1. 準備掛勾 `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 調整 `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[AH]** 叢集需要 <sid adm 的叢集節點上的 sudoers 設定 \> 。 在此範例中，您可以藉由建立新的檔案來達成。 以執行命令 `root` 。    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1，2]** 在兩個複寫網站上啟動 SAP Hana。 以 <sid adm 的形式執行 \> 。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** 確認攔截安裝。 在作用中 \> 的 HANA 系統複寫網站上以 <sid adm 的形式執行。   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** 建立 HANA 叢集資源。 以執行下列命令 `root` 。    
   1. 請確定叢集已處於維護模式。  
    
   2. 接下來，建立 HANA 拓撲資源。  
      如果 **建立 RHEL 7.x** 叢集，請使用下列命令：  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      如果 **建立 RHEL 8.x** 叢集，請使用下列命令：  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. 接下來，建立 HANA 實例資源。  
      > [!NOTE]
      > 本文包含詞彙 *從屬* 的參考，這是 Microsoft 不再使用的詞彙。 從軟體中移除該字詞時，我們也會將其從本文中移除。  
 
      如果 **建立 RHEL 7.x** 叢集，請使用下列命令：    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      如果 **建立 RHEL 8.x** 叢集，請使用下列命令：  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > 建議您最好在執行徹底的容錯移轉測試時，只將 AUTOMATED_REGISTER 設定為 [ **否**]，以防止失敗的主要實例自動註冊為次要。 當容錯移轉測試成功完成後，請將 AUTOMATED_REGISTER 設定為 **[是]**，如此一來，接管系統複寫之後就可以自動繼續。 

   4. 建立虛擬 IP 和相關聯的資源。  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. 建立叢集條件約束  
      如果 **建立 RHEL 7.x** 叢集，請使用下列命令：  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      如果 **建立 RHEL 8.x** 叢集，請使用下列命令：  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** 將叢集移出維護模式。 請確定叢集狀態正常，且所有資源皆已啟動。  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 上述設定中的逾時只是例子，可能需要針對特定的 HANA 安裝來調整。 比方說，如果啟動 SAP Hana 資料庫需要較長的時間，您可能需要延長啟動逾時。
  
## <a name="test-sap-hana-failover"></a>測試 SAP Hana 容錯移轉 

1. 開始測試之前，請先檢查叢集並 SAP Hana 系統複寫狀態。  

   a. 確認沒有任何失敗的叢集動作  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. 確認 SAP Hana 系統複寫是否同步

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. 當節點無法存取 NFS 共用 () 時，請確認叢集設定是否發生失敗案例 `/hana/shared` 。  

   SAP Hana 資源代理程式相依于二進位檔，並儲存在上， `/hana/shared` 以在容錯移轉期間執行作業。 檔案系統 `/hana/shared` 會透過 NFS 裝載在呈現的設定中。 可以執行的測試是以 `/hana/shared` *唯讀* 方式重新掛接檔案系統。 這種方法會驗證叢集將會容錯移轉，如果在使用中 `/hana/shared` 的系統複寫網站上遺失對的存取。  

   **預期的結果**：當您重新掛接 `/hana/shared` 為 *唯讀* 時，在檔案系統上執行讀取/寫入作業的監視作業將會失敗，因為它無法寫入檔案系統，而且將會觸發 HANA 資源容錯移轉。 當您的 HANA 節點失去 NFS 共用的存取權時，預期會有相同的結果。  
     
   您可以藉由執行或來檢查叢集資源的 `crm_mon` 狀態 `pcs status` 。 開始測試之前的資源狀態：
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   若要 `/hana/shared` 在其中一個主要複寫網站 vm 上模擬失敗，請執行下列命令：
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   因為叢集設定的不同，所以不能存取的 HANA VM `/hana/shared` 應重新開機或停止。 叢集資源會遷移至其他 HANA 系統複寫網站。  
         
   如果已重新開機的 VM 上未啟動叢集，請執行下列動作來啟動叢集： 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   當叢集啟動時，檔案系統 `/hana/shared` 將會自動載入。     
   如果您設定 AUTOMATED_REGISTER = "false"，您將需要在次要網站上設定 SAP Hana 系統複寫。 在此情況下，您可以執行這些命令，將 SAP Hana 重新設定為次要。   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   測試之後的資源狀態： 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


建議您在 [Azure vm 上的 Azure vm 上，針對 SAP Hana 的 HA](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)執行測試，以徹底測試 SAP Hana 叢集設定。


## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要瞭解如何為 Azure Vm 上的 SAP Hana 建立高可用性和規劃以進行嚴重損壞修復，請參閱 [Azure 虛擬機器 (vm) 上 SAP Hana 的高可用性 ][sap-hana-ha]。