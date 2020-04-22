---
title: Azure 虛擬機上的 Oracle 解決方案 |微軟文件
description: 瞭解 Microsoft Azure 上支援的 Oracle 虛擬機器映像的配置和限制。
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: borisb
ms.openlocfilehash: 0cee7c25960d567c75a14d8ad9ef95b3e7221862
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683424"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 映像及其在 Microsoft Azure 上的部署

本文介紹有關基於 Oracle 在 Azure 應用商店中發布的虛擬機映射的 Oracle 解決方案的資訊。 如果您對使用 Oracle 雲端基礎架構的跨雲端應用程式解決方案感興趣,請參閱[整合 Microsoft Azure 和 Oracle 雲端結構的 Oracle 應用程式解決方案](oracle-oci-overview.md)。

若要取得目前可用的映像清單，請執行下列命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

截至 2019 年 5 月,提供以下影像:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

系統會將這些映像視為「自備授權」，因此您只會支付執行 VM 產生的計算成本、儲存成本和網路成本。  其假設您已取得使用 Oracle 軟體的適當授權，且與 Oracle 之間已擁有支援合約。 Oracle 已保證從內部部署至 Azure 的授權行動性。 如需有關授權行動性的詳細資訊，請參閱已發佈的 [Oracle 和 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (英文) 附註。 

個人也可以選擇將其在 Azure 中從頭建立的自訂映像，或從內部部署環境上傳的自訂映像作為解決方案的基礎。

## <a name="oracle-database-vm-images"></a>Oracle 資料庫 VM 映像
Oracle 支援在基於 Oracle Linux 的虛擬機器映像上執行 Azure 中的 Oracle 資料庫 12.1 以及更高版本的標準和企業版。  為了在 Azure 上為 Oracle 資料庫的生產工作負載提供最佳性能,請確保正確調整 VM 映射的大小,並使用先進 SSD 或超 SSD 託管磁碟。 有關如何使用 Oracle 的 VM 映像在 Azure 中快速啟動與執行 Oracle 資料庫的說明,[請試著 Oracle 資料庫快速入門演練](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>連接的磁碟組態選項

連接的磁碟依賴 Azure Blob 儲存體服務。 理論上每個標準磁碟最多都能達約每秒 500 輸入/輸出作業 (IOPS)。 我們的進階磁碟供應項目十分適用於高效能資料庫工作附載，且可達到每部磁碟最多 5000 IOPS。 如果滿足性能需求,則可以使用單個磁碟。 但是,如果您使用多個附加磁碟,跨磁碟擴展資料庫數據,然後使用 Oracle 自動儲存管理 (ASM),則可以提高有效的 IOPS 性能。 如需有關 Oracle ASM 的特定詳細資訊，請參閱 [Oracle Automatic Storage 概觀](https://www.oracle.com/technetwork/database/index-100339.html) (英文)。 有關如何在 Linux Azure VM 上安裝和配置 Oracle ASM 的範例,請參閱[安裝和配置 Oracle 自動儲存管理](configure-oracle-asm.md)教學。

### <a name="shared-storage-configuration-options"></a>共用儲存設定選項

Azure NetApp 檔旨在滿足在雲中運行高性能工作負載(如資料庫)的核心要求,並提供;
- Azure 本機共用 NFS 儲存服務,用於透過 VM 本機 NFS 用戶端或 Oracle dNFS 執行 Oracle 工作負載
- 可擴充的效能層,反映 IOPS 需求的實際範圍
- 低延遲
- 高可用性、高耐用性和大規模可管理性,通常是關鍵任務企業工作負載(如 SAP 和 Oracle)要求的
- 快速高效的備份和恢復,實現最具侵略性的 RTO 和 RPO SLA

這些功能是可能的,因為 Azure NetApp 檔案基於 NetApp® ONTAP®在 Azure 資料中心環境中運行的所有快閃記憶體系統 , 作為 Azure 本機服務。 結果是理想的資料庫存儲技術,可以像其他 Azure 儲存選項一樣進行預配和使用。 有關如何部署和存取 Azure NetApp 檔 NFS 卷的詳細資訊,請參閱[Azure NetApp 檔案文件](https://docs.microsoft.com/azure/azure-netapp-files/)。 有關[在 Azure NetApp 檔](https://www.netapp.com/us/media/tr-4780.pdf)上運行 Oracle 資料庫的最佳做法建議,請參閱 Azure 部署最佳實務指南"使用 Azure NetApp 檔。


## <a name="licensing-oracle-database--software-on-azure"></a>許可 Oracle 資料庫& Azure 上的軟體
Microsoft Azure 是用於運行 Oracle 資料庫的授權雲端環境。 在雲中許可 Oracle 資料庫時,Oracle 核心因數表不適用。 相反,當使用為企業版資料庫啟用的超線程技術的 VM 時,如果啟用了超線程(如策略文檔中所述),則將兩個 vCPU 計算為相當於一個 Oracle 處理器許可證。 政策詳情可[在此處](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)找到。
Oracle 資料庫通常需要更高的記憶體和IO。 因此,建議這些工作負載使用[記憶體優化 VM。](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 為了進一步優化工作負載,建議對需要高記憶體、存儲和I/O頻寬但內核計數較高的Oracle資料庫工作負載進行[受限核心vCPU。](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu)

將 Oracle 軟體和工作負載從本地遷移到 Microsoft Azure 時,Oracle 提供了許可證行動性,如[Azure 常見問題解答中的 Oracle](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)中所述


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle 實際應用程式群集 (Oracle RAC) 旨在緩解本地多節點群集配置中單個節點的故障。 它依賴以下兩個內部部署技術，而這兩個技術並非大規模公用雲端環境的原生技術︰網路多點傳送和共用磁碟。 如果資料庫解決方案在 Azure 中需要 Oracle RAC,則需要第三方軟體來啟用這些技術。 有關 Oracle RAC 的詳細資訊,請參閱[FlashGrid 天空叢集頁面](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和災害復原考量
在 Azure 中使用 Oracle 資料庫時,您負責實現高可用性和災難恢復解決方案,以避免任何停機。 

使用[數據衛士、活動數據衛士](https://www.oracle.com/database/technologies/high-availability/dataguard.html)或[Oracle GoldenGate,](https://www.oracle.com/technetwork/middleware/goldengate)在 Azure 上可實現 Oracle 資料庫企業版的高可用性和災難恢復(不依賴於 Oracle RAC),兩個資料庫位於兩個單獨的虛擬機上。 這兩個虛擬機器應該位於相同的[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)中，以確保它們可以透過永續私人 IP 位址互相存取。  此外，建議您將虛擬機器放置於相同的可用性設定組，讓 Azure 將其放置於不同的容錯網域和升級網域。 如果要具有異地冗餘,請將兩個資料庫設置為在兩個不同的區域之間複製,並將這兩個實例與 VPN 閘道連接。

本教程[在 Azure 上實現 Oracle 數據保護](configure-oracle-dataguard.md),引導您完成 Azure 上的基本設置過程。  

使用 Oracle Data Guard，可以藉由某個虛擬機器中的主要資料庫、另一個虛擬機器中的次要 (待命) 資料庫以及它們之間的單向複寫設定，達到高可用性。 這樣讀取作業存取的會是資料庫的複本。 使用 Oracle GoldenGate，您則可以設定兩個資料庫之間的雙向複寫。 若要了解如何使用這些工具為資料庫設定高可用性解決方案，請參閱 Oracle 網站上的 [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文件 (英文)。 如需資料庫複本的讀取-寫入存取權，您可以使用 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

在[Azure 上實現 Oracle 金門的](configure-oracle-golden-gate.md)教程將引導您完成 Azure 上的基本設置過程。

除了在 Azure 中建構 HA 和 DR 解決方案外,還應制定備份策略來還原資料庫。 本教學[「備份和恢復 Oracle 資料庫」](oracle-backup-recovery.md)將引導您完成建立一致備份的基本過程。


## <a name="support-for-jd-edwards"></a>JD Edwards 的支援
根據 Oracle 支援說明[Doc ID 2178595.1,JD](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)Edwards EnterpriseOne`Minimum Technical Requirements`版本 9.2 及以上支援滿足其 特定 (MTR)**的任何公共雲產品**。  您必須建立符合其 OS 和軟體應用程式相容性之 MTR 規格的自訂映像。 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虛擬機器映像

* **只有在 Enterprise Edition 上才支援叢集。** 只有在使用 Oracle WebLogic 伺服器的企業版時,您才有權使用 WebLogic 群集。 請勿將群集與 Oracle WebLogic 伺服器標準版一起使用。
* **不支援 UDP 多點傳送。**  Azure 支援 UDP 單點傳播，但不支援多點傳送或廣播。 Oracle WebLogic 伺服器能夠依賴於 Azure UDP 單播功能。 為了獲得最佳結果,依靠 UDP 單播,我們建議 WebLogic 群集大小保持靜態,或保留不超過 10 個託管伺服器。
* **Oracle WebLogic 伺服器希望 T3 訪問的公共埠和專用埠相同(例如,在使用企業 JavaBeans 時)。** 請考慮一個多層方案,其中服務層 (EJB) 應用程式在 Oracle WebLogic Server 群集上運行,該群集由兩個或多個 VM 組成,位於名為*SLWLS*的虛擬網路中。 用戶端層位於同一虛擬網路中的不同子網中,運行一個簡單的 Java 程式,試圖在服務層中調用 EJB。 由於需要負載平衡服務層,因此需要為 Oracle WebLogic Server 群集中的虛擬機器創建公共負載平衡終結點。 如果您指定的私人連接埠與公用連接埠不同 (例如，7006:7008)，則會發生下列錯誤：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   這是因為對於任何遠端 T3 訪問,Oracle WebLogic 伺服器都希望負載均衡器埠和 WebLogic 託管伺服器埠相同。 在上述情況中，用戶端正在存取連接埠 7006 (負載平衡器連接埠)，而受控伺服器正在接聽 7008 (私人連接埠)。 這項限制只適用於 T3 存取，不適用於 HTTP。

   若要避免此問題，請使用下列其中一種因應措施：

  * 針對 T3 存取專用的負載平衡端點使用相同的私人和公用連接埠號碼。
  * 啟動 Oracle WebLogic 伺服器時包括以下 JVM 參數:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

如需相關資訊，請參閱位於 <https://support.oracle.com> 的 KB 文章 **860340.1**。

* **動態叢集和負載平衡限制。** 假設您想在 Oracle WebLogic Server 中使用動態群集,並透過 Azure 中的單個公共負載平衡終結點公開它。 只要對每個託管伺服器(不是動態分配從範圍)使用固定埠號(而不是動態分配),並且啟動的託管伺服器不會超過管理員正在跟蹤的電腦,就可以執行此操作。 也就是說,每個虛擬機不超過一台託管伺服器)。 如果您的設定導致啟動的 Oracle WebLogic 伺服器多於虛擬機器(即多個 Oracle WebLogic Server 實體共用同一虛擬機器),則 Oracle WebLogic 伺服器的這些實體中不可能綁定到給定埠號。 該虛擬機上的其他計算機失敗。

   若您設定管理伺服器自動將唯一的連接埠號碼指派給其受控伺服器，則無法進行負載平衡，因為 Azure 不支援從單一公用連接埠對應至多個私人連接埠，而此設定中需要這樣做。
* **虛擬機上的 Oracle WebLogic 伺服器的多個實體。** 根據部署的要求,如果虛擬機器足夠大,則可以考慮在同一虛擬機上運行 Oracle WebLogic Server 的多個實例。 例如,在包含兩個內核的中型虛擬機器上,您可以選擇運行 Oracle WebLogic Server 的兩個實例。 但是,我們仍建議您避免在體系結構中引入單點故障,如果您只使用一台運行 Oracle WebLogic Server 多個實例的虛擬機器,則就是這種情況。 使用至少兩個虛擬機可能是一種更好的方法,然後每個虛擬機可以運行 Oracle WebLogic Server 的多個實例。 Oracle WebLogic 伺服器的每個實例可能仍屬於同一群集。 但是,目前無法使用 Azure 來負載平衡由同一虛擬機中的此類 Oracle WebLogic Server 部署公開的終結點,因為 Azure 負載均衡器要求負載平衡伺服器在唯一虛擬機之間分配。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虛擬機器映像
* **JDK 6 和 7 最新更新。**  雖然建議使用 Java 的最新公開支援版本 (目前為 Java 8)，Azure 也提供 JDK 6 和 7 映像。 這是針對尚未準備升級至 JDK 8 的舊版應用程式。 雖然舊版 JDK 映像的更新可能不再提供一般大眾使用，憑藉 Microsoft 與 Oracle 的合作夥伴關係，Azure 提供的 JDK 6 和 7 映像會包含最新的非公用更新，該更新通常是由 Oracle 僅提供給 Oracle 支援客戶的選取群組。 新版本的 JDK 映像與 JDK 6 和 7 的更新版本會在一段時間內供取用。

   JDK 6 和 7 映射中的 JDK 以及從這些映射派生的虛擬機和映射只能在 Azure 中使用。
* **64 位元 JDK。**  Oracle WebLogic Server 虛擬機器映像和 Azure 所提供的 Oracle JDK 虛擬機器映像包含 Windows Server 和 JDK 的 64 位元版本。

## <a name="next-steps"></a>後續步驟
現在,您可以根據 Microsoft Azure 中的虛擬機器映像概述當前的 Oracle 解決方案。 下一步是在 Azure 上部署第一個 Oracle 資料庫。

> [!div class="nextstepaction"]
> [在 Azure 上建立 Oracle 資料庫](oracle-database-quick-create.md)
