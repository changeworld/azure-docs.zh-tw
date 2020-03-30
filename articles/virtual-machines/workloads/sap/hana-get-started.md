---
title: 在 Azure 虛擬機器上安裝 SAP HANA |微軟文檔
description: 在 Azure VM 上安裝 SAP HANA 指南
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123358"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>在 Azure 虛擬機器上安裝 SAP HANA
## <a name="introduction"></a>簡介
本指南可説明您指出在 Azure 虛擬機器中成功部署 HANA 的正確資源。 本指南將指向在 Azure VM 中安裝 SAP HANA 之前需要檢查的文檔資源。 因此，您將能夠執行正確的步驟，以 Azure VM 中支援的 SAP HANA 配置結束。  

> [!NOTE]
> 本指南說明如何將 SAP HANA 部署到 Azure VM。 有關如何將 SAP HANA 部署到 HANA 大型實例中的資訊，請參閱[如何在 Azure 上安裝和配置 SAP HANA（大型實例）。](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
 
## <a name="prerequisites"></a>Prerequisites
本指南還假定您熟悉：
* SAP HANA 與 SAP NetWeaver，以及如何加以內部部署安裝。
* 如何在 Azure 上安裝和操作 SAP HANA 和 SAP 應用程式實例。
* 記錄在以下方面的概念和程式：
   * 規劃 Azure 上的 SAP 部署，其中包括 Azure 虛擬網路規劃和 Azure 存儲使用方式。 有關[Azure 虛擬機器，請參閱 SAP NetWeaver - 規劃和實施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * 部署原則及在 Azure 中部署 VM 的方式。 請參閱[SAP 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * SAP HANA 的高可用性概念，如[SAP HANA 中所示，適用于 Azure 虛擬機器的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>部署前的分步
在本節中，列出了在 Azure 虛擬機器中開始安裝 SAP HANA 之前需要執行的不同步驟。 順序將被枚舉，因此應遵循列舉如下：

1. Azure 上並非支援所有可能的部署方案。 因此，您應該檢查 Azure[虛擬機器支援的方案上的文檔 SAP 工作負荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)，瞭解 SAP HANA 部署時所牢記的方案。 如果未列出方案，則需要假定該方案尚未測試，因此不受支援
2. 假設您對 SAP HANA 部署的記憶體要求有大致的想法，則需要查找合適的 Azure VM。 並非所有經過 SAP NetWeaver 認證的[VM（如 SAP 支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)中所述）均經過 SAP HANA 認證。 SAP HANA 認證 Azure VM 的真相來源是 SAP [HANA 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 以**S**開頭的單位是[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)單元，而不是 Azure VM。
3. 不同的 Azure VM 類型具有不同的 SUSE Linux 或紅帽 Linux 的最小作業系統版本。 在[SAP HANA 硬體目錄中](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，您需要點擊 SAP HANA 認證單元清單中的條目，以獲取此單元的詳細資料。 除了支援的 HANA 工作負載外，還列出了 SAP HANA 的這些單元支援的作業系統版本
4. 在作業系統版本時，需要考慮某些最小內核版本。 這些最低版本記錄在這些 SAP 支援說明中：
    - [SAP 支援說明#2814271 SAP HANA 備份在 Azure 上失敗，但檢查與錯誤](https://launchpad.support.sap.com/#/notes/2814271)
    - [由於計時器回退，SAP 支援說明#2753418潛在的性能下降](https://launchpad.support.sap.com/#/notes/2753418)
    - [由於 Azure 中缺少對超 V 的 VDSO 支援，SAP 支援說明#2791572性能下降](https://launchpad.support.sap.com/#/notes/2791572)
4. 根據為選擇的虛擬機器類型支援的作業系統版本，您需要檢查該作業系統版本是否支援所需的 SAP HANA 版本。 閱讀[SAP 支援說明#2235581，](https://launchpad.support.sap.com/#/notes/2235581)瞭解具有不同作業系統版本的 SAP HANA 版本的支援矩陣。
5. 由於您可能已經找到了 Azure VM 類型、作業系統版本和 SAP HANA 版本的有效組合，因此您需要簽入 SAP 產品可用性矩陣。 在 SAP 可用性矩陣中，您可以瞭解是否要針對 SAP HANA 資料庫運行的 SAP 產品。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>分步 VM 部署和客體作業系統注意事項
在此階段，您需要執行部署 VM 以安裝 HANA 並最終在安裝後優化所選作業系統的步驟。

1. 從 Azure 庫中選擇基本映射。 如果要為 SAP HANA 構建自己的作業系統映射，則需要瞭解成功安裝 SAP HANA 所需的所有不同套裝軟體。 否則，建議在 Azure 映射庫中使用 SAP 或 SAP HANA 的 SUSE 和紅帽映射。 這些映射包括成功安裝 HANA 所需的套裝軟體。 根據您與作業系統供應商的支援合同，您需要選擇自己攜帶許可證的映射。 或者，您可以選擇包含支援的作業系統映射
2. 如果您選擇了需要您自帶許可證的客體作業系統映射，則需要在訂閱中註冊作業系統映射，以便下載並應用最新的修補程式。 此步驟將需要公共互聯網接入。 除非您在 Azure 中設置了 SMT 伺服器的專用實例。
3. 決定 VM 的網路設定。 您可以在文檔中閱讀 SAP [HANA 基礎結構配置和 Azure 上的操作中的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)更多資訊。 請記住，沒有可以分配給 Azure 中的虛擬網卡的網路輸送量配額。 因此，通過不同的 vNIC 定向流量的唯一目的是基於安全注意事項。 我們相信您能夠在通過多個 vNIC 的流量路由的複雜性和安全方面強制實施的要求之間找到可支援的折衷方案。
3. 部署和註冊 VM 後，將最新的修補程式應用於作業系統。 使用您自己的訂閱註冊。 或者，如果您選擇了包含作業系統支援的映射，VM 應已經有權訪問修補程式。 
4. 應用 SAP HANA 所需的調子。 這些調子列在這些 SAP 支援說明中：

    - [#2694118 SAP 支援說明 - Azure 上的紅帽企業 Linux HA 附加元件](https://launchpad.support.sap.com/#/notes/2694118)
    - [#1984787 SAP 支援說明 - SUSE LINUX 企業伺服器 12：安裝說明](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 支援說明#2578899 - SUSE Linux 企業伺服器 15：安裝說明](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 支援說明#2002167 - 紅帽企業 Linux 7.x：安裝和升級](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP 支援說明#2772999 - 紅帽企業 Linux 8.x：安裝和配置](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 支援說明#2777782 - SAP HANA DB：RHEL 8 的推薦作業系統設置](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 支援說明#2455582 - Linux：運行使用 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP 支援說明#2382421 - 在 HANA 和作業系統級別上優化網路設定](https://launchpad.support.sap.com/#/notes/2382421)

1. 選擇 SAP HANA 的 Azure 存儲類型。 在此步驟中，您需要決定 SAP HANA 安裝的存儲佈局。 您將使用附加的 Azure 磁片或本機 Azure NFS 共用。 SAP [HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中記錄了支援或支援的 Azure 存儲類型以及可以使用的不同 Azure 存儲類型的組合。 以記錄為起點的配置為例。 對於非生產系統，您可能能夠配置較低的輸送量或 IOPS。 出於生產目的，您可能需要配置更多的輸送量和 IOPS。
2. 請確保在使用 M 系列或 Mv2 系列 VM 時為包含 DBMS 事務日誌的卷配置[Azure 寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或重做日誌。 請注意已記錄的寫入加速器的限制。
2. 檢查是否已在部署的 VM 上啟用[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。

> [!NOTE]
> 不同樹智調設定檔或注釋中描述的所有命令都可能在 Azure 上成功運行。 操作 VM 的電源模式的命令通常會返回時出現錯誤，因為無法操作基礎 Azure 主機硬體的電源模式。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>特定于 Azure 虛擬機器的分步準備
Azure 的一個細節是安裝 Azure VM 擴展，該擴展為 SAP 主機代理提供監視資料。 有關安裝此監視擴展的詳細資訊記錄在：

-  [SAP 注釋 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)討論在 Azure 上使用 Linux VM 進行 SAP 增強的監視 
-  [SAP 注釋 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)討論有關 Linux 上的 SAPOSCOL 的資訊 
-  [SAP 注釋 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)討論 Microsoft Azure 上 SAP 的關鍵監視指標
-  [SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 安裝
部署 Azure 虛擬機器並註冊和配置作業系統後，可以根據 SAP 安裝安裝 SAP HANA。 作為獲取本文檔的良好開端，從此 SAP 網站[HANA 資源](https://www.sap.com/products/hana/implementation/resources.html)開始

對於使用直接連接的 Azure 高級存儲或超磁片磁片的 SAP HANA 橫向擴展配置，請閱讀文檔中的[SAP HANA 基礎結構配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 備份的其他資源
有關如何在 Azure VM 上備份 SAP HANA 資料庫的資訊，請參閱：
* [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>後續步驟
閱讀文檔：

- [SAP HANA 在 Azure 上的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA Azure 虛擬機器儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





