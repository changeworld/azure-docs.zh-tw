---
title: 使用 WSFC 和 Azure 共用磁片的 SAP ASCS/SCS 多重 SID HA |Microsoft Docs
description: 具有 WSFC 和 Azure 共用磁片之 SAP ASCS/SCS 實例的多重 SID 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4856b2578a007f72aeeec64588ac7f9c58158de
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860943"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>使用 Windows server 容錯移轉叢集和 Azure 共用磁片的 SAP ASCS/SCS 實例多重 SID 高可用性

> ![Windows 作業系統][Logo_Windows] Windows
>

本文著重于如何將單一 ASCS/SCS 安裝移至 SAP 多重 SID 設定，方法是將其他 SAP ASCS/SCS 叢集實例安裝至現有的 Windows Server 容錯移轉叢集， (WSFC) 叢集與 Azure 共用磁片。 完成此程序之後，您將已設定 SAP 多重 SID 叢集。

## <a name="prerequisites-and-limitations"></a>必要條件和限制

目前，您可以使用 Azure 進階 SSD 磁片作為 SAP ASCS/SCS 實例的 Azure 共用磁片。 下列限制已就緒：

-  [Azure Ultra 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) 不支援作為 SAP 工作負載的 Azure 共用磁片。 目前無法在可用性設定組中使用 Azure Ultra 磁片來放置 Azure Vm
-  只有可用性設定組中的 Vm 支援具有進階 SSD 磁片的[Azure 共用磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)。 可用性區域部署中並不支援此功能。 
-  Azure 共用磁片值 [maxShares](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable?tabs=azure-cli#disk-sizes) 決定可使用共用磁片的叢集節點數目。 通常針對 SAP ASCS/SCS 實例，您將在 Windows 容錯移轉叢集中設定兩個節點，因此的值 `maxShares` 必須設定為 [2]。
-  所有 SAP ASCS/SCS 叢集 Vm 都必須部署在相同的 [Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)中。   
   雖然您可以在沒有 PPG 的情況下，使用 Azure 共用磁片在可用性設定組中部署 Windows 叢集 Vm，PPG 將可確保 Azure 共用磁片和叢集 Vm 的實體接近度，進而達到 Vm 和儲存層之間的延遲。    

如需 Azure 共用磁片限制的進一步詳細資料，請仔細參閱 Azure 共用磁片檔的 [限制](https://docs.microsoft.com/azure/virtual-machines/linux/disks-shared#limitations) 一節。  

> [!IMPORTANT]
> 使用 Azure 共用磁片部署 SAP ASCS/SCS Windows 容錯移轉叢集時，請注意，您的部署將會使用一個儲存體叢集中的單一共用磁片來運作。 您的 SAP ASCS/SCS 實例將會受到影響，以防儲存體叢集發生問題，也就是部署 Azure 共用磁片的位置。  

> [!IMPORTANT]
> 安裝程式必須符合下列條件︰
> * 每個資料庫管理系統 (DBMS) SID 必須有自己專用的 WSFC 叢集。  
> * SAP 應用程式伺服器屬於必須擁有自己專用 VM 的一個 SAP 系統 SID。  
> * 不支援在相同的叢集中將複寫伺服器1和排入佇列複寫伺服器2的混合。  


## <a name="supported-os-versions"></a>支援的 OS 版本

支援 Windows Server 2016 和 Windows Server 2019 (使用最新的資料中心映射) 。

我們強烈建議使用 **Windows Server 2019 Datacenter**，如下所示：
- Windows 2019 容錯移轉叢集服務為 Azure 感知
- 藉由監視 Azure 排程事件，可新增 Azure 主機維護的整合和認知，並改善體驗。
- 您可以使用分散式網路名稱 (它是) 的預設選項。 因此，叢集網路名稱不需要有私人 IP 位址。 此外，您也不需要在 Azure Internal Load Balancer 上設定此 IP 位址。 

## <a name="architecture"></a>架構

多 SID 設定支援將複寫伺服器 1 (ERS1) 和排入佇列複寫伺服器 2 (ERS2) 。  相同的叢集中不支援混合的 ERS1 和 ERS2。 

1. 第一個範例顯示兩個 SAP Sid，兩者都有 ERS1 架構：

   - 使用 ERS1，將 SAP 適用于 SID1 部署在共用磁片上。 ERS 實例會安裝在本機主機和本機磁片磁碟機上。
     SAP 適用于 SID1 有自己的 (虛擬) IP 位址 (適用于 SID1 (在 Azure 內部負載平衡器上設定的) SCS IP1) 。

   - 使用 ERS1，將 SAP SID2 部署在共用磁片上。 ERS 實例會安裝在本機主機和本機磁片磁碟機上。
     SAP SID2 具有 (的虛擬) IP 位址 (SID2 () SCS IP2) ，這也是在 Azure 內部負載平衡器上設定的。

![高可用性 SAP ASCS/SCS 實例-具有 ERS1 設定的兩個實例][sap-ha-guide-figure-6007]

2. 第二個範例顯示兩個 SAP Sid，兩者都有 ERS2 架構： 

   - 具有 ERS2 的 SAP 適用于 SID1 也會叢集化，並部署在本機磁片磁碟機上。  
     SAP 適用于 SID1 具有 (的虛擬) IP 位址 (適用于 SID1 (在 Azure 內部負載平衡器上設定的) SCS IP1) 。
     SAP 適用于 SID1 系統所使用的 SAP ERS2 具有自己的 (虛擬) IP 位址 (適用于 SID1 ERS2 IP2) ，它是在 Azure 內部負載平衡器上設定的。

   - 具有 ERS2 的 SAP SID2 也會叢集化，並部署在本機磁片磁碟機上。  
     SAP SID2 具有 (的虛擬) IP 位址 (SID2 (在 Azure 內部負載平衡器上設定的) SCS I P 3) 。
     SAP SID2 系統所使用的 SAP ERS2 具有自己的 (虛擬) IP 位址 (SID2 ERS2 IP4) ，它是在 Azure 內部負載平衡器上設定的。

   這裡總共有四個虛擬 IP 位址：  
   - 適用于 SID1 () SCS IP1
   - SID2 ERS2 IP2
   - SID2 () SCS I P 3
   - SID2 ERS2 IP4

![高可用性 SAP ASCS/SCS 實例-具有 ERS1 和 ERS2 設定的兩個實例][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>基礎結構準備

除了**現有**的叢集 sap **PR1** ASCS/SCS 實例以外，我們也會安裝新的 sap SID **PR2**。  

### <a name="host-names-and-ip-addresses"></a>主機名稱和 IP 位址

| 主機名稱角色 | 主機名稱 | 靜態 IP 位址 | 可用性設定組 | 鄰近位置群組 |
| --- | --- | --- |---| ---|
| 第1個叢集節點 ASCS/SCS 叢集 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 第2個叢集節點 ASCS/SCS 叢集 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 叢集網路名稱 | pr1clust |10.0.0.42 (**僅** 適用于 Win 2016 叢集)  | n/a | n/a |
| **適用于 sid1** ASCS 叢集網路名稱 | pr1-ascscl |10.0.0.43 | n/a | n/a |
| **適用于 sid1****僅**針對 ERS2) ERS 叢集網路名稱 ( | pr1-erscl |10.0.0.44 | n/a | n/a |
| **SID2** ASCS 叢集網路名稱 | pr2-ascscl |10.0.0.45 | n/a | n/a |
| **SID2****僅**針對 ERS2) ERS 叢集網路名稱 ( | pr1-erscl |10.0.0.46 | n/a | n/a |

### <a name="create-azure-internal-load-balancer"></a>建立 Azure 內部負載平衡器

SAP ASCS、SAP SCS 和新的 SAP ERS2 會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上，需要 [負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 才能使用虛擬 IP 位址。 我們強烈建議使用 [標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 

您必須為第二個 SAP SID ASCS/SCS/ERS 實例 **PR2**將設定新增至現有的負載平衡器。 第一個 SAP SID **PR1** 的設定應該已準備就緒。  

** () SCS PR2 [實例號碼 02]**
- 前端組態
    - 靜態 ASCS/SCS IP 位址 **10.0.0.45**
- 後端組態  
    已準備就緒-Vm 已新增至後端集區，同時設定 SAP SID **PR1**
- 探查連接埠
    - 埠 620**nr** [**62002**] 保留 Protocol (TCP) 的預設選項，間隔 (5) ，狀況不良閾值 (2) 
- 負載平衡規則
    - 若使用 Standard Load Balancer，請選取 [HA 連接埠]
    - 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
        - 32**nr** TCP [**3202**]
        - 36**nr** TCP [**3602**]
        - 39**nr** TCP [**3902**]
        - 81**nr** TCP [**8102**]
        - 5**nr**13 TCP [**50213**]
        - 5**nr**14 TCP [**50214**]
        - 5**nr**16 TCP [**50216**]
        - 與 **PR2** ASCS 前端 IP、健康情況探查和現有的後端集區建立關聯。  

    - 請確定 [閒置超時] (分鐘) 設定為最大值30，而且已啟用 [直接伺服器傳回) ] 浮動 IP (。

**ERS2 PR2 [實例號碼 12]** 

由於排入佇列複寫伺服器 2 (ERS2) 也會叢集化，因此除了 SAP ASCS/SCS IP 之外，也必須在 Azure ILB 上設定 ERS2 虛擬 IP 位址。 本節僅適用于使用 **PR2**的佇列複寫伺服器2架構。  
- 新的前端設定
    - 靜態 SAP ERS2 IP 位址 **10.0.0.46**

- 後端組態  
  Vm 已新增至 ILB 後端集區。  

- 新增探查埠
    - 埠 621**nr**  [**62112**] 保留 Protocol (TCP) 的預設選項，間隔 (5) ，狀況不良閾值 (2) 

- 新的負載平衡規則
    - 若使用 Standard Load Balancer，請選取 [HA 連接埠]
    - 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
        - 32**nr** TCP [**3212**]
        - 33**nr** TCP [**3312**]
        - 5**nr**13 TCP [**51212**]
        - 5**nr**14 TCP [**51212**]
        - 5**nr**16 TCP [**51212**]
        - 與 **PR2** ERS2 前端 IP、健康情況探查和現有的後端集區建立關聯。  

    - 請確定 [閒置超時] (分鐘) 設定為最大值（例如30），而且已啟用該浮動 IP ([直接伺服器傳回]) 。


### <a name="create-and-attach-second-azure-shared-disk"></a>建立並附加第二個 Azure 共用磁片

在其中一個叢集節點上執行此命令。 您將需要調整資源群組、Azure 區域、SAPSID 等等的值。  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>使用 PowerShell 將共用磁片格式化
1. 取得磁片編號。 在其中一個叢集節點上執行 PowerShell 命令：

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 將磁片格式化。 在此範例中，它是磁片編號3。 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 確認磁片現在已顯示為叢集磁片。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. 在叢集中註冊磁片。  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>建立叢集 SAP ASCS/SCS 執行個體的虛擬主機名稱

1. 在 Windows DNS 管理員中建立新的 SAP ASCS/SCS 實例之虛擬主機名稱的 DNS 專案。  
   您指派給 DNS 中虛擬主機名稱的 IP 位址必須與您在 Azure Load Balancer 中指派的 IP 位址相同。  

   ![_Define SAP ASCS/SCS 叢集虛擬名稱和 IP 位址的 DNS 專案][sap-ha-guide-figure-6009]
 
   _定義 SAP ASCS/SCS 叢集虛擬名稱和 IP 位址的 DNS 專案_

2. 如果使用 SAP 排入佇列複寫伺服器2，也就是叢集實例，則您也必須在 DNS 中保留 ERS2 的虛擬主機名稱。 
   您指派給 DNS 中 ERS2 之虛擬主機名稱的 IP 位址，必須與您在 Azure Load Balancer 中指派的 IP 位址相同。  

   ![_Define SAP ERS2 叢集虛擬名稱和 IP 位址的 DNS 專案][sap-ha-guide-figure-6010]
 
   _定義 SAP ERS2 叢集虛擬名稱和 IP 位址的 DNS 專案_

3. 若要定義指派給虛擬主機名稱的 IP 位址，請選取 [ **DNS 管理員**  >  **網域**]。

   ![適用于 SAP ASCS/SCS 和 ERS2 叢集設定的新虛擬名稱和 IP 位址][sap-ha-guide-figure-6011]

   _適用于 SAP ASCS/SCS 和 ERS2 叢集設定的新虛擬名稱和 TCP/IP 位址_

## <a name="sap-installation"></a>SAP 安裝 

### <a name="install-the-sap-first-cluster-node"></a>安裝 SAP 的第一個叢集節點

遵循 SAP 描述的安裝程式。 請確定在開始安裝選項「第一個叢集節點」中，並選擇「叢集共用磁片」作為設定選項。  
選擇新建立的共用磁片。  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>修改 ASCS/SCS 實例的 SAP 設定檔

如果您正在執行排入佇列複寫伺服器1，請新增 SAP 設定檔參數， `enque/encni/set_so_keepalive` 如下所述。 此設定檔參數可避免 SAP 工作程序與加入佇列伺服器之間的連線在閒置時間太長時關閉。 ERS2 不需要 SAP 參數。 

1. 如果使用 ERS1，請將此設定檔參數新增至 SAP ASCS/SCS 實例設定檔。

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   針對 ERS1 和 ERS2，請確定 `keepalive` 作業系統參數已設定為 SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)中所述。   

2. 若要套用 SAP 設定檔參數變更，請重新開機 SAP ASCS/SCS 實例。

### <a name="configure-probe-port-on-the-cluster-resource"></a>在叢集資源上設定探查埠

使用內部負載平衡器探查功能，讓整個叢集組態使用 Azure Load Balancer。 Azure 內部負載平衡器通常會在參與的虛擬機器之間，平均分配內送的工作負載。

不過，這對某些叢集組態不會產生作用，因為只有一個執行個體是主動的。 另一個執行個體是被動的，而且不接受任何工作負載。 當 Azure 內部負載平衡器偵測到使用中的實例，而且只以作用中的實例為目標時，探查功能會有所説明。  

> [!IMPORTANT]
> 在此範例設定中， **ProbePort** 會設定為 620**Nr**。 若為具有數位 **02** 的 SAP ASCS 實例，則為 620**02**。
> 您將需要調整設定，以符合您的 SAP 實例號碼和 SAP SID。

若要新增探查埠，請在其中一個叢集 Vm 上執行此 PowerShell 模組：

- 在 SAP ASC/SCS 實例的實例號碼為**02**的情況下 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- 如果使用 ERS2，則為實例編號 **12**（叢集）。 不需要為 ERS1 設定探查埠，因為它未叢集化。  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 函數的程式碼 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` 看起來會像這樣：
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>繼續進行 SAP 安裝

1. 遵循 SAP 安裝指南中所述的程式，安裝資料庫實例。  
2. 遵循 SAP 安裝指南中所述的步驟，在第二個叢集節點上安裝 SAP。  
3. 在您指定用來裝載 PAS 的虛擬機器上安裝 SAP 主要應用程式伺服器 (PAS) 實例。   
   請依照 SAP 安裝指南中所述的程式進行操作。 在 Azure 上沒有相依性。
4. 在指定用來裝載 SAP 應用程式伺服器實例的虛擬機器上，安裝其他 SAP 應用程式伺服器。  
   請依照 SAP 安裝指南中所述的程式進行操作。 在 Azure 上沒有相依性。 

## <a name="test-the-sap-ascsscs-instance-failover"></a>測試 SAP ASCS/SCS 實例容錯移轉
針對概述的容錯移轉測試，我們假設在節點 A 上的 SAP ASCS 為作用中。  

1. 確認 SAP 系統可成功從節點 A 容錯移轉到節點 B。在此範例中，會針對 SAPSID **PR2**進行測試。  
   請確定每個 SAPSID 都可以成功移至另一個叢集節點。   
   選擇下列其中一個選項，以起始 \<SID\> 從叢集節點 a 到叢集節點 B 的 SAP 叢集群組容錯移轉：
    - 容錯移轉叢集管理員  
    - 容錯移轉叢集 PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. 重新啟動 Windows 客體作業系統內的叢集節點 A。 這會起始將 SAP 叢集 \<SID\> 群組從節點 A 到節點 B 的自動容錯移轉。  
3. 從 Azure 入口網站重新啟動叢集節點 A。 這會起始將 SAP 叢集 \<SID\> 群組從節點 A 到節點 B 的自動容錯移轉。  
4. 使用 Azure PowerShell 重新啟動叢集節點 A。 這會起始將 SAP 叢集 \<SID\> 群組從節點 A 到節點 B 的自動容錯移轉。

## <a name="next-steps"></a>後續步驟

* [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構][sap-high-availability-infrastructure-wsfc-shared-disk]
* [在 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和共用磁碟上安裝 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md