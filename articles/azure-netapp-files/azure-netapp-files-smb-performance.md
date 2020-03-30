---
title: 有關 Azure NetApp 檔 SMB 性能的常見問題解答*微軟文檔
description: 回答有關 Azure NetApp 檔的 SMB 性能的常見問題。
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460444"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>有關 Azure NetApp 檔的 SMB 性能的常見問題解答

本文回答了有關 Azure NetApp 檔的 SMB 性能最佳實踐的常見問題 （FAQ）。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB 多通道是否在 SMB 股票中啟用？ 

是，預設情況下啟用 SMB 多管道，這是 2020 年 1 月初進行的更改。 所有早于現有 SMB 卷的 SMB 共用都啟用了該功能，所有新創建的卷在創建時也將啟用該功能。 

在功能啟用之前建立的任何 SMB 連接都需要重置才能利用 SMB 多通道功能。 要重置，可以斷開連接並重新連接 SMB 共用。

## <a name="is-rss-supported"></a>是否支援 RSS？

是的，Azure NetApp 檔支援接收側縮放 （RSS）。

啟用 SMB 多通道後，SMB3 用戶端通過支援單個 RSS 的網路介面卡 （NIC） 建立到 Azure NetApp 檔 SMB 伺服器的多個 TCP 連接。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支援 SMB 多管道？

自 Windows 2012 年以來，Windows 一直支援 SMB 多管道，以實現最佳性能。  有關詳細資訊，請參閱[部署 SMB 多通道](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))和[SMB 多通道的基礎知識](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/)。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虛擬機器是否支援 RSS？

要查看 Azure 虛擬機器 NIC 是否支援 RSS，請按照`Get-SmbClientNetworkInterface`如下方式運行該命令並`RSS Capable`檢查欄位： 

![對 Azure 虛擬機器的 RSS 支援](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 檔是否支援 SMB 直接檔？

否，Azure NetApp 檔不支援 SMB Direct。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多管道有什麼好處？ 

SMB 多通道功能使 SMB3 用戶端能夠通過單個網路介面卡 （NIC） 或多個 NIC 建立連接池，並使用它們發送單個 SMB 會話的請求。 相反，根據設計，SMB1 和 SMB2 要求用戶端建立一個連接，並通過該連接發送給定會話的所有 SMB 流量。 此單個連接限制可以從單個用戶端實現的總體協定性能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>我應該為 SMB 配置用戶端上的多個 NIC 嗎？

否。 SMB 用戶端將與 SMB 伺服器返回的 NIC 計數匹配。  每個存儲卷可從一個存儲終結點訪問。  這意味著對於任何給定的 SMB 關係，將只使用一個 NIC。  

如下文的`Get-SmbClientNetworkInterace`輸出所示，虛擬機器有兩個網路介面 - 15 和 12。  如下圖所示`Get-SmbMultichannelConnection`，即使有兩個支援 RSS 的 NICS，也僅使用介面 12 與 SMB 共用連接;介面 15 未使用。

![支援 RSS 的 NICS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 中是否支援 NIC 團隊？

Azure 中不支援 NIC 團隊。 儘管 Azure 虛擬機器支援多個網路介面，但它們表示邏輯而不是物理構造。 因此，它們不提供容錯。  此外，Azure 虛擬機器可用的頻寬是為電腦本身計算的，而不是為任何單獨的網路介面計算的。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 多管道的性能如何？

以下測試和圖表演示了 SMB 多通道對單實例工作負載的強大功能。

### <a name="random-io"></a>隨機 I/O  

在用戶端上禁用 SMB 多通道後，使用 FIO 和 40-GiB 工作集執行純 8-KiB 讀寫測試。  SMB 共用在每個測試之間分離，SMB 用戶端連接計數的增量為每個 RSS 網路`1`介面設置`4`，`8``16`、 `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`、 。 測試顯示，的`4`預設設置足以滿足 I/O 密集型工作負載;遞增至`8`無`16`效果。 

該命令`netstat -na | findstr 445``1`證明，建立其他連接時，從 到`4``8`和 以`16`遞增為增量。  在每次測試期間，SMB 都充分利用了四個 CPU 內核，這一點`Per Processor Network Activity Cycles`由 perfmon 統計資訊（不包括在本文中）所證實。

![隨機 I/O 測試](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虛擬機器不會影響 SMB（或 NFS） 存儲 I/O 限制。  如下所示，D16 實例類型的緩存存儲 IOPS 的受限速率為 32，000，未緩存存儲 IOPS 的速率為 25，600。  但是，上圖顯示的 I/O 比 SMB 明顯多。

![隨機 I/O 比較](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>順序 IO 

與上述隨機 I/O 測試類似的測試使用 64-KiB 順序 I/O 執行。 儘管每個 RSS 網路介面的用戶端連接計數增加超過 4' 對隨機 I/O 沒有明顯影響，但順序 I/O 也不適用。 如下圖所示，每次增加都與讀取輸送量的相應增加相關。 由於 Azure 對每個實例類型/大小施加的網路頻寬限制，寫入輸送量保持不變。 

![順序 I/O 測試](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 對每個虛擬機器類型/大小進行網路速率限制。 速率限制僅對出站流量施加限制。 虛擬機器上存在的 NIC 數與電腦可用的頻寬總量無關。  例如，D16 實例類型施加的網路限制為 8000 Mbps（1，000 MiB/s）。  如上圖所示，限制會影響出站流量（寫入），但不會影響多通道讀取。

![順序 I/O 比較](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>是否建議加速聯網？

為了達到最佳性能，建議盡可能配置[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。 請記住以下注意事項：  

* 預設情況下，Azure 門戶為支援此功能的虛擬機器啟用加速網路。  但是，其他部署方法（如 Ansible 和類似的組態工具）可能不會。  如果啟用加速網路可能會妨礙機器的性能。  
* 如果由於虛擬機器缺少實例類型或大小的支援，因此未在虛擬機器的網路介面上啟用加速網路，則該介面將保持禁用狀態，實例類型較大。 在這種情況下，您將需要手動干預。

## <a name="are-jumbo-frames-supported"></a>支援巨型幀嗎？

Azure 虛擬機器不支援巨型幀。

## <a name="is-smb-signing-supported"></a>是否支援 SMB 簽名？ 

SMB 協定為檔和列印共用以及其他網路操作（如遠端 Windows 管理）提供了基礎。 為防止會修改傳輸中 SMB 封包的中間人攻擊，SMB 通訊協定支援 SMB 封包的數位簽章。 

對於 Azure NetApp 檔支援的所有 SMB 協定版本，都支援 SMB 簽名。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 簽名對性能有何影響？  

SMB 簽名對 SMB 性能有有害影響。 在性能下降的其他潛在原因中，每個資料包的數位簽章會消耗額外的用戶端 CPU，如下所述 perfmon 輸出所示。 在這種情況下，核心 0 似乎負責 SMB，包括 SMB 簽名。  與上一節中的非多通道順序讀取輸送量數位的比較顯示，SMB 簽名可將總體輸送量從 875MiB/s 降至大約 250MiB/s。 

![SMB 簽名性能影響](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>後續步驟  

- [關於 Azure NetApp 檔的常見問題解答](azure-netapp-files-faqs.md)
- 請參閱 Azure NetApp 檔：有關使用 SMB 檔共用的[SMB 工作負載的託管企業檔共用](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)。
