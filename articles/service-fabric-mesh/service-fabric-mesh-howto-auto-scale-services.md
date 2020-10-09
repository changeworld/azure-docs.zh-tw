---
title: 自動調整在 Azure Service Fabric 網狀中執行的應用程式
description: 深入了解如何設定 Service Fabric Mesh 應用程式服務的自動調整原則。
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: f65fcfa76069a3de37fd3a76e38e38fba40e04ac
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843051"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>建立 Service Fabric Mesh 應用程式的自動調整原則
將應用程式部署至 Service Fabric 網格的主要優點之一，就是您可以輕鬆地相應縮小或放大您的服務。這應該用來處理服務上的不同負載量，或提升可用性。 您可以手動相應縮小或相應放大您的服務，或設定自動調整原則。

[自動調整](service-fabric-mesh-scalability.md#autoscaling-service-instances)可讓您以動態方式調整服務執行個體的數目 (水平調整)。 自動調整提供絕佳的彈性，並且可以根據 CPU 或記憶體使用率來佈建或移除服務執行個體。

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>建立自動調整原則、觸發程序與機制的選項
自動調整原則是針對您想要調整的每個服務進行定義。 原則會定義於 YAML 服務資源檔或 JSON 部署範本。 每個調整原則是由兩個部分所組成：觸發程序和調整機制。

觸發程序會定義何時叫用自動調整原則。  指定觸發程序類型 (平均負載) 和要監視的計量 (CPU 或記憶體)。  指定以百分比表示的負載臨界值上限和下限。 調整間隔會定義所有目前已部署服務執行個體之間指定使用率 (例如 CPU 平均負載) 的檢查頻率 (以秒為單位)。  當監視計量低於臨界值下限或高於臨界值上限，就會觸發機制。  

調整機制會定義當觸發原則時，如何執行調整作業。  指定機制類型 (新增/移除複本)、複本計數下限或上限 (整數)。  服務複本數永遠不能調整為低於計數下限或高於計數上限。  同時也將調整增量指定為整數，這是在調整作業中新增或移除的複本數目。  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>在 JSON 範本中定義自動調整原則

下列範例會顯示 JSON 部署範本中的自動調整原則。  自動調整原則會在要調整的服務屬性中宣告。  在此範例中，會定義 CPU 平均負載觸發程序。  如果所有已部署執行個體的平均 CPU 負載低於 0.2 (20%) 或高於 0.8 (80%)，就會觸發機制。  CPU 負載每隔 60 秒檢查一次。  調整機制定義為在觸發原則時新增或移除執行個體。  服務執行個體會以 1 的增量新增或移除。  同時也會定義執行個體計數下限 1，以及執行個體計數上限 40。

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>在 service.yaml 資源檔中定義自動調整原則
下列範例會顯示服務資源 (YAML) 檔中的自動調整原則。  自動調整原則會宣告為要調整的服務屬性。  在此範例中，會定義 CPU 平均負載觸發程序。  如果所有已部署執行個體的平均 CPU 負載低於 0.2 (20%) 或高於 0.8 (80%)，就會觸發機制。  CPU 負載每隔 60 秒檢查一次。  調整機制定義為在觸發原則時新增或移除執行個體。  服務執行個體會以 1 的增量新增或移除。  同時也會定義執行個體計數下限 1，以及執行個體計數上限 40。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>後續步驟
深入了解如何[手動調整服務](service-fabric-mesh-tutorial-template-scale-services.md)
