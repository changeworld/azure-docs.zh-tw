---
title: Azure NetApp 檔的性能基準測試結果 |微軟文檔
description: 在卷級別描述 Azure NetApp 檔的性能基準測試結果。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881744"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>適用於Azure NetApp Files 的效能基準測試結果

本文介紹了 Azure NetApp 檔在卷級別的性能基準測試結果。 

## <a name="sample-application-used-for-the-tests"></a>用於測試的應用程式範例

使用 Azure NetApp 檔使用應用程式範例運行效能測試。 該應用程式具有以下特徵： 

* 為雲構建的基於 Linux 的應用程式
* 可使用添加的虛擬機器 （VM） 線性擴展，根據需要提高計算能力
* 需要快速訪問資料湖
* 具有有時隨機的 I/O 模式，有時是連續的 
    * 對於大量 I/O，隨機模式需要低延遲。 
    * 順序模式需要大量的頻寬。 

## <a name="about-the-workload-generator"></a>關於工作負載產生器

結果來自 Vdbench 摘要檔。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html)是一個命令列實用程式，用於生成磁片 I/O 工作負載以驗證存儲性能。 使用的用戶端-伺服器配置是可擴展的。  它包括單個混合主/用戶端和 14 個專用用戶端 VM。

## <a name="about-the-tests"></a>關於測試

這些測試旨在確定應用程式範例可能具有的限制以及曲線到限制的回應時間。  

運行了以下測試： 

* 100% 8-KiB 隨機讀取
* 100% 8-KiB 隨機寫入
* 100% 64-KiB 順序讀取
* 100% 64-KiB 順序寫入
* 50% 64-KiB 順序讀取，50% 64-KiB 順序寫入
* 50% 8-KiB 隨機讀取，50% 8-KiB 隨機寫入

## <a name="bandwidth"></a>頻寬

Azure NetApp 檔提供多個[服務等級](azure-netapp-files-service-levels.md)。 每個服務等級提供不同數量的頻寬，每個 TiB 的預配容量（容量配額）。 基於服務等級和卷配額的組合預配卷的頻寬限制。 頻寬限制只是確定將實現的實際輸送量量的一個因素。  

目前，4，500 米 B 是工作負載對測試中單個卷實現的最高輸送量。  使用高級服務等級，容量配額為 70.31 TiB 將預配足夠的頻寬，以便根據以下計算實現此輸送量： 

![頻寬公式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![配額和服務等級](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>輸送量密集型工作負載

輸送量測試使用 Vdbench 和 12xD32s V3 存儲 VM 的組合。 測試中的示例卷實現了以下輸送量數位：

![輸送量測試](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O 密集型工作負載

I/O 測試使用 Vdbench 和 12xD32s V3 存儲 VM 的組合。 測試中的樣本量達到以下 I/O 編號：

![I/O 測試](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

測試 VM 和 Azure NetApp 檔卷之間的距離會影響 I/O 性能。  下圖比較了兩組 VM 的 IOPS 與延遲回應曲線。  一組 VM 位於 Azure NetApp 檔附近，另一組則位於更遠的地方。  進一組 VM 的延遲增加會影響在給定並行性級別上實現的 IOPS 量。  無論如何，對卷的讀取可以超過 300，000 IOPS，如下圖所示： 

![延遲研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>總結

延遲敏感的工作負載（資料庫）可以具有一毫秒的回應時間。 單個卷的事務性能可以超過 300k IOPS。

輸送量敏感型應用程式（用於流式處理和映射）的輸送量為 4.5GiB/s。

## <a name="example-scripts"></a>範例指令碼

以下示例腳本僅用於演示目的。  它們不用於生產目的。  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
