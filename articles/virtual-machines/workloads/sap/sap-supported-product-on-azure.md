---
title: Azure 上的 SAP： Azure 上支援的 SAP 軟體
description: 說明支援在 Azure 上部署的 SAP 軟體
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
ms.openlocfilehash: 217d5b060ca1b7d32cd334049db9b7c68b7949a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065081"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支援哪些 SAP 軟體
本文說明如何找出 Azure 部署支援的 SAP 軟體，以及所需的作業系統版本或 DBMS 版本。

評估，無論您目前的 SAP 軟體是否受支援，以及您在 Azure 中的 SAP 軟體支援哪些 OS 和 DBMS 版本，您都需要存取：

- SAP 支援附注
- SAP 產品可用性對照表



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作負載的一般限制
可用於 SAP 工作負載的 Azure IaaS 服務僅限於 x86-64 或 x64 硬體。 沒有適用于 SAP 工作負載的 Sparc 或以電源 CPU 為基礎的供應專案。 在其應用程式上執行的客戶，是在與 IBM 大型主機或 AS400 等硬體架構專屬的作業系統上執行，或是在使用 HP-UX、Solaris 或 AIX 的作業系統上，需要將其 SAP 應用程式（包括 DBMS）變更為下列其中一個作業系統：

- X86-64 平臺的 Windows server 64 位
- 適用于 x86-64 平臺的 SUSE linux 64 位
- 適用于 x86-64 平臺的 Red hat Linux 64 位
- X86-64 平臺 Oracle Linux 64 位

與 SAP 軟體結合時，不支援其他作業系統版本或 Linux 發行版本。 有關特定版本和案例的確切詳細資料，將于稍後記載于檔中。


## <a name="you-start-here"></a>從這裡開始
您的起始點是 [SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 當您從上到下流覽此 SAP 附注時，會顯示數個支援的軟體和 Vm 區域

第一個區段列出一般 Azure Vm 中 SAP 軟體支援的作業系統最低需求。 如果您未達到這些最低需求，並執行這些作業系統的較舊版本，您必須將作業系統版本升級到最小版本或更新的版本。 這是正確的，Azure 通常會支援某些作業系統的較舊版本。 但是，所列出的限制或最小版本是以測試和執行的資格為基礎，且不會進一步延伸。 


> [!NOTE]
>有一些特定的 VM 類型、HANA 大型實例或 SAP 工作負載即將需要較新的 OS 版本。 這類案例將在整份檔中提及。 例如，在 SAP 附注或其他 SAP 發行集中，會清楚記載這類案例。

下一節列出支援的版本所支援的一般 SAP 平臺，以及支援的 SAP 核心。 它會列出支援的 NetWeaver/ABAP 或 JAVA 堆疊，且需要最小的核心版本。 Azure 支援較新的 ABAP 堆疊，但不需要最小的核心版本，因為 Azure 的變更是從開發的最新堆疊開始著手

您必須檢查：

- 您正在執行的 SAP 應用程式是否涵蓋于所述的最小版本。 如果不是，您需要定義新的目標版本、簽入 SAP 產品可用性對照表、新目標版本所支援的作業系統組建和 DBMS 組合。 因此，您可以選擇正確的作業系統版本和 DBMS 版本
- 您是否需要在移至 Azure 時更新 SAP 核心
- 您是否需要更新 SAP 支援封裝。 尤其是當您需要移至較新的 DBMS 版本時，可能需要的基礎支援封裝


下一節將詳細說明適用于 Windows 和 Linux Azure 上的 SAP 所支援的其他 SAP 產品和 DBMS 版本。 

> [!NOTE]
> 您可以謹慎選擇不同 DBMS 的最小版本，而不一定會反映在 Azure 上一般不同 DBMS 廠商支援的整個 DBMS 版本。 許多與 SAP 工作負載相關的考慮都已納入考慮，以定義這些最低版本。 測試和限定舊版 DBMS 版本並不會有任何努力。 

> [!NOTE]
> 所列的最小版本表示較舊版本的作業系統和資料庫版本。 我們強烈建議使用最新的作業系統版本和資料庫版本。 在許多情況下，較新的作業系統和資料庫版本會將在公用雲端中執行的使用案例納入考慮，並調整程式碼以優化以在公用雲端或更明確的 Azure 中執行

## <a name="oracle-dbms-support"></a>Oracle DBMS 支援
Azure 上支援的作業系統、Oracle DBMS 版本和 Oracle 功能，會特別列在 [SAP 支援附注 #2039619](https://launchpad.support.sap.com/#/notes/2039619)中。 這種情況下的本質摘要如下所示：

- 經 NetWeaver 認證的 Azure Vm 支援的最小 Oracle 版本為 Oracle 11g Release 2 Patchset 3 (11.2.0.4) 
- 僅限客體作業系統的 Windows 和 Oracle Linux 資格。 確切的作業系統版本和相關的最小 DBMS 版本會列在附注中
- Oracle Linux 的支援也會延伸至 Oracle DBMS 用戶端。 這表示所有 SAP 元件（像是 ABAP 或 JAVA 堆疊的對話實例）也都必須在 Oracle Linux 上執行。 只有這類 SAP 系統內不會連接到 Oracle DBMS 的 SAP 元件，才能執行不同的 Linux 作業系統
- 不支援 Oracle RAC 
- 某些情況下支援 Oracle ASM。 詳細資料列于附注中
- 只有使用 Windows 客體作業系統執行的應用程式伺服器才支援非 Unicode SAP 系統。 DBMS 的客體作業系統可以是 Oracle Linux 或 Windows。 當您檢查 SAP 產品可用性對照表 (PAM) 時，這項限制的原因很明顯。 針對 Oracle Linux，SAP 永不發行非 Unicode SAP 核心

如果您想要執行的 SAP 產品版本支援，請瞭解您所需的作業系統版本和 DBMS 所需的目標 Azure 基礎結構所支援的 DBMS 版本，以檢查 SAP 產品可用性對照表。 


## <a name="sap-hana-support"></a>SAP Hana 支援
在 Azure 中，有兩個可用來執行 HANA 資料庫的服務：

- Azure 虛擬機器
- [HANA 大型實例](./hana-overview-architecture.md)

針對執行 SAP Hana，SAP 比起執行 NetWeaver 或其他 SAP 應用程式和 DBMS，需要更多的條件來滿足基礎結構需求。 因此，較少數的 Azure Vm 符合執行 SAP Hana DBMS 的資格。 SAP Hana 支援的 Azure 基礎結構清單可以在所謂的 [SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中找到。 

> [!NOTE]
> 以字母 ' ' 開頭的單位是 [HANA 大型實例](./hana-overview-architecture.md) 單位。 

> [!NOTE]
> SAP 沒有特定的認證相依于 SAP Hana 的主要版本。 相反地，在[hana 認證 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中的資料行憑證**案例**中，資料行對**hana 主要或次要版本的認證沒有任何陳述**。 您必須假設所有列出的單位都可用於 HANA 1.0 和 HANA 2.0，只要 HANA 1.0 版本支援特定單位的認證作業系統版本即可。 

針對 SAP Hana 的使用方式，可能會有不同的最低 OS 版本適用于一般 NetWeaver 案例。 您必須針對每個單位個別檢查支援的作業系統，因為這些系統可能會有所不同。 若要這樣做，請按一下每個單位。 將會顯示更多詳細資料。 列出的其中一個詳細資料，是此特定單位支援的不同作業系統。

> [!NOTE]
> 相較于 Azure Vm，azure HANA 大型實例單位在支援的作業系統上受到更多的限制。 另一方面，Azure Vm 可能會強制執行較新版本的最新版本。 這特別適用于一些需要變更 Linux 核心的較大 VM 單位

若要瞭解 Azure 基礎結構支援的作業系統，您必須檢查 [SAP 支援附注 #2235581](https://launchpad.support.sap.com/#/notes/2235581) ，以瞭解您的目標 Azure 單位所支援的確切 SAP Hana 版本和修補程式等級。 

> [!IMPORTANT]
> 檢查確切 SAP Hana 版本和支援的修補程式層級的步驟非常重要。 在許多情況下，支援特定 OS 版本取決於 SAP Hana 可執行檔的特定修補程式等級。

當您知道您可以在目標 Azure 基礎結構上執行的特定 HANA 版本時，您需要簽入 SAP 產品可用性對照表，以瞭解支援您已篩選之 HANA 版本的 SAP 產品版本是否有限制


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>經認證的 Azure Vm 和 HANA 大型實例單位和商務交易輸送量
除了評估支援的作業系統版本、DBMS 版本和相依支援 Azure 基礎結構單位的 SAP 軟體版本之外，您還需要以 SAP 的單位「SAP」表示的商業交易輸送量來限定這些單位。 所有的 SAP 大小調整取決於 SAP 計算。 評估現有的 SAP 系統，您通常可以在基礎結構提供者的協助下，計算單位的 SAP。 適用于 DBMS 層和應用層。 在建立新功能的其他情況下，SAP 的調整大小練習可以顯示應用層和 DBMS 層所需的 SAP 數量。 作為基礎結構提供者，Microsoft 不必為提供 NetWeaver 和/或 HANA 認證的不同單位的 SAP 輸送量特性。

針對 Azure Vm， [sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中記載了這些 sap 輸送量數位。 針對 Azure HANA 大型實例單位， [sap 支援附注](https://launchpad.support.sap.com/#/notes/2316233)中記載了 sap 輸送量數位 #2316233

查看 [SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)，下列備註適用：

- **針對 M 系列的 Azure vm 和 Mv2-Series azure vm，不同的最低 OS 版本適用于其他 AZURE vm 類型**。 針對較新 OS 版本的需求，是根據不同作業系統廠商必須在其作業系統版本中提供的變更，以啟用在特定 Azure VM 類型上執行的作業系統，或將這些 VM 類型上的 SAP 工作負載效能和輸送量優化。
- 有兩個數據表指定不同的 VM 類型。 第二個數據表會針對僅支援 Azure 標準儲存體的 Azure VM 類型，指定 SAP 輸送量。 不支援在便箋的第二個數據表中指定的單位上進行 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 上支援的其他 SAP 產品
一般來說，假設有超大規模雲端（例如 Azure）的狀態，大部分的 SAP 軟體都應該在 Azure 中執行，而不會有任何功能問題。 和私用雲端的視覺效果相反，SAP 仍然針對不同的 hyerpscale 雲端提供者，明確地表示對不同 SAP 產品的支援。 因此，有不同的 SAP 支援附注指出不同 SAP 產品的 Azure 支援。 

針對商務物件 BI 平臺， [SAP 支援附注 #2145537](https://launchpad.support.sap.com/#/notes/2145537) 提供 Azure 上所支援的 Sap Business Objects 產品清單。 如果有關于軟體版本和作業系統版本的問題，而這些元件或軟體版本和作業系統版本的組合似乎未列出或受支援，而且比所列的最小版本還新，則您需要針對您查詢支援的元件開啟 SAP 支援要求。

針對商務物件資料服務， [SAP 支援附注 #22288344](https://launchpad.support.sap.com/#/notes/2288344) 說明 Azure 上執行之 SAP Data Services 的最小支援。 

> [!NOTE]
> 如 SAP 支援附注中所示，您必須簽入 SAP PAM，以找出在 Azure 上支援的正確支援套件層級。

[Sap 支援附注](https://launchpad.support.sap.com/#/notes/2464722)中詳細說明 Azure Kubernetes SERVICES (AKS) 中的 sap Datahub/Vora 支援 #2464722

Sap[支援附注](https://launchpad.support.sap.com/#/notes/2451795)中說明 sap BPC 10.1 SP08 的支援 #2451795

[Hybris 檔](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)會詳細說明 Azure 上的 SAP Hybris Commerce Platform 支援。 從 SAP Hybris Commerce 平臺支援的 DBMS 中，它會列出如下：

- Windows 作業系統平臺上的 SQL Server 和 Oracle。 相同的最小版本適用于 SAP NetWeaver。 如需詳細資訊，請參閱[SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Red Hat 和 SUSE Linux 上的 SAP Hana。 如 [本檔](#sap-hana-support)稍早所述，需要 SAP Hana 認證的 VM 類型。 SAP (Hybris) Commerce 平臺視為 OLTP 工作負載
- 從 SAP (Hybris) Commerce Platform 1811 版的 SQL Azure DB




## <a name="next-steps"></a>後續步驟
閱讀[適用于 SAP NetWeaver 的 Azure 虛擬機器規劃和執行](./planning-guide.md)中的後續步驟
