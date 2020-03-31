---
title: Azure 和獨立服務結構群集概述
description: 您可以在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。 這表示在任何環境中，只要有一組互連式 Windows Server 或 Linux 電腦，不論是在內部部署、Microsoft Azure 或透過任何雲端提供者，您都能夠部署和執行 Service Fabric 應用程式。
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751153"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>比較 Windows Server 和 Linux 上的 Azure 和獨立 Service Fabric 叢集

Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 隸屬於叢集的機器或 VM 稱為叢集模式。 叢集可擴充至數千個節點。 若您新增節點至叢集，則 Service Fabric 會重新平衡全體增加節點數的服務資料分割複本和執行個體。 整體應用程式效能會有所改善，改善，並減少爭用記憶體的存取權。 若未有效率地使用叢集中的節點，您可減少叢集中的節點數目。 Service Fabric 會再次重新平衡全體減少節點數的資料分割複本和執行個體，以善加使用每個節點上的硬體。

Service Fabric 可讓您在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。 這表示在任何環境中，只要有一組互連式 Windows Server 或 Linux 電腦，不論是在內部部署、Microsoft Azure 或透過任何雲端提供者，您都能夠部署和執行 Service Fabric 應用程式。

## <a name="benefits-of-clusters-on-azure"></a>Azure 上的叢集具備的優點

在 Azure 中，我們與其他的 Azure 功能和服務整合，因此能輕鬆可靠地操作與管理叢集。

* **Azure 入口網站：** Azure 入口網站能輕鬆建立和管理叢集。
* **Azure 資源管理員：** 使用 Azure Resource Manager 可輕鬆管理叢集做為單位使用的所有資源，並簡化成本追蹤與付費作業。
* **Service Fabric 叢集做為 Azure 資源** Service Fabric 叢集是 Azure 資源，因此可以像 Azure 中的其他資源進行模型化。
* **與 Azure 基礎結構整合** Service Fabric 會協調作業系統的 Azure 基礎結構、網路和其他升級，以改善應用程式的可用性和可靠性。  
* **診斷：** 在 Azure 上，我們提供與 Azure 診斷和 Azure 監視器日誌的集成。
* **自動調整：** 對於 Azure 上的叢集，我們會提供虛擬機器調整集產生的內建自動調整功能。 在內部部署與其他雲端環境中，您必須建置您自己的自動調整規模功能，或使用 Service Fabric 針對調整叢集規模顯示的 API 來手動調整規模。

## <a name="benefits-of-standalone-clusters"></a>獨立叢集具備的優點

* 您可以自由選擇任何雲端提供者來裝載您的叢集。
* Service Fabric 應用程式一旦撰寫完成，只需進行最低限度的變更或不需任何變更，即可在多個裝載環境中執行。
* 建置 Service Fabric 應用程式的知識可以從一個裝載環境轉移到其他裝載環境。
* 執行和管理 Service Fabric 叢集的操作經驗可以從一個環境轉移到其他環境。
* 不受裝載環境條件約束束縛的廣大客戶群。
* 有一層額外的可靠性和保護可防止發生廣泛的中斷狀況，因為您可以在資料中心或雲端提供發生中斷時，將服務移到另一個部署環境。

## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure 上的 Service Fabric 叢集](service-fabric-azure-clusters-overview.md)概觀
* 請參閱 [Service Fabric 獨立叢集](service-fabric-standalone-clusters-overview.md)概觀
* 瞭解[服務交換矩陣支援選項](service-fabric-support.md)