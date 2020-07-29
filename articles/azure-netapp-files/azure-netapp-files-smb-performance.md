---
title: 適用于 Azure NetApp Files 的 SMB 效能常見問題 |Microsoft Docs
description: 針對 Azure NetApp Files 的 SMB 效能解答常見問題。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460444"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>適用于 Azure NetApp Files 的 SMB 效能常見問題

本文會針對 Azure NetApp Files 的 SMB 效能最佳做法，回答相關的常見問題（Faq）。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Smb 共用中是否已啟用 SMB 多重通道？ 

是，預設會啟用 SMB 多重通道，這是在2020年1月的初期進行的變更。 所有 SMB 共用預先可追溯的現有 SMB 磁片區已啟用此功能，而且所有新建立的磁片區也會在建立時啟用此功能。 

在功能啟用之前建立的任何 SMB 連線都必須重設，才能利用 SMB 多重通道功能。 若要重設，您可以中斷連線，然後重新連線到 SMB 共用。

## <a name="is-rss-supported"></a>支援 RSS 嗎？

是，Azure NetApp Files 支援接收端調整（RSS）。

啟用 SMB 多重通道後，SMB3 用戶端會透過可支援單一 RSS 的網路介面卡（NIC），建立多個與 Azure NetApp Files SMB 伺服器的 TCP 連線。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支援 SMB 多重通道？

Windows 自 Windows 2012 開始支援 SMB 多重通道，以實現最佳效能。  如需詳細資訊，請參閱[部署 Smb 多重](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))通道和[smb 多重通道的基本概念](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/)。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虛擬機器是否支援 RSS？

若要查看您的 Azure 虛擬機器 Nic 是否支援 RSS，請如下所示執行命令， `Get-SmbClientNetworkInterface` 並檢查欄位 `RSS Capable` ： 

![Azure 虛擬機器的 RSS 支援](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files 是否支援 SMB Direct？

否，Azure NetApp Files 不支援 SMB 直接傳輸。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多重通道的優點為何？ 

「SMB 多重通道」功能可讓 SMB3 用戶端透過單一網路介面卡（NIC）或多個 Nic 建立連線集區，並使用它們來傳送單一 SMB 會話的要求。 相反地，根據設計，SMB1 和 SMB2 會要求用戶端建立一個連線，並透過該連線傳送指定會話的所有 SMB 流量。 這個單一連接會限制可以從單一用戶端達成的整體通訊協定效能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>我是否應該在用戶端上設定 SMB 的多個 Nic？

否。 SMB 用戶端會符合 SMB 伺服器傳回的 NIC 計數。  每個存放磁片區都只能從一個儲存體端點存取。  這表示任何指定的 SMB 關聯性都只會使用一個 NIC。  

如以下的輸出所 `Get-SmbClientNetworkInterace` 示，虛擬機器有兩個網路介面：-15 和12。  如下列命令底下所示 `Get-SmbMultichannelConnection` ，即使有兩個支援 RSS 的 nic，但只有介面12會用於與 SMB 共用連線; 介面15不在使用中。

![支援 RSS 的 NIC](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 中支援 NIC 小組嗎？

Azure 不支援 NIC 小組。 雖然 Azure 虛擬機器支援多個網路介面，但它們代表邏輯，而不是實體結構。 因此，它們不提供容錯功能。  此外，Azure 虛擬機器可用的頻寬是針對機器本身計算，而不是任何個別的網路介面。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 多重通道的效能為何？

下列測試和圖形示範單一實例工作負載上 SMB 多重通道的強大功能。

### <a name="random-io"></a>隨機 i/o  

在用戶端上停用 SMB 多重通道時，純粹的 8-KiB 讀取和寫入測試是使用 FIO 和 40-GiB 工作集來執行。  SMB 共用已在每個測試之間卸離，而每個 RSS 網路介面設定的 SMB 用戶端連線計數增量為、、、 `1` `4` `8` `16` 、 `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` 。 測試顯示的預設設定 `4` 足以應付需要大量 i/o 的工作負載; 遞增至 `8` 且 `16` 不會有任何作用。 

此命令 `netstat -na | findstr 445` 證明已建立額外的連接，其遞增從 `1` 到 `4` 到以及到 `8` `16` 。  在每個測試期間，已將四個 CPU 核心完全使用於 SMB，如 perfmon 統計資料所確認 `Per Processor Network Activity Cycles` （不包含在本文中）。

![隨機 i/o 測試](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虛擬機器不會影響 SMB （或 NFS）儲存體 i/o 限制。  如下所示，針對快取的儲存體 IOPS，D16 實例類型的比率為32000，而針對未快取的儲存體 IOPS 則為25600。  不過，上圖顯示 SMB 的 i/o 明顯較多。

![隨機 i/o 比較](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>連續 IO 

類似上述隨機 i/o 測試的測試是以 64-KiB 順序 i/o 來執行。 雖然每個 RSS 網路介面的用戶端連接計數增加超過4個不會對隨機 i/o 造成明顯的影響，但這同樣不適用於順序 i/o。 如下圖所示，每個增加都會與相對應的讀取輸送量增加相關聯。 因為 Azure 針對每個實例類型/大小所放置的網路頻寬限制，所以寫入輸送量會保持不變。 

![順序 i/o 測試](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 會在每個虛擬機器類型/大小上放置網路速率限制。 速率限制只會強加于輸出流量。 虛擬機器上的 Nic 數目與電腦可用的總頻寬量無關。  例如，D16 實例類型的網路限制為 8000 Mbps （1000 MiB/s）。  如上圖所示，此限制會影響輸出流量（寫入），但不會影響多重通道讀取。

![順序 i/o 比較](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>建議使用加速網路嗎？

為了達到最大效能，建議您盡可能設定[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。 請記住下列考慮：  

* 根據預設，Azure 入口網站會針對支援此功能的虛擬機器啟用加速網路。  不過，其他部署方法（例如 Ansible 和類似的設定工具）可能不會。  若無法啟用加速網路，可能會妨礙電腦的效能。  
* 如果虛擬機器的網路介面上未啟用加速網路，因為它不支援實例類型或大小，它會維持以較大的實例類型來停用。 在這些情況下，您將需要手動介入。

## <a name="are-jumbo-frames-supported"></a>是否支援巨型框架？

Azure 虛擬機器不支援巨型框架。

## <a name="is-smb-signing-supported"></a>是否支援 SMB 簽署？ 

SMB 通訊協定提供檔案和列印共用以及其他網路作業（例如遠端 Windows 系統管理）的基礎。 為防止會修改傳輸中 SMB 封包的中間人攻擊，SMB 通訊協定支援 SMB 封包的數位簽章。 

Azure NetApp Files 支援的所有 SMB 通訊協定版本都支援 SMB 簽章。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 簽章的效能影響為何？  

SMB 簽署在 SMB 效能上具有不利的效果。 在效能降低的其他潛在原因中，每個封包的數位簽章會耗用額外的用戶端 CPU，如以下的 perfmon 輸出所示。 在此情況下，核心0會出現負責 SMB 的，包括 SMB 簽署。  與上一節中非多重通道連續讀取輸送量數位的比較顯示，SMB 簽署會將整體輸送量從 875MiB/s 降至大約 250MiB/秒。 

![SMB 簽章效能影響](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>後續步驟  

- [關於 Azure NetApp Files 的常見問題](azure-netapp-files-faqs.md)
- 如需有關使用 SMB 檔案共用搭配 Azure NetApp Files 的[Smb 工作負載，請參閱 Azure Netapp files：受控企業檔案共用](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)。
