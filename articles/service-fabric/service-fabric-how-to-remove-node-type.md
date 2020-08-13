---
title: 在 Azure Service Fabric 中移除節點類型 | Microsoft Docs
description: 學習如何從在 Azure 中執行的 Service Fabric 叢集移除節點類型。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163572"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>如何移除 Service Fabric 節點類型
此文章說明如何透過將現有的節點類型從叢集移除，來調整 Azure Service Fabric 叢集的規模。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 建立 Service Fabric 叢集之後，您可以透過移除節點類型 (虛擬機器擴展集) 與其所有節點，來水平調整叢集規模。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

> [!WARNING]
> 不建議經常使用此方法從生產環境叢集移除節點類型。 它是非常危險的命令，因為它會刪除節點類型後的虛擬機器擴展集資源。 

## <a name="durability-characteristics"></a>持久性特性
使用 Remove-azservicefabricnodetype 時，安全性的優先順序高於速度。 節點類型必須是銀級或金級[持久性層級](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)，因為：
- 銅級不提供關於儲存狀態資訊的任何保證。
- 銀級和金級持久性可以攔截對擴展集的任何變更。
- 金級也可提供您對擴展集下 Azure 更新的控制。

Service Fabric 會「協調」基礎結構變更和更新，如此資料就不會遺失。 不過，當您移除具有銅級耐久性的節點類型時，可能會遺失狀態資訊。 如果您要移除主要節點類型，而您的應用程式是無狀態，則可接受銅級。 當您在生產環境中執行具狀態工作負載時，最低設定應該是銀級。 同樣地，針對生產環境案例，主要節點類型應該一律為銀級或金級。

### <a name="more-about-bronze-durability"></a>深入了解銅級持久性

當移除銅級的節點類型時，該節點類型中的所有節點會立即停機。 Service Fabric 不會攔截任何銅級節點擴展集更新，因此所有的 VM 會立即停機。 如果您有具狀態的任何項目在這些節點上，資料會遺失。 現在，即使您是無狀態的，Service Fabric 中的所有節點都會參與通道，因此整個鄰近的資料都會遺失，這可能會造成叢集本身不穩定。

## <a name="remove-a-node-type"></a>移除節點類型

1. 開始進行此程式之前，請先處理此必要條件。

    - 叢集狀況良好。
    - 移除節點類型後，仍然會有足夠的容量，例如 要放置所需複本計數的節點數目。

2. 移動具有放置條件約束的所有服務，以使用節點類型關閉節點類型。

    - 修改應用程式/服務資訊清單，以不再參考該節點類型。
    - 部署變更。

    然後驗證：
    - 上面修改的所有服務都不再于屬於節點類型的節點上執行。
    - 所有服務都狀況良好。

3. 取消標示節點類型為非主要 (略過非主要節點類型) 

    - 找出用於部署的 Azure Resource Manager 範本。
    - 在 [Service Fabric] 區段中，尋找與節點類型相關的區段。
    - 將 isPrimary 屬性變更為 false。 * * 請勿移除與此工作中的節點類型相關的區段。
    - 部署已修改的 Azure Resource Manager 範本。 * * 視叢集設定而定，此步驟可能需要一些時間。
    
    然後驗證：
    - 入口網站中的 Service Fabric 區段表示叢集已就緒。
    - 叢集狀況良好。
    - 屬於節點類型的節點都不會標示為種子節點。

4. 停用節點類型中的每個節點。

    使用 PowerShell 連接到叢集，然後執行下列步驟。
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - 針對銅級持久性，等待所有節點進入停用狀態
    - 對於銀級和金級耐久性，某些節點會進入停用狀態，而其餘節點則會處於停用狀態。 檢查處于停用狀態之節點的 [詳細資料] 索引標籤，如果它們全都停滯在確保基礎結構服務分割區的仲裁，則可以安全地繼續進行。

5. 停止節點類型的資料。

    使用 PowerShell 連接到叢集，然後執行下列步驟。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    等到節點類型的所有節點都標示為關閉為止。

6. 解除配置原始虛擬機器擴展集中的節點
    
    登入已部署擴展集的 Azure 訂用帳戶，並移除虛擬機器擴展集。 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. 移除節點類型的資料。

    使用 PowerShell 連接到叢集，然後執行下列步驟。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    等候所有節點都已從叢集移除。 節點不應顯示在 SFX 中。

8. 從 Service Fabric 區段移除節點類型。

    - 找出用於部署的 Azure Resource Manager 範本。
    - 在 [Service Fabric] 區段中，尋找與節點類型相關的區段。
    - 移除對應于節點類型的區段。
    - 僅適用于銀級和更高的耐久性叢集，請更新範本中的叢集資源，並設定健康原則以忽略 fabric：/系統應用程式健康狀態，方法是 `applicationDeltaHealthPolicies` 在叢集資源下方新增， `properties` 如下所示。 下列原則應忽略現有的錯誤，但不允許新的健全狀況錯誤。 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - 部署已修改的 Azure Resource Manager 範本。 * * 此步驟需要一些時間，通常最多兩小時。 此升級會將設定變更為 InfrastructureService，因此需要重新開機節點。 在此情況下 `forceRestart` ，會忽略。 
    參數 `upgradeReplicaSetCheckTimeout` 會指定 Service Fabric 等待分割區處於安全狀態的最長時間（如果尚未處於安全狀態）。 一旦對節點上的所有資料分割進行安全檢查，Service Fabric 會在該節點上繼續進行升級。
    參數的值 `upgradeTimeout` 可以縮短為6小時，但應使用最大安全12小時。

    然後驗證：
    - 入口網站中的 Service Fabric 資源會顯示 [就緒]。

9. 從 ARM 範本移除所有與節點類型相關之資源的參考。

    - 找出用於部署的 Azure Resource Manager 範本。
    - 從範本中移除虛擬機器擴展集和與節點類型相關的其他資源。
    - 部署變更。

    然後：
    - 等待部署完成。
    
10. 移除與不再使用之節點類型相關的資源。 範例 Load Balancer 和公用 IP。 

    - 若要移除這些資源，您可以使用步驟6中所使用的相同 PowerShell 命令來指定特定的資源類型和 API 版本。 

> [!Note]
> 如果相同的 Load Balancer，而且在節點類型之間重複使用 IP，則此步驟是選擇性的。

## <a name="next-steps"></a>後續步驟
- 深入了解叢集[持久性特性](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)。
- 深入了解[節點類型和虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。
- 深入了解[調整 Service Fabric 叢集](service-fabric-cluster-scaling.md)。
