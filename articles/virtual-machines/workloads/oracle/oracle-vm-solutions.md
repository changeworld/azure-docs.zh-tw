---
title: Azure 虛擬機器上的 Oracle 解決方案 | Microsoft Docs
description: 了解 Microsoft Azure 上支援的 Oracle 虛擬機器映像組態和限制。
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
ms.date: 05/12/2020
ms.author: borisb
ms.openlocfilehash: 27cc0c758a1f8cec5ad7f124f39c01c4e770676e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660523"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure 上的 Oracle VM 映像及其部署

本文涵蓋以 Oracle 在 Azure Marketplace 中發佈的虛擬機器映像為基礎的 Oracle 解決方案相關資訊。 如果您對使用 Oracle Cloud Infrastructure 的跨雲端應用程式解決方案感興趣，請參閱[整合 Microsoft Azure 與 Oracle Cloud Infrastructure 的 Oracle 應用程式解決方案](oracle-oci-overview.md)。

若要取得目前可用的映像清單，請執行下列命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

從 2020 年 5 月起，可取得下列映像：

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

系統會將這些映像視為「自備授權」，因此您只會支付執行 VM 產生的計算成本、儲存成本和網路成本。  其假設您已取得使用 Oracle 軟體的適當授權，且與 Oracle 之間已擁有支援合約。 Oracle 已保證從內部部署至 Azure 的授權行動性。 如需有關授權行動性的詳細資訊，請參閱已發佈的 [Oracle 和 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (英文) 附註。 

個人也可以選擇將其在 Azure 中從頭建立的自訂映像，或從內部部署環境上傳的自訂映像作為解決方案的基礎。

## <a name="oracle-database-vm-images"></a>Oracle 資料庫 VM 映像
Oracle 支援在以 Oracle Linux 為基礎的虛擬機器映像上，在 Azure 中執行 Oracle Database 12.1 及更高的 Standard 和 Enterprise 版本。  若要讓 Azure 上的 Oracle Database 生產工作負載提供最佳效能，請務必適當地調整 VM 映像的大小，並使用進階 SSD 或 Ultra SSD 受控磁碟。 如需有關如何使用 Oracle 已發佈之 VM 映像在 Azure 中快速地讓 Oracle Database 啟動並執行的指示，[請嘗試 Oracle Database 快速入門逐步解說](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>連接的磁碟組態選項

連接的磁碟依賴 Azure Blob 儲存體服務。 理論上每個標準磁碟最多都能達約每秒 500 輸入/輸出作業 (IOPS)。 我們的進階磁碟供應項目十分適用於高效能資料庫工作附載，且可達到每部磁碟最多 5000 IOPS。 如果單一磁片符合您的效能需求，即可加以使用。 不過，透過使用多個連接的磁碟，並將資料庫資料分散至其間，然後使用 Oracle Automatic Storage Management (ASM)，就能改善有效的 IOPS 效能。 如需有關 Oracle ASM 的特定詳細資訊，請參閱 [Oracle Automatic Storage 概觀](https://www.oracle.com/technetwork/database/index-100339.html) (英文)。 如需如何在 Linux Azure VM 上安裝和設定 Oracle ASM 的範例，請參閱[安裝和設定 Oracle Automatic Storage Management](configure-oracle-asm.md) 教學課程。

### <a name="shared-storage-configuration-options"></a>共用儲存體設定選項

Azure NetApp Files 的設計訴求是為了符合執行高效能工作負載 (例如雲端的資料庫) 的核心需求，並提供：
- Azure 原生共用 NFS 儲存體服務，以便透過 VM 原生 NFS 用戶端或 Oracle dNFS 執行 Oracle 工作負載
- 可調整的效能層級，以反映 IOPS 需求的實質範圍
- 低延遲
- 高可用性、高持久性和大規模管理，通常是由任務關鍵性企業工作負載 (例如 SAP 和 Oracle) 所要求
- 快速且有效率的備份和復原，以達到最積極的 RTO 和 RPO SLA

這些功能是可行的，因為 Azure NetApp Files 是以在 Azure 資料中心環境中執行的 NetApp® ONTAP® 全快閃系統為基礎 – 作為 Azure 原生服務。 結果是可以像其他 Azure 儲存體選項一樣佈建和取用的理想資料庫儲存體技術。 如需如何部署和存取 Azure NetApp Files NFS 磁碟區的詳細資訊，請參閱 [Azure NetApp Files 文件](https://docs.microsoft.com/azure/azure-netapp-files/)。 如需在 Azure NetApp Files 上操作 Oracle 資料庫的最佳做法建議，請參閱 [Oracle on Azure Deployment Best Practice Guide Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)。


## <a name="licensing-oracle-database--software-on-azure"></a>Azure 上的授權 Oracle Database 和軟體
Microsoft Azure 是執行 Oracle Database 的授權雲端環境。 在雲端授權 Oracle 資料庫時，不適用 Oracle Core Factor 資料表。 相反地，使用已針對 Enterprise Edition 資料庫啟用超執行緒技術的 VM 時，如果已啟用超執行緒，請將兩個虛擬 CPU 計算為等同於一個 Oracle Processor 授權 (如原則文件所述)。 在[這裡](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)可以找到原則詳細資料。
Oracle 資料庫通常需要較高的記憶體和 IO。 基於這個理由，建議針對這些工作負載使用[記憶體最佳化 VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。 若要將您的工作負載進一步最佳化，建議針對需要高記憶體、儲存體和 I/O 頻寬，但不是高核心計數的 Oracle Database 工作負載使用[限制核心虛擬 CPU](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu)。

將 Oracle 軟體和工作負載從內部部署遷移至 Microsoft Azure 時，Oracle 會提供如 [Oracle on Azure FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) 所述的授權行動性。


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Clusters (Oracle RAC) 是用來在內部部署的多節點叢集組態中，減少發生單一節點錯誤的機率。 它依賴以下兩個內部部署技術，而這兩個技術並非大規模公用雲端環境的原生技術︰網路多點傳送和共用磁碟。 若您的資料庫解決方案需要位在 Azure 中的 Oracle RAC，則您會需要第三方軟體才能啟用這些技術。 如需 Oracle RAC 的詳細資訊，請參閱 [FlashGrid SkyCluster 頁面](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和災害復原考量
在 Azure 中使用 Oracle 資料庫時，您必須負責實作高可用性和災害復原解決方案，以避免任何停機。 

在 Azure 上可以使用 [Data Guard、Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 或 [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate) 來達成 Oracle Database Enterprise Edition 的高可用性和災害復原 (不需倚賴 Oracle RAC)，且兩個資料庫位於兩個不同的虛擬機器上。 這兩個虛擬機器應該位於相同的[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)中，以確保它們可以透過永續私人 IP 位址互相存取。  此外，建議您將虛擬機器放置於相同的可用性設定組，讓 Azure 將其放置於不同的容錯網域和升級網域。 如果您想要擁有異地備援，請將這兩個資料庫設定為在兩個不同的區域之間進行複寫，並透過 VPN 閘道連接兩個執行個體。

[在 Azure 上實作 Oracle Data Guard](configure-oracle-dataguard.md) 教學課程會引導您在 Azure 上完成基本設定程序。  

使用 Oracle Data Guard，可以藉由某個虛擬機器中的主要資料庫、另一個虛擬機器中的次要 (待命) 資料庫以及它們之間的單向複寫設定，達到高可用性。 這樣讀取作業存取的會是資料庫的複本。 使用 Oracle GoldenGate，您則可以設定兩個資料庫之間的雙向複寫。 若要了解如何使用這些工具為資料庫設定高可用性解決方案，請參閱 Oracle 網站上的 [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文件 (英文)。 如需資料庫複本的讀取-寫入存取權，您可以使用 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

[在 Azure 上實作 Oracle GoldenGate](configure-oracle-golden-gate.md) 教學課程會引導您在 Azure 上完成基本設定程序。

除了已在 Azure 中建構 HA 和 DR 解決方案架構，您還應該擁有可用於還原資料庫的備份策略。 [備份及復原 Oracle 資料庫](oracle-backup-recovery.md)教學課程會引導您完成建立一致性備份的基本程序。


## <a name="support-for-jd-edwards"></a>JD Edwards 的支援
根據 Oracle 支援附註 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)，符合其特定 `Minimum Technical Requirements` (MTR) 的**任何公用雲端供應項目**都支援 JD Edwards EnterpriseOne 9.2 版和更新版本。  您必須建立符合其 OS 和軟體應用程式相容性之 MTR 規格的自訂映像。 


## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server 虛擬機器供應項目

Oracle 正與 Microsoft 攜手合作，以一組 Azure 應用程式供應項目的形式將 WebLogic Server 帶入 Azure Marketplace。  [Oracle WebLogic Server Azure 應用程式](oracle-weblogic.md)一文會說明這些供應項目。

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虛擬機器映像

* **只有在 Enterprise Edition 上才支援叢集。** 只有當您使用的是 Oracle WebLogic Server Enterprise Edition 時，才會獲授權使用 WebLogic 叢集。 請勿在使用 Oracle WebLogic Server Standard Edition 時使用叢集。
* **不支援 UDP 多點傳送。** Azure 支援 UDP 單點傳播，但不支援多點傳送或廣播。 Oracle WebLogic Server 可以依賴 Azure 的 UDP 單點傳播功能。 若要依賴 UDP 單點傳播獲得最佳結果，建議 WebLogic 叢集大小保持靜態，或保持不超過 10 部受控伺服器。
* **Oracle WebLogic Server 預期針對 T3 存取的公用和私人連接埠是相同的 (例如，使用 Enterprise JavaBeans 時)。** 請考慮使用多層式案例，其中服務層 (EJB) 應用程式會在 Oracle WebLogic Server 叢集上執行，而該叢集包含兩個或多個 VM，且都位於名為 *SLWLS* 的虛擬網路中。 用戶端層位於相同虛擬網路中的不同子網路，並執行簡單的 Java 程式，嘗試呼叫服務層中的 EJB。 由於負載平衡服務層是必要動作，所以必須先為 Oracle WebLogic Server 叢集中的虛擬機器建立公用負載平衡端點。 如果您指定的私人連接埠與公用連接埠不同 (例如，7006:7008)，則會發生下列錯誤：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   這是因為對於任何遠端 T3 存取，Oracle WebLogic Server 預期負載平衡器連接埠和 WebLogic 受控伺服器連接埠是相同的。 在上述情況中，用戶端正在存取連接埠 7006 (負載平衡器連接埠)，而受控伺服器正在接聽 7008 (私人連接埠)。 這項限制只適用於 T3 存取，不適用於 HTTP。

   若要避免此問題，請使用下列其中一種因應措施：

  * 針對 T3 存取專用的負載平衡端點使用相同的私人和公用連接埠號碼。
  * 啟動 Oracle WebLogic Server 時包含下列 JVM 參數：

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

如需相關資訊，請參閱位於 <https://support.oracle.com> 的 KB 文章 **860340.1**。

* **動態叢集和負載平衡限制。** 假設您想要在 Oracle WebLogic Server 中使用動態叢集，並透過 Azure 中的單一、公用負載平衡端點將其公開。 做法是只要您使用各個受控伺服器的固定連接埠號碼 (不是從範圍動態指派)，且不要啟動比系統管理員正在追蹤之機器的數量還多的受控伺服器。 也就是每台虛擬機器不超過一個受控伺服器。 若您的設定會導致啟動的 Oracle WebLogic Server 多於可用的虛擬機器 (也就是多個 Oracle WebLogic Server 執行個體共用相同的虛擬機器)，則那些 Oracle WebLogic Server 執行個體中只有一個可以繫結到指定的連接埠號碼。 該虛擬機器上的其他執行個體則會失敗。

   若您設定管理伺服器自動將唯一的連接埠號碼指派給其受控伺服器，則無法進行負載平衡，因為 Azure 不支援從單一公用連接埠對應至多個私人連接埠，而此設定中需要這樣做。
* **虛擬機器上的多個 Oracle Weblogic Server 執行個體。** 根據您的部署需求，如果虛擬機器夠大，您可以考慮在相同虛擬機器上執行多個 Oracle WebLogic Server 的執行個體。 例如，在中型大小虛擬機器上 (包含 2 個核心)，您可以選擇執行兩個 Oracle WebLogic Server 的執行個體。 不過，仍然建議您避免將單一失敗點引入您的架構，如果您僅使用一個執行多個 Oracle WebLogic Server 執行個體的虛擬機器也是如此。 使用至少兩個虛擬機器可能是較好的方法，每個虛擬機器可以執行多個 Oracle WebLogic Server 執行個體。 每個 Oracle WebLogic Server 的執行個體仍可以是相同叢集的一部分。 不過，目前無法使用 Azure 對相同虛擬機器內此類 Oracle WebLogic Server 部署所公開的端點進行負載平衡，因為 Azure 負載平衡器需要負載平衡伺服器在唯一的虛擬機器內散佈。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虛擬機器映像
* **JDK 6 和 7 最新更新。** 雖然建議使用 Java 的最新公開支援版本 (目前為 Java 8)，Azure 也提供 JDK 6 和 7 映像。 這是針對尚未準備升級至 JDK 8 的舊版應用程式。 雖然舊版 JDK 映像的更新可能不再提供一般大眾使用，憑藉 Microsoft 與 Oracle 的合作夥伴關係，Azure 提供的 JDK 6 和 7 映像會包含最新的非公用更新，該更新通常是由 Oracle 僅提供給 Oracle 支援客戶的選取群組。 新版本的 JDK 映像與 JDK 6 和 7 的更新版本會在一段時間內供取用。

   JDK 6 和 7 映像中可用的 JDK，以及從中衍生的虛擬機器和映像，只能在 Azure 內使用。
* **64 位元 JDK。** Oracle WebLogic Server 虛擬機器映像和 Azure 所提供的 Oracle JDK 虛擬機器映像包含 Windows Server 和 JDK 的 64 位元版本。

## <a name="next-steps"></a>後續步驟
您現在已大致了解以 Microsoft Azure 中的虛擬機器映像為基礎的現行 Oracle 解決方案。 下一個步驟是在 Azure 上部署第一個 Oracle 資料庫。

> [!div class="nextstepaction"]
> [在 Azure 上建立 Oracle 資料庫](oracle-database-quick-create.md)
