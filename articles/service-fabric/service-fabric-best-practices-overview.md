---
title: Azure Service Fabric 應用程式與叢集最佳做法
description: 使用 Azure Service Fabric 來管理叢集、應用程式和服務的最佳作法和設計考慮。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261095"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 應用程式與叢集最佳做法

本文提供管理 Azure Service Fabric 應用程式和叢集的最佳作法連結。 強烈建議您執行這些作法，以優化生產環境的可靠性。 使用其中一個 [Service Fabric 叢集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates) 開始設計您的生產解決方案，或更新現有的範本以納入這些作法。

## <a name="security"></a>安全性

* [安全性的最佳作法](service-fabric-best-practices-security.md)

## <a name="networking"></a>網路功能

* [建立網路的最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>計算規劃和調整

* [調整計算規模的最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [計算容量規劃](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>基礎結構即程式碼

* [實作基礎結構即程式碼性的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視和診斷

* [叢集監視和診斷的最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>應用程式設計

* [應用程式設計的最佳作法](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>檢查清單

在您執行上述各節所建議的作法之後，請確定您已在生產環境就緒檢查清單中整合所有最佳作法：
* [Azure Service Fabric 生產環境就緒狀態檢查清單](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>接下來的步驟

* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解 Service Fabric： [疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
