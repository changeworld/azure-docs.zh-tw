---
title: Azure NetApp Files 的 SMB 效能相關常見問題 |Microsoft Docs
description: 回答有關 Azure NetApp Files SMB 效能的常見問題。
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929451"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Azure NetApp Files 的 SMB 效能常見問題

本文將回答有關 Azure NetApp Files 的 SMB 效能最佳做法)  (常見問題的常見問題。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB 共用中是否已啟用 SMB 多重通道？ 

是，預設會啟用 SMB 多重通道，這項變更會在2020年1月提早進行。 所有 SMB 共用預先可追溯的現有 SMB 磁片區已啟用此功能，而且所有新建立的磁片區也會在建立時啟用該功能。 

在功能啟用之前建立的任何 SMB 連線都必須重設，才能利用 SMB 多重通道功能。 若要重設，您可以中斷連線並重新連線 SMB 共用。

## <a name="is-rss-supported"></a>是否支援 RSS？

是，Azure NetApp Files 支援 (RSS) 的接收端調整。

啟用 SMB 多重通道後，SMB3 用戶端會透過網路介面卡，透過網路介面卡建立多個與 Azure NetApp Files SMB 伺服器的 TCP 連線， (NIC) 支援單一 RSS。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支援 SMB 多重通道？

Windows 自 Windows 2012 起支援 SMB 多重通道，以達到最佳效能。  如需詳細資料，請參閱 [部署 smb](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) 多重通道和 [smb 多重通道的基本概念](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) 。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虛擬機器是否支援 RSS？

若要查看您的 Azure 虛擬機器 Nic 是否支援 RSS，請執行 `Get-SmbClientNetworkInterface` 下列命令並檢查欄位 `RSS Capable` ： 

![顯示 Azure 虛擬機器 RSS 輸出的螢幕擷取畫面。](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files 是否支援 SMB Direct？

否，Azure NetApp Files 不支援 SMB 直接傳輸。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多重通道的優點為何？ 

SMB 多重通道功能可讓 SMB3 用戶端透過單一網路介面卡建立連接集區 (NIC) 或多個 Nic，並使用它們來傳送單一 SMB 會話的要求。 相反地，根據設計，SMB1 和 SMB2 需要用戶端建立一個連線，並透過該連線傳送指定會話的所有 SMB 流量。 此單一連接會限制可從單一用戶端達成的整體通訊協定效能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>我是否應該在適用于 SMB 的用戶端上設定多個 Nic？

否。 SMB 用戶端會符合 SMB 伺服器所傳回的 NIC 計數。  每個存放磁片區只能從一個儲存體端點來存取。  這表示任何指定的 SMB 關聯性都只會使用一個 NIC。  

如以下的輸出所 `Get-SmbClientNetworkInterace` 示，虛擬機器有2個網路介面--15 和12。  如下列命令所示 `Get-SmbMultichannelConnection` ，即使有兩個支援 RSS 的 nic，但只有介面12用於與 SMB 共用的連接; 介面15不在使用中。

![Notificationhubs，顯示支援 RSS 的 NIC 的輸出。](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 支援 NIC 小組嗎？

Azure 不支援 NIC 小組。 雖然 Azure 虛擬機器支援多個網路介面，但它們代表邏輯而非實體結構。 因此，它們不會提供容錯能力。  此外，Azure 虛擬機器的可用頻寬是針對機器本身計算，而不是任何個別的網路介面。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 多重通道的效能為何？

下列測試和圖形示範單一實例工作負載上 SMB 多重通道的強大功能。

### <a name="random-io"></a>隨機 i/o  

在用戶端上停用 SMB 多重通道時，就會使用 FIO 和 40 GiB 工作集來執行純 4 KiB 讀取和寫入測試。  SMB 共用已在每個測試之間卸離，且每個 RSS 網路介面設定的 SMB 用戶端連接計數增量為、、 `1` `4` `8` 、 `16` 、 `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` 。 測試顯示的預設設定 `4` 對於 i/o 密集的工作負載而言已足夠; 遞增至且不會有明顯的 `8` `16` 影響。 

此命令 `netstat -na | findstr 445` 證明已建立額外的連接，並以遞增方式從到到 `1` `4` `8` 和到 `16` 。  在每個測試期間，每個測試都有四個 CPU 核心可供使用，如 perfmon 統計資料所確認 `Per Processor Network Activity Cycles` (不包含在本文中。 ) 

![此圖表顯示 SMB 多重通道的隨機 i/o 比較。](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虛擬機器不會影響 SMB (也不會) 儲存體 i/o 限制。  如下圖所示，針對快取的儲存體 IOPS，D32ds 實例類型的比率為308000，而針對未快取的儲存體 IOPS 則是51200。  不過，上圖顯示的是透過 SMB 更多 i/o。

![顯示隨機 i/o 比較測試的圖表。](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>連續 IO 

與先前所述隨機 i/o 測試類似的測試，是以 64-KiB 順序 i/o 來執行。 雖然超過4個的每個 RSS 網路介面的用戶端連接計數增加，對於隨機 i/o 沒有明顯的影響，但同樣的 i/o 也不適用。 如下圖所示，每個增加都會與相對應的讀取輸送量增加相關聯。 由於 Azure 針對每個實例類型/大小所放置的網路頻寬限制，寫入輸送量仍保持平坦。 

![顯示輸送量測試比較的圖表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 會在每個虛擬機器類型/大小上放置網路速率限制。 速率限制只會加諸輸出流量。 虛擬機器上存在的 Nic 數目與電腦可用的總頻寬量無關。  例如，D32ds 實例類型具有設定的網路限制 16000 Mbps (2000 MiB/s) 。  如上圖所示，此限制會影響輸出流量 (寫入) 但不是多重通道讀取。

![顯示連續 i/o 比較測試的圖表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>使用 1 TB 資料集的單一實例所預期的效能為何？

為了更詳細地瞭解具有讀/寫混合的工作負載，下列兩個圖表會顯示單一、Ultra 服務層級50的雲端磁片區（1 tb 的資料集，以及具有4個 SMB 多重通道）的效能。 使用的最佳 IODepth 為16，且彈性 IO (FIO) 參數可用來確保網路頻寬 () 的完整使用 `numjobs=16` 。

下圖顯示4k 隨機 i/o 的結果，其中單一 VM 實例和讀取/寫入混合的間隔為10%：

![顯示 Windows 2019 標準 _D32ds_v4 4K 隨機 IO 測試的圖表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

下圖顯示順序 i/o 的結果：

![顯示 Windows 2019 標準 _D32ds_v4 64K 順序輸送量的圖表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>使用具有 1 TB 資料集的5部 Vm 進行相應放大時，預期的效能為何？

這些具有5個 Vm 的測試會使用與單一 VM 相同的測試環境，每個進程都會寫入其本身的檔案。

下圖顯示隨機 i/o 的結果：

![顯示 Windows 2019 標準 _D32ds_v4 4K 5 實例 randio IO 測試的圖表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

下圖顯示順序 i/o 的結果：

![顯示 Windows 2019 標準 _D32ds_v4 64K 5 個實例順序輸送量的圖表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>您要如何監視 Hyper-v 乙太網路介面卡，並確保能將網路容量最大化？  

用來測試 FIO 的其中一個策略是設定 `numjobs=16` 。 這麼做會將每個工作分支至16個特定的實例，以充分利用 Microsoft Hyper-V 的網路介面卡。

您可以選取 [ **效能監視器 > 將 > 計數器新增至網路介面] > Microsoft Hyper-V 網路介面卡**，檢查 Windows 效能監視器中每個介面卡上的活動。

![顯示效能監視器新增計數器介面的螢幕擷取畫面。](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

在磁片區中執行資料流量之後，您可以在 Windows 效能監視器中監視您的介面卡。 如果您未使用這些16個虛擬網路介面卡，您可能不會將網路頻寬容量最大化。

![顯示效能監視器輸出的螢幕擷取畫面。](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>是否建議使用加速網路？

為了達到最大效能，建議您盡可能設定 [加速網路](../virtual-network/create-vm-accelerated-networking-powershell.md) 。 請記住下列考慮：  

* Azure 入口網站預設會針對支援這項功能的虛擬機器啟用加速網路。  不過，其他部署方法（例如 Ansible 和類似的設定工具）可能不會。  無法啟用加速網路可能會妨礙機器的效能。  
* 如果虛擬機器的網路介面上未啟用加速網路，因為其不支援實例類型或大小，它會維持在較大的實例類型中停用。 在這些情況下，您將需要手動介入。

## <a name="are-jumbo-frames-supported"></a>是否支援巨型個框架？

Azure 虛擬機器不支援大型框架。

## <a name="is-smb-signing-supported"></a>是否支援 SMB 簽署？ 

SMB 通訊協定提供檔案和列印共用，以及其他網路作業（例如遠端 Windows 系統管理）的基礎。 為防止會修改傳輸中 SMB 封包的中間人攻擊，SMB 通訊協定支援 SMB 封包的數位簽章。 

Azure NetApp Files 支援的所有 SMB 通訊協定版本都支援 SMB 簽署。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 簽署有何效能影響？  

SMB 簽章在 SMB 效能上有不利的效果。 在效能降低的其他潛在原因中，每個封包的數位簽章都會耗用額外的用戶端 CPU，作為下面所示的 perfmon 輸出。 在此情況下，會對 SMB （包括 SMB 簽署）顯示「核心0」的責任。  在上一節中，與非多聲道連續讀取輸送量數位的比較，顯示 SMB 簽署可減少從 875MiB/s 到大約 250MiB/s 的整體輸送量。 

![顯示 SMB 簽章效能影響的圖表。](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>後續步驟  

- [關於 Azure NetApp Files 的常見問題](azure-netapp-files-faqs.md)
- 請參閱 [Azure Netapp files：適用于 Smb 工作負載的受控企業檔案共用](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) ，有關如何搭配 Azure NetApp FILES 使用 smb 檔案共用。