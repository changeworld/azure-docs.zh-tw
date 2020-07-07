---
title: 升級 Azure Service Fabric 獨立叢集
description: 了解如何升級 Service Fabric 獨立叢集的版本或組態。  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: ac3e80aae440c6709057e4211f7a4f6a1beea058
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82790638"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>升級和更新 Service Fabric 獨立叢集

對於任何現代系統，可升級性的設計是產品達到長期成功的關鍵。 Azure Service Fabric 獨立叢集是您擁有的資源。 本文說明可以升級或更新的項目。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制叢集上執行的網狀架構版本
請確定您的叢集一律會執行[支援的 Service Fabric 版本](service-fabric-versions.md)。 當 Microsoft 宣布發行新版本的 Service Fabric 時，從宣布當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。 那時就有新的版本可選擇。

您可以將叢集設定為在 Microsoft 釋出網狀架構升級時自動接收該升級，您也可以手動選取您想讓叢集執行的受支援網狀架構版本。 如需詳細資訊，請參閱[升級在叢集上執行的 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)。

## <a name="customize-configuration-settings"></a>自訂組態設定

在 ClusterConfig.json** 檔案中可設定許多不同的[組態設定](service-fabric-cluster-manifest.md)，例如叢集和節點屬性的可靠性層級。  若要深入了解，請參閱[升級獨立叢集的組態](service-fabric-cluster-config-upgrade-windows-server.md)。  也可以自訂其他許多更進階的設定。  如需詳細資訊，請參閱 [Service Fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)。

## <a name="define-node-properties"></a>定義節點屬性
有時候您可以確保工作負載只在叢集中的特定節點類型上執行。 例如，某些工作負載可能需要 GPU 或 SSD，而有些則不用。 針對叢集中的每種節點類型，您可以將自訂節點屬性新增至叢集節點。 條件約束是陳述式，會附加至針對一或多個節點屬性選取的個別服務。 放置條件約束會定義應該執行服務的位置。

如需使用放置條件約束、節點屬性及如何定義它們的詳細資訊，請參閱[節點屬性和放置條件約束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。
 

## <a name="add-capacity-metrics"></a>新增容量計量
對於每個節點類型，您可以加入您要在應用程式中用來報告負載的自訂容量計量。 如需使用容量度量報告負載的詳細資訊，請參閱《Service Fabric 叢集 Resource Manager 文件》的[描述您的叢集](service-fabric-cluster-resource-manager-cluster-description.md)和[度量和負載](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="patch-the-os-in-the-cluster-nodes"></a>修補叢集節點中的 OS
修補程式協調流程應用程式 (POA) 是 Service Fabric 應用程式，可在 Service Fabric 叢集上將作業系統修補自動化，而不需要停機。 [適用於 Windows 的修補程式協調流程應用程式](service-fabric-patch-orchestration-application.md)可在叢集上部署，以協調的方式安裝修補程式，同時讓服務隨時可供使用。 


## <a name="next-steps"></a>後續步驟
* 瞭解如何自訂一些[service fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)
* 了解如何 [相應放大和相應縮小叢集](service-fabric-cluster-scale-in-out.md)
* 瞭解[應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
