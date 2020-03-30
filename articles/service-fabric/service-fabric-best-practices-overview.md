---
title: Azure Service Fabric 應用程式與叢集最佳做法
description: 使用 Azure 服務結構管理群集、應用和服務的最佳做法和設計注意事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551772"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 應用程式與叢集最佳做法

本文提供了管理 Azure 服務結構應用程式和群集的最佳做法的連結。 我們強烈建議您實施這些實踐，以優化生產環境的可靠性。 使用 Service [Fabric 群集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)之一開始設計生產解決方案，或更新現有範本以合併這些實踐。

## <a name="security"></a>安全性

* [安全性的最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>網路

* [建立網路的最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>計算規劃和調整

* [調整計算規模的最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [計算容量規劃](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>基礎結構即程式碼

* [實作基礎結構即程式碼性的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視和診斷

* [叢集監視和診斷的最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>應用程式設計

* [應用程式設計的最佳實踐](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>檢查清單

實施前幾節中建議的做法後，請確保在生產就緒檢查表中集成了所有最佳實踐：
* [Azure 服務結構生產就緒檢查表](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 故障排除服務結構：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)