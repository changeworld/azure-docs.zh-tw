---
title: SAP HANA 擴展，在 RHEL 上使用 Azure NetApp 檔進行待機。微軟文檔
description: 紅帽企業 Linux 上的 SAP NetWeaver 高可用性指南，用於 SAP 應用程式的 Azure NetApp 檔
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
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372976"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>使用紅帽企業 Linux 上的 Azure NetApp 檔，在 Azure VM 上部署具有備用節點的 SAP HANA 橫向擴展系統 

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
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介紹如何通過將[Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)用於共用存儲卷，在 Azure 紅帽企業 Linux 虛擬機器 （VM） 上具有待機狀態的橫向擴展配置中部署高可用性 SAP HANA 系統。  

在示例配置、安裝命令等中，HANA 實例為**03，HANA**系統 ID 為**HN1**。 這些示例基於 SAP 7.6 的 HANA 2.0 SP4 和紅帽企業 Linux。 

在開始之前，請參閱以下 SAP 說明和檔：

* [Azure NetApp 檔文檔][anf-azure-doc] 
* SAP 說明[1928533]包括：  
  * 支援部署 SAP 軟體的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * 微軟 Azure 上 Windows 和 Linux 所需的 SAP 內核版本
* SAP 說明[2015553]：列出 Azure 中 SAP 支援的 SAP 軟體部署的先決條件
* SAP 說明 [2002167] 已建議為紅帽企業 Linux 設置作業系統設置
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP 注釋[2178632]：包含有關 Azure 中為 SAP 報告的所有監視指標的詳細資訊
* SAP 注釋[2191498]： 包含 Azure 中 Linux 所需的 SAP 主機代理版本
* SAP 注釋[2243692]： 包含有關 Azure 中 Linux 上的 SAP 許可的資訊
* SAP 說明[1999351]： 包含用於 SAP 的 Azure 增強監視擴展的其他故障排除資訊
* SAP 說明[1900823]： 包含有關 SAP HANA 存儲要求的資訊
* [SAP 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)： 包含 Linux 所需的所有 SAP 筆記
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [紅帽企業 Linux 網路指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* 特定于 Azure 的 RHEL 文檔：
  * [在 Red Hat Enterprise Linux 上安裝 SAP HANA 以用於 Microsoft Azure](https://access.redhat.com/solutions/3193782)
* [使用 Azure NetApp 檔在 Microsoft Azure 上使用 NetApp SAP 應用程式][anf-sap-applications-azure]


## <a name="overview"></a>總覽

實現 HANA 高可用性的一種方法是配置主機自動容錯移轉。 要配置主機自動容錯移轉，請將一個或多個虛擬機器添加到 HANA 系統，並將其配置為備用節點。 當活動節點發生故障時，備用節點會自動接管。 在 Azure 虛擬機器的顯示配置中，通過使用[Azure NetApp 檔上的 NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)實現自動容錯移轉。  

> [!NOTE]
> 備用節點需要訪問所有資料庫卷。 HANA 卷必須裝入 NFSv4 卷。 NFSv4 協定中改進的基於檔租約的鎖定機制用於`I/O`遮罩。 

> [!IMPORTANT]
> 要構建支援的配置，必須將 HANA 資料和日誌卷部署為 NFSv4.1 卷，並使用 NFSv4.1 協定裝載它們。 NFSv3 不支援具有備用節點的 HANA 主機自動容錯移轉配置。

![SAP NetWeaver 高可用性概觀](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

在遵循 SAP HANA 網路建議的上圖中，三個子網在一個 Azure 虛擬網路中表示： 
* 用於用戶端通信
* 用於與存儲系統通信
* 用於內部 HANA 節點間通信

Azure NetApp 卷位於單獨的子網中，[委託給 Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

對於此示例配置，子網是：  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26（委派子網到 Azure NetApp 檔）

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>設置 Azure NetApp 檔基礎結構 

在繼續設置 Azure NetApp 檔基礎結構之前，請熟悉 Azure [NetApp 檔文檔][anf-azure-doc]。 

Azure NetApp 檔在多個[Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 檢查所選 Azure 區域是否提供 Azure NetApp 檔。  

有關 Azure NetApp 檔（按 Azure 區域）的可用性的資訊，請參閱[Azure NetApp 檔可用性（按 Azure 區域][anf-avail-matrix]）。  

在部署 Azure NetApp 檔之前，請通過註冊[Azure NetApp 檔說明][anf-register]請求載入 Azure NetApp 檔。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 檔資源  

以下說明假定您已部署[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 檔資源和 VM（其中將裝載 Azure NetApp 檔資源）必須部署在同一 Azure 虛擬網路或對等 Azure 虛擬網路中。  

1. 如果尚未部署資源，請請求載入 Azure [NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 按照創建 NetApp 帳戶中的說明在所選 Azure 區域中[創建 NetApp 帳戶](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)。  

3. 按照[設置 Azure NetApp 檔容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)中的說明設置 Azure NetApp 檔容量池。  

   本文仲介紹的 HANA 體系結構使用*超服務等級*的單個 Azure NetApp 檔容量池。 對於 Azure 上的 HANA 工作負荷，我們建議使用 Azure NetApp 檔*超或**高級*[服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。  

4. 將子網委派給 Azure NetApp 檔，如[將子網委派到 Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的說明中所述。  

5. 按照為[Azure NetApp 檔創建 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)中的說明部署 Azure NetApp 檔卷。  

   部署卷時，請確保選擇**NFSv4.1**版本。 在指定的 Azure NetApp 檔[子網](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 
   
   請記住，Azure NetApp 檔資源和 Azure VM 必須位於同一 Azure 虛擬網路或對等 Azure 虛擬網路中。 例如 **，HN1**-data-mnt00001、HN1 -log-mnt00001 等，是卷名稱，nfs://10.9.0.4/ ** ** **HN1**-資料-mnt00001、nfs://10.9.0.4/**HN1**-log-mnt00001 等，是 Azure NetApp 檔卷的檔路徑。  

   * 卷**HN1**-資料 mnt00001 （nfs://10.9.0.4/**HN1**-資料 mnt00001）
   * 卷**HN1**-資料 mnt00002 （nfs://10.9.0.4/**HN1**-資料 mnt00002）
   * 卷**HN1**-log-mnt00001 （nfs://10.9.0.4/**HN1**-log mnt00001）
   * 卷**HN1**-log-mnt0002 （nfs://10.9.0.4/**HN1**-log mnt00002）
   * 卷**HN1**-共用 （nfs://10.9.0.4/**HN1**-共用）
   
   在此示例中，我們為每個 HANA 資料和日誌卷使用了單獨的 Azure NetApp 檔卷。 要在較小或非生產性系統上進行成本優化的配置，可以將所有資料裝載放在單個卷上，並且所有日誌都安裝在不同的單個卷上。  

### <a name="important-considerations"></a>重要考量︰

在創建 Azure NetApp 檔以使用備用節點方案進行 SAP HANA 橫向擴展時，請注意以下重要注意事項：

- 最小容量池為 4 位元組 （TiB）。  
- 最小體積大小為 100 GB （GiB）。
- Azure NetApp 檔和將裝載 Azure NetApp 檔卷的所有虛擬機器必須位於同一 Azure 虛擬網路中或同一區域中的[對等虛擬網路中](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所選虛擬網路必須具有委派給 Azure NetApp 檔的子網。
- Azure NetApp 檔卷的輸送量是卷配額和服務等級的函數，如[Azure NetApp 檔的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 調整 HANA Azure NetApp 卷的量量時，請確保生成的輸送量滿足 HANA 系統要求。  
- 使用 Azure NetApp 檔[匯出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)，可以控制允許的用戶端、訪問類型（讀寫、唯讀等）。 
- Azure NetApp 檔功能尚未區域感知。 目前，該功能未部署在 Azure 區域中的所有可用性區域中。 請注意某些 Azure 區域的潛在延遲影響。  

> [!IMPORTANT]
> 對於 SAP HANA 工作負載，低延遲至關重要。 與 Microsoft 代表合作，確保虛擬機器和 Azure NetApp 檔卷在接近的地方部署。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp 檔上的 HANA 資料庫的尺寸

Azure NetApp 檔卷的輸送量是卷大小和服務等級的函數，如[Azure NetApp 檔的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中為 SAP 設計基礎結構時，請注意 SAP 的一些最低存儲要求，這轉化為最低輸送量特徵：

- 在 /hana/log 上讀取寫入，每秒 250 MB （MB/s），具有 1-MB I/O 大小。  
- 讀取 16 MB 和 64 MB I/O 大小的 /hana/資料至少 400 MB/s 的活動。  
- 使用 16 MB 和 64 MB I/O 大小為 /hana/資料寫入至少 250 MB/s 的活動。 

[Azure NetApp 檔輸送量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)每 1 TiB 的卷配額是：
- 高級存儲層 - 64 MiB/s  
- 超存儲層 - 128 MiB/s  

為了滿足 SAP 資料和日誌的最低輸送量要求以及 /hana/共用的準則，建議的大小是：

| 磁碟區 | 大小<br>高級存儲層 | 大小<br>超存儲層 | 支援的 NFS 協定 |
| --- | --- | --- | --- |
| /哈納/日誌/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| HANA/shared | 每 4 個輔助節點 1xRAM | 每 4 個輔助節點 1xRAM | v3 或 v4.1 |

使用 Azure NetApp 檔超存儲層，本文仲介紹的佈局的 SAP HANA 配置是：

| 磁碟區 | 大小<br>超存儲層 | 支援的 NFS 協定 |
| --- | --- | --- |
| /哈納/日誌/mnt00001 | 2 TiB | v4.1 |
| /哈納/日誌/mnt00002 | 2 TiB | v4.1 |
| /哈納/資料/mnt00001 | 3.2 TiB | v4.1 |
| /哈納/資料/mnt00002 | 3.2 TiB | v4.1 |
| HANA/shared | 2 TiB | v3 或 v4.1 |

> [!NOTE]
> 此處列出的 Azure NetApp 檔案大小調整建議旨在滿足 SAP 為其基礎結構提供程式建議的最低要求。 在實際的客戶部署和工作負載方案中，這些大小可能不夠。 將這些建議作為起點，並根據特定工作負載的要求進行調整。  

> [!TIP]
> 您可以動態調整 Azure NetApp 檔卷的大小，而無需*卸載*卷、停止虛擬機器或停止 SAP HANA。 此方法允許靈活性，以滿足應用程式的預期和意外輸送量需求。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通過 Azure 門戶部署 Linux 虛擬機器

首先，您需要創建 Azure NetApp 檔卷。 然後執行以下步驟：
1. 在[Azure 虛擬網路中](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)創建[Azure 虛擬網路子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 
1. 部署 VM。 
1. 創建其他網路介面，並將網路介面附加到相應的 VM。  

   每個虛擬機器都有三個網路介面，對應于三個 Azure 虛擬網路子網`client`（`storage`和`hana`。 

   有關詳細資訊，請參閱在[Azure 中使用多個網路介面卡創建 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。  

> [!IMPORTANT]
> 對於 SAP HANA 工作負載，低延遲至關重要。 要實現低延遲，請與 Microsoft 代表合作，確保虛擬機器和 Azure NetApp 檔卷在接近的地方部署。 當您使用 SAP HANA Azure NetApp 檔[加入新的 SAP HANA 系統](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)時，提交必要的資訊。 
 
接下來的說明假定您已經創建了資源組、Azure 虛擬網路和三個 Azure 虛擬網路子網：`client`和`storage``hana`。 部署 VM 時，請選擇用戶端子網，以便用戶端網路介面是 VM 上的主介面。 您還需要通過存儲子網閘道配置到 Azure NetApp 檔委派子網的顯式路由。 

> [!IMPORTANT]
> 確保您選擇的作業系統已針對您正在使用的特定 VM 類型對 SAP HANA 進行了 SAP 認證。 有關這些類型的 SAP HANA 認證 VM 類型和作業系統版本的清單，請訪問[SAP HANA 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)網站。 按一下列出的 VM 類型的詳細資訊，獲取該類型支援的 SAP HANA 作業系統版本的完整清單。  

1. 為 SAP HANA 創建可用性集。 請確保設置最大更新域。  

2. 通過執行以下步驟創建三個虛擬機器（hanadb1、hanadb2、hanadb3）：** ** **hanadb2** **hanadb3**  

   a. 在 SAP HANA 支援的 Azure 庫中使用紅帽企業 Linux 映射。 在本示例中，我們使用 RHEL-SAP-HA 7.6 圖像。  

   b. 選擇您之前為 SAP HANA 創建的可用性集。  

   c. 選擇用戶端 Azure 虛擬網路子網。 選擇[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

   部署虛擬機器時，將自動生成網路介面名稱。 在這些簡單性說明中，我們將引用自動生成的網路介面，這些介面連接到用戶端 Azure 虛擬網路子網，作為**hanadb1 用戶端****、hanadb2 用戶端**和**hanadb3 用戶端**。 

3. 為`storage`虛擬網路子網創建三個網路介面，每個虛擬機器一個介面（在此示例中為**hanadb1 存儲****、hanadb2 存儲**和**hanadb3 存儲**）。  

4. 為`hana`虛擬網路子網創建三個網路介面，每個虛擬機器一個介面（在此示例中為**hanadb1-hana、hanadb2-hana**和**hanadb3-hana）。** **hanadb2-hana**  

5. 通過執行以下步驟，將新創建的虛擬網路介面附加到相應的虛擬機器：  

    a. 轉到[Azure 門戶](https://portal.azure.com/#home)中的虛擬機器。  

    b. 在左側窗格中，選擇 **"虛擬機器**"。 篩選虛擬機器名稱（例如**hanadb1），** 然後選擇虛擬機器。  

    c. 在 **"概述"** 窗格中，選擇 **"停止"** 以取消分配虛擬機器。  

    d. 選擇 **"網路**"，然後附加網路介面。 在**附加網路介面**下拉清單中，為`storage`和`hana`子網選擇已創建的網路介面。  
    
    e. 選取 [儲存]****。 
 
    f. 對剩餘的虛擬機器重複步驟 b 到 e（在我們的示例中 **，hanadb2**和**hanadb3）。**
 
    g. 將虛擬機器保留為停止狀態。 接下來，我們將為所有新連接的網路介面啟用[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

6. 通過執行以下步驟，為 和`storage``hana`子網的其他網路介面啟用加速網路：  

    a. 在[Azure 門戶](https://portal.azure.com/#home)中打開[Azure 雲外殼](https://azure.microsoft.com/features/cloud-shell/)。  

    b. 執行以下命令，為附加網路介面啟用加速網路，這些介面連接到`storage`和`hana`子網。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 通過執行以下步驟啟動虛擬機器：  

    a. 在左側窗格中，選擇 **"虛擬機器**"。 篩選虛擬機器名稱（例如**hanadb1），** 然後選擇它。  

    b. 在 **"概述"** 窗格中，選擇 **"開始**"。  

## <a name="operating-system-configuration-and-preparation"></a>作業系統配置和準備

下一節中的說明用以下內容之一進行預綴：
* **[A]**： 適用于所有節點
* **[1]**： 僅適用于節點 1
* **[2]**： 僅適用于節點 2
* **[3]**：僅適用于節點 3

通過執行以下步驟配置和準備作業系統：

1. **[A]** 維護虛擬機器上的主機檔。 包括所有子網的條目。 此示例添加了`/etc/hosts`以下條目。  

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

3. **[A]** 添加網路路由，以便與 Azure NetApp 檔的通信通過存儲網路介面進行。  

   在此示例中，將用於`Networkmanager`配置其他網路路由。 以下說明假定存儲網路介面為`eth1`。  
   首先，確定設備`eth1`的連接名稱。 在此示例中，設備的`eth1`連接名稱為`Wired connection 1`。  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   然後通過`eth1`配置到 Azure NetApp 檔委派網路的其他路由。  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    重新開機 VM 以啟動更改。  

3. **[A]** 安裝 NFS 用戶端包。  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** 準備作業系統，以便使用 NFS 在 Azure NetApp 上運行 SAP HANA，如[NETApp AFF 系統上的 SAP HANA 中所述，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。 為 NetApp 配置設置創建設定檔 */etc/sysctl.d/netapp-hana.conf。*  

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

4. **[A]** 創建具有其他優化設置的設定檔 */etc/sysctl.d/ms-az.conf。*  

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

5. **[A]** 調整陽光設置，如[NETApp AFF 系統上的 SAP HANA 中的建議，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** 用於 HANA 配置的紅帽。

    配置 RHEL，如 SAP 說明<https://access.redhat.com/solutions/2447641>2292690、2455582、2593824 和 。 [2292690] [2455582] [2593824]

    > [!NOTE]
    > 如果安裝 HANA 2.0 SP04，則需要按照`compat-sap-c++-7`SAP 說明[2593824]中所述安裝套裝軟體，然後才能安裝 SAP HANA。 

## <a name="mount-the-azure-netapp-files-volumes"></a>裝載 Azure NetApp 檔卷

1. **[A]** 為 HANA 資料庫卷創建裝載點。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 在**HN1**共用上為 /usr/sap 創建特定于節點的目錄。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 驗證 NFS 域設置。 確保域配置為預設的 Azure NetApp 檔域，即**`defaultv4iddomain.com`** 映射設置為**無人**。  

    > [!IMPORTANT]
    > 請確保在 VM 上`/etc/idmapd.conf`設置 NFS 域，以匹配 Azure NetApp 檔上的預設**`defaultv4iddomain.com`** 域配置： 。 如果 NFS 用戶端（即 VM）上的域配置與 NFS 伺服器（即 Azure NetApp 配置）不匹配，則安裝在 VM 上的 Azure NetApp 卷上的檔許可權將顯示為`nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`驗證 。 應將其設置為**Y**。要創建位於的位置`nfs4_disable_idmapping`的目錄結構，請執行裝載命令。 您將無法在 /sys/模組下手動創建目錄，因為訪問是為內核/驅動程式保留的。  

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

   有關如何更改`nfs4_disable_idmapping`參數的更多詳細資訊，請參閱https://access.redhat.com/solutions/1749883。

6. **[A]** 裝載共用的 Azure NetApp 檔卷。  

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

7. **[1]** 將節點特定的卷安裝在**hanadb1**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 將節點特定的卷安裝在**hanadb2**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 將節點特定的卷安裝在**hanadb3**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 驗證所有 HANA 卷是否都裝載了 NFS 協定版本**NFSv4**。  

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

在此示例中，使用 Azure 的備用節點在橫向擴展配置中部署 SAP HANA，我們使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>準備 HANA 安裝

1. **[A]** 在 HANA 安裝之前，設置根密碼。 安裝完成後，可以禁用根密碼。 執行為`root`命令`passwd`。  

2. **[1]** 驗證您可以通過 SSH 登錄到**hanadb2**和**hanadb3，** 而無需提示輸入密碼。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安裝 HANA 2.0 SP4 所需的其他套裝軟體。 有關詳細資訊，請參閱 SAP 說明[2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2]，[3]** 將 SAP HANA `data` `log`和目錄的擁有權更改為**hn1**adm。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** 暫時禁用防火牆，以便它不會干擾 HANA 安裝。 完成 HANA 安裝後，您可以重新啟用它。 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA 安裝

1. **[1]** 按照[SAP HANA 2.0 安裝和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的說明安裝 SAP HANA。 在此示例中，我們使用主節點、一個輔助節點和一個備用節點安裝 SAP HANA 橫向擴展。  

   a. 從 HANA 安裝軟體目錄中啟動**hdblcm**程式。 使用`internal_network`參數並傳遞子網的位址空間，該子網用於內部 HANA 節點間通信。  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. 在提示符處，輸入以下值：

     * 對於**選擇操作**：輸入**1（** 用於安裝）
     * 對於**用於安裝的其他元件**：輸入**2、3**
     * 對於安裝路徑：按 Enter（預設為 /hana/共用）
     * 對於**本地主機名稱**：按 Enter 接受預設值
     * 在 **"是否要將主機添加到系統？"：** 輸入**y**
     * 對於逗**號分隔主機名稱要添加**： 輸入**hanadb2，hanadb3**
     * 對於**根使用者名**[根]：按 Enter 接受預設值
     * 對於主機 hanadb2 的角色：輸入**1（** 對於工作人員）
     * 對於主機 hanadb2 的**主機容錯移轉組**[預設值]：按 Enter 接受預設值
     * 對於主機 hanadb2 [<>]<assign automatically>的**存儲分區編號**：按 Enter 接受預設值
     * 對於主機 hanadb2 [預設]**的工作人員組**：按 Enter 接受預設值
     * 對於主機 hanadb3**的選定角色**：輸入**2（** 用於待機）
     * 對於主機 hanadb3 的**主機容錯移轉組**[預設]：按 Enter 接受預設值
     * 對於主機 hanadb3 [預設]**的工作人員組**：按 Enter 接受預設值
     * 對於**SAP HANA 系統 ID**：輸入**HN1**
     * 對於**實例編號**[00]： 輸入**03**
     * 對於**本地主機輔助角色組**[預設]：按 Enter 接受預設值
     * 對於**選擇系統使用方式 / 輸入索引 [4]**： 輸入**4** （用於自訂）
     * 有關**資料卷的位置**[/hana/資料/HN1]：按 Enter 接受預設值
     * 有關**日誌卷的位置**[/哈納/日誌/HN1]：按 Enter 接受預設值
     * 對於**限制最大記憶體分配？** [n]： 輸入**n**
     * 對於**主機 hanadb1 的證書主機名稱稱1** [hanadb1]：按 Enter 接受預設值
     * 對於**主機 hanadb2 的證書主機名稱稱2** [hanadb2]：按 Enter 接受預設值
     * 對於**主機 hanadb3 的證書主機名稱稱3** [hanadb3]：按 Enter 接受預設值
     * 對於**系統管理員 （hn1adm） 密碼**：輸入密碼
     * 對於**系統資料庫使用者（系統）密碼**：輸入系統的密碼
     * 對於**確認系統資料庫使用者（系統）密碼**：輸入系統的密碼
     * 對於**電腦重新開機後的重新開機系統？** [n]： 輸入**n** 
     * 對於**是否要繼續 （y/n）：** 驗證摘要，如果一切看起來良好，請輸入**y**


2. **[1]** 驗證全域  

   顯示全域.ini，並確保內部 SAP HANA 節點間通信的配置到位。 驗證**通信**部分。 它應具有`hana`子網的位址空間，並且`listeninterface`應設置為`.internal`。 驗證**internal_hostname_resolution**部分。 它應具有屬於子網的 HANA 虛擬機器的`hana`IP 位址。  

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

3. **[1]** 添加主機映射以確保用戶端 IP 位址用於用戶端通信。 添加`public_host_resolution`節 ，並從用戶端子網添加相應的 IP 位址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** 重新開機 SAP HANA 以啟動更改。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 驗證用戶端介面是否將使用子網中的`client`IP 位址進行通信。  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   有關如何驗證配置的資訊，請參閱 SAP 注釋[2183363 - SAP HANA 內部網路的配置](https://launchpad.support.sap.com/#/notes/2183363)。  

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

   - 打開必要的防火牆埠

       > [!IMPORTANT]
       > 創建防火牆規則以允許 HANA 節點間通信和用戶端流量。 [所有 SAP 產品的 TCP/IP 通訊埠](https://help.sap.com/viewer/ports)會列出必要的連接埠。 以下命令只是一個示例。 在這種情況下，使用系統編號為 03。

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

   - 開始 HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. 要優化基礎 Azure NetApp 檔存儲的 SAP HANA，設置以下 SAP HANA 參數：

   - `max_parallel_io_requests`**128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**所有**

   有關詳細資訊，請參閱[NetApp AFF 系統上的 SAP HANA，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。 

   從 SAP HANA 2.0 系統開始，您可以在 中`global.ini`設置參數。 有關詳細資訊，請參閱 SAP 說明[1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   
   對於 SAP HANA 1.0 系統版本 SPS12 和更早版本，可以在安裝過程中設置這些參數，如 SAP 說明[2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp 檔使用的存儲具有 16 TB 的檔案大小限制。 SAP HANA 不會隱式地意識到存儲限制，並且在達到 16 TB 的檔案大小限制時，它不會自動創建新的資料檔案。 當 SAP HANA 嘗試將檔擴展到 16 TB 以上時，該嘗試將導致錯誤，並最終導致索引伺服器崩潰。 

   > [!IMPORTANT]
   > 為防止 SAP HANA 嘗試將資料檔案增長到超出儲存子系統[的 16 TB 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)，在 中`global.ini`設置以下參數。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 有關詳細資訊，請參閱 SAP 說明[2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 請注意 SAP 注釋[2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>測試 SAP HANA 容錯移轉 

1. 類比 SAP HANA 輔助節點上的節點崩潰。 執行下列動作： 

   a. 在類比節點崩潰之前，將以下命令運行為**hn1**dm 以捕獲環境狀態：  

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

   b. 要類比節點崩潰，在輔助節點上運行以下命令作為根，在這種情況下為**hanadb2：**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 監視系統容錯移轉完成情況。 容錯移轉完成後，捕獲狀態，該狀態應如下所示：  

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
   > 當節點遇到內核死機時，通過`kernel.panic`*在所有*HANA 虛擬機器上設置為 20 秒，避免 SAP HANA 容錯移轉的延遲。 配置在 中`/etc/sysctl`完成。 重新開機虛擬機器以啟動更改。 如果未執行此更改，則當節點遇到內核死機時，容錯移轉可能需要 10 分鐘或更長時間。  

2. 通過執行以下操作終止名稱伺服器：

   a. 在測試之前，通過將以下命令作為**hn1**adm 來檢查環境的狀態：  

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

   b. 在活動主節點上以**hn1**dm 身份運行以下命令，在這種情況下，該節點為**hanadb1：**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    備用節點**hanadb3**將接管主節點。 以下是容錯移轉測試完成後的資源狀態：  

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

   c. 重新開機**hanadb1**上的 HANA 實例（即在同一虛擬機器上，其中名稱伺服器已終止）。 **hanadb1**節點將重新加入環境，並將保留其備用角色。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**HANadb1**上啟動 SAP HANA 後，預期狀態如下：  

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

   d. 同樣，在當前活動的主節點（即節點**hanadb3**上）上終止名稱伺服器。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   節點**hanadb1**將恢復主節點的角色。 容錯移轉測試完成後，狀態將如下所示：

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

   e. 在**hanadb3**上啟動 SAP HANA，它將準備作為備用節點。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**HANadb3**上啟動 SAP HANA 後，狀態如下所示：  

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
* 要瞭解如何在 Azure（大型實例）上建立 SAP HANA 的高可用性和災害復原計畫，請參閱[Azure 上的 SAP HANA（大型實例）高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
* 要瞭解如何在 Azure VM 上建立高可用性並規劃 SAP HANA 的災害復原，請參閱[Azure 虛擬機器 （VM） 上的 SAP HANA 的高可用性][sap-hana-ha]。
