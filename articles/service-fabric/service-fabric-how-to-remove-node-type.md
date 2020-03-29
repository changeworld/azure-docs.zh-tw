---
title: 在 Azure Service Fabric 中移除節點類型 | Microsoft Docs
description: 學習如何從在 Azure 中執行的 Service Fabric 叢集移除節點類型。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969413"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>如何刪除服務結構節點類型
此文章說明如何透過將現有的節點類型從叢集移除，來調整 Azure Service Fabric 叢集的規模。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 建立 Service Fabric 叢集之後，您可以透過移除節點類型 (虛擬機器擴展集) 與其所有節點，來水平調整叢集規模。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

> [!WARNING]
> 不建議經常使用此方法從生產群集中刪除節點類型。 它是非常危險的命令，因為它會刪除節點類型後的虛擬機器擴展集資源。 

## <a name="durability-characteristics"></a>持久性特性
使用"刪除-AzServiceFabricNodeType"時，安全性優先于速度。 節點類型必須是銀級或金級[持久性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，因為：
- 銅級不提供關於儲存狀態資訊的任何保證。
- 銀級和金級持久性可以攔截對擴展集的任何變更。
- 金級也可提供您對擴展集下 Azure 更新的控制。

Service Fabric 會「協調」基礎結構變更和更新，如此資料就不會遺失。 但是，當您刪除具有青銅持久性的節點類型時，可能會丟失狀態資訊。 如果要刪除主節點類型，並且應用程式處於無狀態狀態，則"銅牌"是可以接受的。 當您在生產環境中執行具狀態工作負載時，最低設定應該是銀級。 同樣地，針對生產環境案例，主要節點類型應該一律為銀級或金級。

### <a name="more-about-bronze-durability"></a>深入了解銅級持久性

當移除銅級的節點類型時，該節點類型中的所有節點會立即停機。 Service Fabric 不會攔截任何銅級節點擴展集更新，因此所有的 VM 會立即停機。 如果您有具狀態的任何項目在這些節點上，資料會遺失。 現在，即使您是無狀態的，Service Fabric 中的所有節點都會參與通道，因此整個鄰近的資料都會遺失，這可能會造成叢集本身不穩定。

## <a name="remove-a-node-type"></a>移除節點類型

1. 在開始此過程之前，請注意此先決條件。

    - 群集是正常運行的。
    - 刪除節點類型後，仍有足夠的容量，例如。 要放置所需副本計數的節點數。

2. 將所有具有放置約束的服務從節點類型中移出節點類型。

    - 修改應用程式/服務清單以不再引用節點類型。
    - 部署更改。

    然後驗證：
    - 上面修改的所有服務不再在屬於節點類型的節點上運行。
    - 所有服務都是健康的。

3. 取消將節點類型標記為非主節點類型（為非主節點類型跳過）

    - 查找用於部署的 Azure 資源管理器範本。
    - 在"服務結構"部分中查找與節點類型相關的部分。
    - 將主要屬性更改為 false。 • 不要刪除與此任務中的節點類型相關的部分。
    - 部署修改後的 Azure 資源管理器範本。 • 根據群集配置，此步驟可能需要一段時間。
    
    然後驗證：
    - 門戶中的服務結構部分指示群集已準備就緒。
    - 群集是健康的。
    - 不屬於節點類型的節點均未標記為種子節點。

4. 禁用節點類型的資料。

    使用 PowerShell 連接到群集，然後運行以下步驟。
    
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

    - 對於青銅持久性，請等待所有節點進入禁用狀態
    - 對於白銀和黃金的持久性，某些節點將處於禁用狀態，其餘節點將處於禁用狀態。 檢查處于禁用狀態的節點的詳細資訊選項卡，如果它們都停留在確保基礎結構服務分區的仲裁上，則可以安全地繼續。

5. 停止節點類型的資料。

    使用 PowerShell 連接到群集，然後運行以下步驟。
    
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
    
    等待，直到節點類型的所有節點都標記為"向下"。
    
6. 刪除節點類型的資料。

    使用 PowerShell 連接到群集，然後運行以下步驟。
    
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

    等待，直到從群集中刪除所有節點。 節點不應顯示在 SFX 中。

7. 從服務結構部分刪除節點類型。

    - 查找用於部署的 Azure 資源管理器範本。
    - 在"服務結構"部分中查找與節點類型相關的部分。
    - 刪除與節點類型對應的部分。
    - 僅適用于 Silver 和更高持久性群集，更新範本中的群集資源，並將運行狀況策略配置為忽略結構：/系統應用程式運行狀況，只需在下面`applicationDeltaHealthPolicies`給出的群集`properties`資源下添加。 以下策略應忽略現有錯誤，但不允許出現新的運行狀況錯誤。 
 
 
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

    - 部署修改後的 Azure 資源管理器範本。 • 此步驟需要一段時間，通常長達兩個小時。 此升級將更改基礎結構服務的設置，因此需要重新開機節點。 在這種情況下`forceRestart`將被忽略。 
    該參數`upgradeReplicaSetCheckTimeout`指定 Service Fabric 等待分區處於安全狀態（如果不是尚未處於安全狀態）的最大時間。 一旦對節點上的所有分區進行安全檢查，Service Fabric 將繼續升級該節點。
    參數`upgradeTimeout`的值可以減小到 6 小時，但為了最大安全，應使用 12 小時。

    然後驗證：
    - 門戶中的服務結構資源已準備就緒。

8. 刪除對與節點類型相關的資源的所有引用。

    - 查找用於部署的 Azure 資源管理器範本。
    - 從範本中刪除虛擬機器縮放集以及與節點類型相關的其他資源。
    - 部署變更。

    然後：
    - 等待部署完成。

## <a name="next-steps"></a>後續步驟
- 深入了解叢集[持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。
- 深入了解[節點類型和虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。
- 深入了解[調整 Service Fabric 叢集](service-fabric-cluster-scaling.md)。
