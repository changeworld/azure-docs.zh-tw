---
title: 在 Azure 虛擬機器上安裝 SAP Hana |Microsoft Docs '
description: 在 Azure Vm 上安裝 SAP Hana 的指南
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80123358"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>在 Azure 虛擬機器上安裝 SAP Hana
## <a name="introduction"></a>簡介
本指南可協助您指向正確的資源，以順利在 Azure 虛擬機器中部署 HANA。 本指南將引導您前往在 Azure VM 中安裝 SAP Hana 之前，您需要檢查的檔資源。 因此，您可以執行正確的步驟，以在 Azure Vm 中支援的 SAP Hana 設定來結束。  

> [!NOTE]
> 本指南說明如何將 SAP HANA 部署到 Azure VM。 如需如何將 SAP Hana 部署到 HANA 大型實例的相關資訊，請參閱[如何在 Azure 上安裝和設定 SAP Hana （大型實例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)。
 
## <a name="prerequisites"></a>必要條件
本指南也假設您已經熟悉：
* SAP HANA 與 SAP NetWeaver，以及如何加以內部部署安裝。
* 如何在 Azure 上安裝和操作 SAP Hana 和 SAP 應用程式實例。
* 中記載的概念和程式：
   * 在 Azure 上規劃 SAP 部署，其中包括 Azure 虛擬網路規劃和 Azure 儲存體使用量。 請參閱[Azure 上的 SAP NetWeaver 虛擬機器-規劃與實施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * 部署原則及在 Azure 中部署 VM 的方式。 請參閱[適用于 SAP 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)中所述 SAP Hana 的高可用性概念

## <a name="step-by-step-before-deploying"></a>部署前的逐步解說
在本節中，會列出不同的步驟，您必須在開始安裝 Azure 虛擬機器中的 SAP Hana 之前執行。 順序會列舉出來，因此應遵循列舉：

1. Azure 不支援所有可能的部署案例。 因此，您應該查看[Azure 虛擬機器支援案例上的 SAP 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)檔，以瞭解您的 SAP Hana 部署所需考慮的案例。 如果未列出案例，您必須假設它尚未經過測試，因此不受支援。
2. 假設您大致瞭解 SAP Hana 部署的記憶體需求，您需要尋找適合的 Azure VM。 並非所有經 SAP NetWeaver 認證的 Vm （如[sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中所述）都 SAP Hana 認證。 SAP Hana 認證的 Azure Vm 的真實來源是[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)的網站。 以**S**開頭的單位是[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)單位，而不是 Azure vm。
3. 不同的 Azure VM 類型對於 SUSE Linux 或 Red Hat Linux 具有不同的最低作業系統版本。 在 [網站[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)] 上，您需要按一下 SAP Hana 認證單位清單中的專案，以取得此單元的詳細資料。 除了支援的 HANA 工作負載之外，也會列出 SAP Hana 的這些單位所支援的作業系統版本
4. 從作業系統版本的發行，您必須考慮特定的最低核心版本。 這些最低版本記載于下列 SAP 支援附注中：
    - [SAP 支援附注 #2814271 在 Azure 上 SAP Hana 備份失敗，發生總和檢查碼錯誤](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP 支援附注 #2753418 因計時器回溯而造成的潛在效能降低](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP 支援附注 #2791572 效能降低，因為 Azure 中的 Hyper-v 缺少 VDSO 支援](https://launchpad.support.sap.com/#/notes/2791572)
4. 根據所選虛擬機器類型所支援的作業系統版本，您必須檢查該作業系統版本是否支援您所需的 SAP Hana 版本。 如需具有不同作業系統版本之 SAP Hana 版本的支援對照表，[請參閱 SAP 支援附注 #2235581](https://launchpad.support.sap.com/#/notes/2235581) 。
5. 您可能已經找到 Azure VM 類型、作業系統版本和 SAP Hana 版本的有效組合，您必須簽入 SAP 產品可用性矩陣。 在 SAP 可用性矩陣中，您可以瞭解是否支援您想要針對 SAP Hana 資料庫執行的 SAP 產品。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>逐步執行 VM 部署和客體作業系統的考慮
在此階段中，您需要執行部署 VM 的步驟來安裝 HANA，最後在安裝後將所選擇的作業系統優化。

1. 已從 Azure 映射庫選擇基底映射。 如果您想要建立自己的作業系統映射以進行 SAP Hana，您需要知道成功安裝 SAP Hana 所需的所有不同套件。 否則，建議使用適用于 SAP 的 SUSE 和 Red Hat 映射，或從 Azure 映射庫中 SAP Hana。 這些映射包含成功安裝 HANA 所需的套件。 根據您與作業系統提供者的支援合約，您必須選擇可攜帶自己的授權的映射。 或者，您可以選擇包含支援的作業系統映射
2. 如果您選擇需要自備授權的客體作業系統映射，則必須向您的訂用帳戶註冊 OS 映射，如此一來，您就可以下載並套用最新的修補程式。 此步驟將需要公用網際網路存取。 除非您設定的私用實例（例如 Azure 中的 SMT 伺服器）。
3. 決定 VM 的網路設定。 您可以在[Azure 上 SAP Hana 基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)檔中閱讀詳細資訊。 請記住，您不能將網路輸送量配額指派給 Azure 中的虛擬網路卡。 因此，將流量導向不同 Vnic 的唯一目的是根據安全性考慮。 我們信任您在透過多個 Vnic 的流量路由複雜性與安全性層面強制執行的需求之間，找到可支援性的危害。
3. 部署並註冊 VM 之後，請將最新的修補程式套用至作業系統。 已使用您自己的訂用帳戶註冊。 或者，如果您選擇包含作業系統支援的映射，VM 應該可以存取已有的修補程式。 
4. 套用 SAP Hana 所需的微調。 這些微調列于這些 SAP 支援附注中：

    - [SAP 支援附注 #2694118-Red Hat Enterprise Linux Azure 上的 HA 附加元件](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP 支援附注 #1984787-SUSE LINUX Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 支援附注 #2578899-SUSE Linux Enterprise Server 15：安裝注意事項](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 支援附注 #2002167-Red Hat Enterprise Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP 支援附注 #2772999-Red Hat Enterprise Linux 8.x：安裝和設定](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 支援附注 #2777782-SAP Hana DB：適用于 RHEL 8 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 支援附注 #2455582-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP 支援附注 #2382421-優化 HANA 和 OS 層級上的網路設定](https://launchpad.support.sap.com/#/notes/2382421)

1. 選取適用于 SAP Hana 的 Azure 儲存體類型。 在此步驟中，您必須決定用於 SAP Hana 安裝的儲存體配置。 您將使用已連接的 Azure 磁片或原生 Azure NFS 共用。 您可以在[SAP Hana azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中，記錄或支援的 azure 儲存體類型，以及可使用的不同 azure 儲存體類型組合。 將設定記錄為起始點。 對於非生產系統，您可能可以設定較低的輸送量或 IOPS。 基於生產目的，您可能需要設定更多的輸送量和 IOPS。
2. 當您使用 M 系列或 Mv2 系列 Vm 時，請確定您已為包含 DBMS 交易記錄或重做記錄的磁片區設定[Azure 寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。 請留意所記載寫入加速器的限制。
2. 檢查已部署的 VM 上是否已啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。

> [!NOTE]
> 並非不同 sap 微調設定檔中的所有命令，或如附注中所述，可能會在 Azure 上順利執行。 會操控 Vm 電源模式的命令通常會傳回錯誤，因為無法操作基礎 Azure 主機硬體的電源模式。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure 虛擬機器特定的逐步準備
其中一個 Azure 詳細資訊是安裝 Azure VM 擴充功能，以提供 SAP 主機代理程式的監視資料。 此監視擴充功能的安裝詳細資料記載于：

-  [Sap Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)討論使用 Azure 上的 Linux VM 進行 SAP 增強監視 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)討論 Linux 上的 SAPOSCOL 相關資訊 
-  [Sap Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)討論 sap on Microsoft Azure 的主要監視計量
-  [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 安裝
部署 Azure 虛擬機器並註冊並設定作業系統之後，您就可以根據 SAP 安裝來安裝 SAP Hana。 若要開始使用此檔集，請從這個 SAP 網站[HANA 資源](https://www.sap.com/products/hana/implementation/resources.html)開始著手

如需使用 Azure 進階儲存體或 Ultra 磁片的直接連接磁片 SAP Hana 相應放大設定，請參閱[azure 上 SAP Hana 基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)檔中的詳細資訊


## <a name="additional-resources-for-sap-hana-backup"></a>SAP Hana 備份的其他資源
如需如何備份 Azure Vm 上 SAP Hana 資料庫的相關資訊，請參閱：
* [Azure 虛擬機器上 SAP Hana 的備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>後續步驟
閱讀檔：

- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





