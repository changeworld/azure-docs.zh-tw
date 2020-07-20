---
title: 容器和服務的資源管理
description: Azure Service Fabric 可讓您針對在容器內部或外部執行的服務指定資源限制。
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81115135"
---
# <a name="resource-governance"></a>資源管理

在相同的節點或叢集上執行多個服務時，有可能發生某個服務取用較多資源而導致處理序中的其他服務無資源可用的情況。 此問題稱為「擾鄰」問題。 Azure Service Fabric 可讓開發人員指定每一個服務的保留和限制，以確保有資源可用並限制資源使用量。

> 繼續本文之前，建議您先熟悉 [Service Fabric 應用程式模型](service-fabric-application-model.md)和 [Service Fabric 主控模型](service-fabric-hosting-model.md)。
>

## <a name="resource-governance-metrics"></a>資源管理計量

Service Fabric 可依[服務套件](service-fabric-application-model.md)支援資源控管。 指派給服務套件的資源可以進一步在程式碼套件之間分配。 指定的資源限制也意謂著資源的保留。 Service Fabric 支援使用兩個內建的[計量](service-fabric-cluster-resource-manager-metrics.md)來指定每個服務套件的 CPU 與記憶體：

* *CPU* (計量名稱 `servicefabric:/_CpuCores`)：主機上提供的邏輯核心。 所有節點上的所有核心加權都相同。

* *記憶體*（計量名稱 `servicefabric:/_MemoryInMB` ）：記憶體是以 mb 表示，而且會對應至機器上可用的實體記憶體。

針對這兩個計量，[叢集資源管理員](service-fabric-cluster-resource-manager-cluster-description.md)會追蹤叢集總容量、叢集中每個節點上的負載，以及叢集中剩餘的資源數。 這兩個計量相當於其他任何使用者或自訂計量。 所有現有的功能都可以搭配這些計量使用：

* 叢集可以根據這兩個計量來進行[平衡](service-fabric-cluster-resource-manager-balancing.md) (預設行為)。
* 叢集可以根據這兩個計量來進行[重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 在[描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)時，可以為這兩個計量設定緩衝處理的容量。

> [!NOTE]
> 這些計量不支援[動態負載報告](service-fabric-cluster-resource-manager-metrics.md);這些計量的載入會在建立時定義。

## <a name="resource-governance-mechanism"></a>資源控管機制

Service Fabric 執行階段目前不提供資源的保留。 當開啟處理序或容器時，執行階段會將資源限制設定為在建立時定義的負載。 再者，如果超出資源數，執行階段就會拒絕開啟可用的新服務套件。 為了進一步了解處理序的運作方式，讓我們來看具有兩個 CPU 核心的節點範例 (記憶體控管的機制都相同)：

1. 首先，容器會放在節點上，並要求一個 CPU 核心。 執行階段會開啟容器，並將 CPU 限制設定為一個核心。 容器無法使用多個核心。

2. 然後，服務的複本會放在節點上，且對應的服務套件會指定一個 CPU 核心的限制。 執行階段會開啟程式碼套件，並將其 CPU 限制設定為一個核心。

此時，限制的總和等於節點的容量。 處理序和容器會各使用一個核心執行，而不互相干擾。 Service Fabric 不會再放置任何容器或複本，以防它們指定 CPU 限制。

不過，有兩種情況其他處理序可能會爭用 CPU。 在這些情況下，我們範例中的處理序和容器可能會遇到擾鄰問題：

* *混合有控管和無控管的服務和容器*：如果使用者建立服務時沒有指定任何資源控管，執行階段會將它視為不會耗用任何資源，且能夠將它放在我們範例中的節點上。 在此情況下，這個新處理序會不客氣地耗用一些 CPU，犧牲已在節點上執行的服務。 此問題有兩個解決方案。 不要在相同叢集上混合有控管和無控管的服務，或使用[放置條件約束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)，讓這兩種類型的服務不會放在同一組節點上。

* *在 Service Fabric 外 (例如，作業系統服務) 的節點上啟動另一個處理序時*：在此情況下，Service Fabric 外的處理序也會與現有服務爭用 CPU。 這個問題的解決方法是設定節點容量時正確計入 OS 額外負荷，如下一節所述。

## <a name="cluster-setup-for-enabling-resource-governance"></a>設定叢集以啟用資源控管

當節點啟動並加入叢集時，Service Fabric 會偵測可用的記憶體數目與可用的核心數目，然後設定那兩個資源的節點容量。

為保留一些緩衝空間給作業系統和其他可能在節點上執行的處理序，Service Fabric 只會使用節點可用資源的 80%。 這個百分比可加以設定，並可在叢集資訊清單中變更。

以下範例指示 Service Fabric 使用可用 CPU 的 50% 以及可用記憶體的 70%：

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

對於大部分的客戶和案例而言，會自動偵測 CPU 和記憶體的節點容量，這是建議的設定（預設會開啟自動偵測）。 不過，如果您需要完全手動設定節點容量，您可以使用用來描述叢集中節點的機制來設定每個節點類型的。 以下範例說明如何設定具有四個核心和 2 GB 記憶體的節點類型：

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

當您啟用自動偵測可用資源，並手動定義叢集資訊清單中的節點容量時，Service Fabric 會檢查節點是否有足夠資源來支援使用者已經定義的容量：

* 如果資訊清單中定義的節點容量小於或等於節點上的可用資源，Service Fabric 會使用資訊清單中指定的容量。

* 如果資訊清單中定義的節點容量大於可用資源，Service Fabric 會使用可用資源作為節點容量。

如果非必要，可以關閉可用資源的自動偵測。 若要將它關閉，請變更下列設定：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

為了獲得最佳效能，在叢集資訊清單中應該也開啟下列設定：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> 從 Service Fabric 版本7.0 開始，我們已更新規則，在使用者手動提供節點資源容量的值的情況下，如何計算節點資源容量。 讓我們來看看下列案例：
>
> * 節點上有10個 cpu 核心總計
> * SF 設定為使用80% 的使用者服務總資源（預設值），這會為節點上執行的其他服務留下20% 的緩衝區（包括 Service Fabric 系統服務）
> * 使用者決定手動覆寫 cpu 核心計量的節點資源容量，並將其設定為5個核心
>
> 我們已變更如何以下列方式計算 Service Fabric 使用者服務可用容量的規則：
>
> * 在 Service Fabric 7.0 之前，使用者服務的可用容量會計算為**5 個核心**（忽略20% 的容量緩衝區）
> * 從 Service Fabric 7.0 開始，使用者服務的可用容量會計算為**4 個核心**（不會忽略20% 的容量緩衝區）

## <a name="specify-resource-governance"></a>指定資源控管

指定資源管理限制時，是在應用程式資訊清單 (ServiceManifestImport 區段) 中指定，如下列範例所示：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

在此範例中，稱為 **ServicePackageA** 的服務套件在其所在的節點上獲得一個核心。 此服務套件包含兩個程式碼套件（**CodeA1**和**CodeA2**），並同時指定 `CpuShares` 參數。 CpuShares 的比例 512:256 會將核心在這兩個程式碼套件做分配。

因此，在此範例中，CodeA1 會獲得 2/3 的核心，CodeA2 則獲得 1/3 的核心 (並具有同樣的彈性保證保留)。 如果未針對程式碼套件指定 CpuShares，Service Fabric 就會在它們之間平均分配核心。

記憶體限制是絕對的，因此這兩個程式碼套件都限制為 1024 MB 的記憶體 (並具有同樣的彈性保證保留)。 程式碼套件 (容器或處理序) 無法配置超過此限制的記憶體，如果嘗試這麼做，將會導致記憶體不足的例外狀況。 若要讓資源限制強制能夠運作，應該為服務套件內的所有程式碼套件都指定記憶體限制。

### <a name="using-application-parameters"></a>使用應用程式參數

指定資源治理設定時，您可以使用[應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)來管理多個應用程式設定。 下列範例會示範應用程式參數的使用方式：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

在此範例中，會針對生產環境設定預設參數值，其中每個服務套件會得到 4 個核心和 2 GB 的記憶體。 可以使用應用程式參數檔案變更預設值。 在此範例中，可以使用一個參數檔案在本機測試應用程式，其中應用程式會得到比生產環境中還少的資源：

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> 從 Service Fabric 版本 6.1 開始，可以使用應用程式參數指定資源控管。<br>
>
> 當應用程式參數用來指定資源控管時，Service Fabric 無法降級至 6.1 版之前的版本。

## <a name="enforcing-the-resource-limits-for-user-services"></a>強制執行使用者服務的資源限制

將資源治理套用至您的 Service Fabric 服務，可保證那些資源管控的服務不能超過其資源配額，而許多使用者仍然需要在 ungoverned 模式下執行部分的 Service Fabric 服務。 使用 ungoverned Service Fabric 服務時，可能會遇到「失控」 ungoverned 服務在 Service Fabric 節點上耗用所有可用資源的情況，這可能會導致嚴重的問題，例如：

* 節點上執行的其他服務資源不足（包括 Service Fabric 系統服務）
* 節點最後處於狀況不良狀態
* Service Fabric 叢集管理 Api 沒有回應

為避免發生這些情況，Service Fabric 可讓您 *針對節點上執行的所有 Service Fabric 使用者服務*（控管和 ungoverned）強制執行資源限制，以確保使用者服務永遠不會使用超過指定的資源量。 將 ClusterManifest 的 PlacementAndLoadBalancing 區段中的 EnforceUserServiceMetricCapacities config 值設定為 true，即可達成此目的。 預設會關閉此設定。

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

其他備註：

* 資源限制強制僅適用于 `servicefabric:/_CpuCores` 和 `servicefabric:/_MemoryInMB` 資源計量
* 只有在資源計量的節點容量可供 Service Fabric （透過自動偵測機制），或透過手動指定節點容量（如[啟用資源管理](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)的叢集設定一節中所述）的使用者，才可以強制執行資源限制。如果未設定節點容量，就無法使用資源限制強制功能，因為 Service Fabric 無法知道要為使用者服務保留多少資源。如果 "EnforceUserServiceMetricCapacities" 為 true，但未設定節點容量，Service Fabric 將會發出健康情況警告。

## <a name="other-resources-for-containers"></a>容器的其他資源

除了 CPU 和記憶體，也可以為容器指定其他資源限制。 這些限制是在程式碼套件層級指定，並會在容器啟動時套用。 不同於 CPU 和記憶體，叢集資源管理員不會知道這些資源，也不會進行任何容量檢查或負載平衡。

* *MemorySwapInMB*：容器可使用的交換記憶體數量。
* *MemoryReservationInMB*：記憶體管理的彈性限制，只有在節點上偵測到記憶體爭用的情況時才會強制執行。
* *CpuPercent*：容器可使用的 CPU 百分比。 若有指定服務套件的 CPU 限制，則會實際忽略此參數。
* *MaximumIOps*：容器可使用的 IOPS 上限 (讀取和寫入)。
* *MaximumIOBytesps*：容器可使用的 IO 上限 (位元組/秒) (讀取和寫入)。
* *BlockIOWeight*：區塊相對於其他容器的 IO 權數。

這些資源可以與 CPU 和記憶體結合。 以下是如何指定容器的其他資源的範例：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解叢集資源管理員，請閱讀 [Service Fabric 叢集資源管理員簡介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要深入了解應用程式模型、服務套件、程式碼套件，以及複本如何與它們對應，請閱讀[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)。
