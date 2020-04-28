---
title: 適用于 Azure NetApp Files 的效能基準測試結果 |Microsoft Docs
description: 描述適用于磁片區層級之 Azure NetApp Files 的效能基準測試結果。
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881744"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>適用於Azure NetApp Files 的效能基準測試結果

本文說明 Azure NetApp Files 在磁片區層級的效能基準測試結果。 

## <a name="sample-application-used-for-the-tests"></a>用於測試的範例應用程式

效能測試是以使用 Azure NetApp Files 的範例應用程式來執行。 應用程式具有下列特性： 

* 針對雲端打造的 Linux 應用程式
* 可以透過新增的虛擬機器（Vm）線性調整，以視需要增加計算能力
* 需要 data lake 的快速存取範圍
* 的 i/o 模式有時是隨機的，有時也是連續的 
    * 隨機模式需要低延遲的大量 i/o。 
    * 順序模式需要大量的頻寬。 

## <a name="about-the-workload-generator"></a>關於工作負載產生器

結果來自 Vdbench 摘要檔案。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html)是命令列公用程式，它會產生磁片 i/o 工作負載來驗證存放裝置效能。 使用的用戶端-伺服器設定是可調整的。  它包含單一混合式主要/用戶端和14個專用用戶端 Vm。

## <a name="about-the-tests"></a>關於測試

測試的設計目的是要識別範例應用程式可能會有的限制，以及曲線到限制為止的回應時間。  

已執行下列測試： 

* 100% 8-KiB 隨機讀取
* 100% 8-KiB 隨機寫入
* 100% 64-KiB 順序讀取
* 100% 64-KiB 順序寫入
* 50% 64-KiB 順序讀取，50% 64-KiB 連續寫入
* 50% 8-KiB 隨機讀取，50% 8-KiB 隨機寫入

## <a name="bandwidth"></a>頻寬

Azure NetApp Files 提供多個[服務層級](azure-netapp-files-service-levels.md)。 每個服務等級都會提供不同的頻寬量，每個 TiB 的布建容量（磁片區配額）。 磁片區的頻寬限制是根據服務等級和磁片區配額的組合來布建。 頻寬限制只是判斷即將實現的實際輸送量量的一個因素。  

目前，4500 MiB 是工作負載針對測試中的單一磁片區所達到的最高輸送量。  使用 Premium 服務層級時，70.31 TiB 的磁片區配額會布建足夠的頻寬，以根據下列計算來實現此輸送量： 

![頻寬公式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![配額和服務等級](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>需要大量輸送量的工作負載

輸送量測試使用 Vdbench 和 12xD32s V3 儲存體 Vm 的組合。 測試中的範例磁片區已達到下列輸送量數位：

![輸送量測試](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>需要大量 i/o 的工作負載

I/o 測試使用 Vdbench 和 12xD32s V3 儲存體 Vm 的組合。 測試中的範例磁片區已達到下列 i/o 數位：

![I/o 測試](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

測試 Vm 與 Azure NetApp Files 磁片區之間的距離會影響 i/o 效能。  下圖比較兩組不同 Vm 的 IOPS 和延遲回應曲線。  其中一組 Vm 位於 Azure NetApp Files 附近，而另一組則會進一步離開。  進一步的 Vm 集增加的延遲，會影響在指定的平行處理原則層級所達到的 IOPS 數量。  無論如何，讀取磁片區可能會超過 300000 IOPS，如下所示： 

![延遲研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>摘要

延遲敏感的工作負載（資料庫）可以有一毫秒的回應時間。 交易效能可能會超過單一磁片區的 300k.wvx IOPS。

輸送量敏感的應用程式（適用于串流和映射處理）可以有 4.5 GiB/s 輸送量。

## <a name="example-scripts"></a>範例指令碼

下列範例腳本僅供示範用途之用。  它們不會用於生產用途。  

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
