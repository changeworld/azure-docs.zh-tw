---
title: Azure Service Fabric 應用程式與叢集最佳做法
description: 使用 Azure Service Fabric 管理叢集、應用程式和服務的最佳作法和設計考慮。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551772"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 應用程式與叢集最佳做法

本文提供管理 Azure Service Fabric 應用程式和叢集的最佳做法連結。 我們強烈建議您執行這些作法，以優化生產環境的可靠性。 使用其中一個[Service Fabric 叢集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)來開始設計您的生產解決方案，或更新現有的範本以併入這些實務。

## <a name="security"></a>安全性

* [安全性的最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>網路功能

* [建立網路的最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>計算規劃和調整

* [調整計算規模的最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [計算容量規劃](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>基礎結構即程式碼

* [實作基礎結構即程式碼性的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視和診斷

* [叢集監視和診斷的最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>應用程式設計

* [應用程式設計的最佳作法](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>檢查清單

在您執行前幾節中所建議的做法之後，請確定您已整合生產環境就緒檢查清單中的所有最佳作法：
* [Azure Service Fabric 生產環境就緒檢查清單](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解 Service Fabric：[疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)