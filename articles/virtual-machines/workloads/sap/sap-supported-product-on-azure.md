---
title: Azure 上的 SAP： Azure 上支援的 SAP 軟體
description: 說明哪些 SAP 軟體支援部署在 Azure 上
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2f4e7a16c967b26b545d1405f973bf8b8afaae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086125"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支援哪些 SAP 軟體
本文說明如何找出 Azure 部署支援的 SAP 軟體，以及所需的作業系統版本或 DBMS 版本。

評估，無論您目前的 SAP 軟體是否受到支援，以及您的 SAP 軟體在 Azure 中支援的 OS 和 DBMS 版本為何，您都需要存取：

- SAP 支援附注
- SAP 產品可用性對照表



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作負載的一般限制
可用於 SAP 工作負載的 Azure IaaS 服務僅限於 x86-64 或 x64 硬體。 沒有適用于 SAP 工作負載的以 Sparc 或電源 CPU 為基礎的供應專案。 當客戶在其應用程式上執行的作業系統是以硬體架構（如 IBM 大型主機或 AS400）為目標，或是使用 HP-UX、Solaris 或 AIX 的作業系統時，需要將其 SAP 應用程式（包括 DBMS）變更為下列其中一個作業系統：

- 適用于 x86-64 平臺的 Windows server 64 位
- 適用于 x86-64 平臺的 SUSE linux 64 位
- 適用于 x86-64 平臺的 Red hat Linux 64 位
- 適用于 x86-64 平臺的 Oracle Linux 64 位

與 SAP 軟體結合時，不支援其他作業系統版本或 Linux 發行版本。 有關特定版本和案例的確切詳細資料，將在檔中稍後記載。


## <a name="you-start-here"></a>從這裡開始
您的起點是[SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 當您從上到下逐一流覽此 SAP 便箋時，會顯示支援的軟體和 Vm 的數個區域

第一節列出 Azure Vm 中的 SAP 軟體一般支援的作業版本最低需求。 如果您未達到這些最低需求，並執行這些作業系統的較舊版本，您必須將作業系統版本升級為這類最低版本或更新版本。 正確的是，Azure 一般會支援某些作業系統的較舊版本。 但是列出的限制或最低發行版本是根據所執行的測試和資格，而且不會進一步擴充。 


> [!NOTE]
>有一些特定的 VM 類型、HANA 大型實例或 SAP 工作負載即將需要較新的作業系統版本。 這類案例會在整份檔中提及。 這類案例會清楚記載在 SAP 附注或其他 SAP 發行集中。

下一節列出支援的版本所支援的一般 SAP 平臺，以及支援的 SAP 核心。 它會列出支援的 NetWeaver/ABAP 或 JAVA 堆疊，以及需要最小核心版本的程式。 Azure 上支援較新的 ABAP 堆疊，但不需要最小核心版本，因為 Azure 的變更是從最新堆疊的開發開始著手

您需要檢查：

- 您正在執行的 SAP 應用程式是否由所述的最低版本所涵蓋。 如果不是，您必須定義新的目標版本、簽入 SAP 產品可用性對照表、新的目標版本支援哪些作業系統組建和 DBMS 組合。 因此，您可以選擇正確的作業系統版本和 DBMS 版本
- 您是否需要在移至 Azure 的情況中更新 SAP 核心
- 是否需要更新 SAP 支援封裝。 特別是當您需要移至較新的 DBMS 版本時，可能需要的封裝。


下一節將詳細說明 Windows 和 Linux Azure 上的 SAP 所支援的其他 SAP 產品和 DBMS 版本。 

> [!NOTE]
> 會謹慎選擇不同 DBMS 的最小版本，而且可能不一定會反映不同 DBMS 廠商在 Azure 上支援的整個 DBMS 版本。 許多 SAP 工作負載的相關考慮已納入定義這些最低版本。 測試和驗證舊版的 DBMS 版本並沒有任何麻煩。 

> [!NOTE]
> 列出的最低發行版本代表舊版的作業系統和資料庫版本。 我們強烈建議使用最新的作業系統版本和資料庫版本。 在許多情況下，較新的作業系統和資料庫版本會將在公用雲端中執行的使用案例納入考慮，並調整程式碼以優化以在公用雲端或更具體的 Azure 中執行

## <a name="oracle-dbms-support"></a>Oracle DBMS 支援
Azure 上支援的 Oracle DBMS 版本和 Oracle 功能特別列于[SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 從該便箋中的本質，可以摘要如下：

- 已通過 NetWeaver 認證的 Azure Vm 支援的最低 Oracle 版本為 Oracle 11g Release 2 Patchset 3 （11.2.0.4）
- 身為客體作業系統，只有 Windows 和 Oracle Linux 符合資格。 便箋中會列出確切版本的 OS 和相關的最小 DBMS 版本
- Oracle Linux 的支援也會延伸至 Oracle DBMS 用戶端。 這表示所有 SAP 元件（例如 ABAP 或 JAVA 堆疊的對話方塊實例）也必須在 Oracle Linux 上執行。 只有在這類 SAP 系統內不會連線到 Oracle DBMS 的 SAP 元件，才能夠執行不同的 Linux 作業系統
- 不支援 Oracle RAC 
- 在某些情況下，支援 Oracle ASM。 詳細資料列在便箋中
- 非 Unicode SAP 系統僅支援搭配 Windows 客體作業系統執行的應用程式伺服器。 DBMS 的客體作業系統可以是 Oracle Linux 或 Windows。 檢查 SAP 產品可用性對照表（PAM）時，這種限制的原因很明顯。 針對 Oracle Linux，SAP 永遠不會發行非 Unicode SAP 核心

瞭解以目標 Azure 基礎結構支援的 DBMS 版本時，您必須檢查 SAP 產品可用性對照表，確認您要執行的 SAP 產品版本是否支援所需的作業系統版本和 DBMS。 


## <a name="sap-hana-support"></a>SAP Hana 支援
在 Azure 中，有兩項服務可用於執行 HANA 資料庫：

- Azure 虛擬機器
- [HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

針對執行 SAP Hana，SAP 具有比執行 NetWeaver 或其他 SAP 應用程式和 DBMS 更多且更強的條件基礎結構必須符合。 因此，較少數的 Azure Vm 符合執行 SAP Hana DBMS 的資格。 如需 SAP Hana 支援的 Azure 基礎結構清單，請參閱所謂的[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 

> [!NOTE]
> 以字母 ' ' 開頭的單位是[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)單位。 

> [!NOTE]
> SAP 沒有與 SAP Hana 主要版本相依的特定認證。 相反地，「 [hana 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)」中的「資料行**認證」案例**，此專欄不會對**hana 主要或次要發行認證提供任何聲明**。 您必須假設 HANA 1.0 版本也支援適用于 HANA 1.0 和 HANA 2.0 的所有單位，只要該特定單位的認證作業系統版本也可供使用。 

在使用 SAP Hana 時，可能會套用不同于一般 NetWeaver 案例的最低 OS 版本。 您需要個別檢查每個單位支援的作業系統，因為這些系統可能會有所不同。 若要這麼做，請按一下每個單位。 將會顯示更多詳細資料。 其中一個列出的詳細資料是針對此特定單位支援的不同作業系統。

> [!NOTE]
> 相較于 Azure Vm，Azure HANA 大型實例單位與支援的作業系統相比更具限制性。 另一方面，Azure Vm 可能會強制執行較新的作業版本（最少版本）。 尤其是某些需要變更 Linux 核心的較大 VM 單位時，更是如此

瞭解 Azure 基礎結構支援的作業系統時，您必須查看[SAP 支援附注 #2235581](https://launchpad.support.sap.com/#/notes/2235581) ，以取得您的目標 Azure 單位所支援的確切 SAP Hana 版本和修補層級。 

> [!IMPORTANT]
> 檢查所支援的確切 SAP Hana 版本和修補程式等級的步驟非常重要。 在許多情況下，特定 OS 版本的支援取決於 SAP Hana 可執行檔的特定修補程式等級。

當您知道可以在目標 Azure 基礎結構上執行的特定 HANA 版本時，您必須簽入 SAP 產品可用性對照表，以瞭解是否有 SAP 產品版本的限制，可支援您篩選出的 HANA 版本


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>經認證的 Azure Vm 和 HANA 大型實例單位和商務交易輸送量
除了評估支援的作業系統版本、DBMS 版本和相依支援適用于 Azure 基礎結構單位的 SAP 軟體版本之外，您還需要依據企業交易輸送量來限定這些單位，這在 SAP 中是以「SAP」單位來表示。 所有 SAP 大小調整都取決於 SAP 計算。 評估現有的 SAP 系統時，您通常可以透過基礎結構提供者的協助來計算單位的 SAP。 適用于 DBMS 層以及應用層。 在建立新功能的其他情況下，使用 SAP 進行大小調整的練習可以顯示應用層和 DBMS 層所需的 SAP 編號。 身為基礎結構提供者，Microsoft 不必提供 NetWeaver 和/或 HANA 認證之不同單位的 SAP 輸送量特性。

針對 Azure Vm，這些 sap 輸送量數位記載于[SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 針對「Azure HANA 大型實例單位」， [sap 支援附注](https://launchpad.support.sap.com/#/notes/2316233)中會記載 sap 輸送量數位 #2316233

查看[SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)，適用下列備註：

- **對於 M 系列的 Azure vm 和 Mv2 系列 Azure vm，不同的最低 OS 版本適用于其他 AZURE VM 類型**。 較新作業系統版本的需求是以不同作業系統版本所需提供的變更為基礎，讓他們在特定 Azure VM 類型上執行的作業系統，或將這些 VM 類型上 SAP 工作負載的效能和輸送量優化。
- 有兩個數據表會指定不同的 VM 類型。 第二個數據表會針對僅支援 Azure 標準儲存體的 Azure VM 類型，指定 SAP 輸送量。 不支援在便箋的第二個數據表中指定的單位上進行 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 上支援的其他 SAP 產品
一般來說，假設在超大規模資料庫雲端（例如 Azure）的狀態下，大部分的 SAP 軟體都應該在 Azure 中執行，而不會有任何功能問題。 儘管如此，與私用雲端的視覺效果相反，SAP 仍然會針對不同的 hyerpscale 雲端提供者明確地表示對不同 SAP 產品的支援。 因此，有不同的 SAP 支援附注指出不同 SAP 產品的 Azure 支援。 

針對商務物件 BI 平臺， [SAP 支援附注 #2145537](https://launchpad.support.sap.com/#/notes/2145537)提供 Azure 上支援的 SAP 商務物件產品清單。 如果有關于元件或軟體版本和作業系統版本組合的問題，但這些發行與未列出或支援的版本，以及比列出的最低發行版本還新，則您需要針對您查詢支援的元件開啟 SAP 支援要求。

針對資料服務的商務物件， [SAP 支援附注 #22288344](https://launchpad.support.sap.com/#/notes/2288344)說明在 Azure 上執行之 SAP 資料服務的最小支援。 

> [!NOTE]
> 如 SAP 支援附注所示，您必須簽入 SAP PAM 以識別在 Azure 上支援的正確支援封裝層級

Azure Kubernetes Services （AKS）中的 SAP Datahub/Vora 支援詳述于[sap 支援附注 #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Sap[支援附注](https://launchpad.support.sap.com/#/notes/2451795)中會描述 sap BPC 10.1 SP08 的支援 #2451795

[Hybris 檔](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)中會詳細說明 Azure 上的 SAP Hybris Commerce 平臺支援。 就 SAP Hybris Commerce 平臺支援的 DBMS 而言，它會列出如下：

- Windows 作業系統平臺上的 SQL Server 和 Oracle。 相同的最低發行版本適用于 SAP NetWeaver。 如需詳細資訊，請參閱[SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Red Hat 和 SUSE Linux 上的 SAP Hana。 如[本檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support)稍早所述，需要 SAP Hana 認證的 VM 類型。 SAP （Hybris） Commerce 平臺視為 OLTP 工作負載
- SAP 的 SQL Azure DB （Hybris）商務平臺版本1811




## <a name="next-steps"></a>後續步驟
閱讀[適用于 SAP NetWeaver 的 Azure 虛擬機器規劃與實施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的後續步驟

