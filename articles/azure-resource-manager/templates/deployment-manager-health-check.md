---
title: 健全狀況整合首度發行-Azure Deployment Manager
description: 描述如何使用 Azure 部署管理員在許多區域中部署服務。 它會顯示安全部署實務，以便在推出到所有區域之前確認部署的穩定性。
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273796"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>引進 Azure Deployment Manager 的健全狀況整合推出（公開預覽）

[Azure Deployment Manager](./deployment-manager-overview.md)可讓您執行 Azure Resource Manager 資源的階段式部署。 資源是依區域以排序方式部署區域。 Azure Deployment Manager 的整合健康情況檢查可以監視首度發行，並自動停止問題的首度發行，讓您可以疑難排解並降低影響的規模。 這項功能可以減少因更新中的衰退而造成的服務無法使用。

## <a name="health-monitoring-providers"></a>健全狀況監視提供者

為了讓健康情況整合盡量簡化，Microsoft 持續與一些最頂尖的服務健康情況監視公司合作，為您提供簡單的複製/貼上解決方案，以整合健康情況檢查與您的部署。 如果您還不是使用健康狀態監視器，這些都是很好的解決方案，可供您開始使用：

| ![azure 部署管理員健全狀況監視提供者 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理員健全狀況監視提供者 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理員健全狀況監視提供者 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog，這是現代化雲端環境的領先監視和分析平臺。 瞭解[Datadog 如何與 Azure Deployment Manager 整合](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，這是一項全功能的私用和公用雲端服務監視解決方案。 瞭解[Site24x7 如何與 Azure Deployment Manager 整合](https://www.site24x7.com/azure/adm.html)。| Wavefront，這是適用于多雲端應用程式環境的監視和分析平臺。 瞭解[Wavefront 如何與 Azure Deployment Manager 整合](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>服務健全狀況的判斷方式

[健全狀況監視提供者](#health-monitoring-providers)會提供數種監視服務的機制，並警示您任何服務健康狀態問題。 [Azure 監視器](../../azure-monitor/overview.md)是其中一個這類供應專案的範例。 Azure 監視器可用來在超出特定閾值時建立警示。 例如，當您將新的更新部署至您的服務時，您的記憶體和 CPU 使用率會尖峰超過預期的層級。 當收到通知時，您可以採取更正動作。

這些健康狀態提供者通常會提供 REST Api，以便以程式設計方式檢查服務監視的狀態。 REST Api 可以使用簡單的狀況良好/狀況不良信號（由 HTTP 回應碼判斷），以及/或透過其接收信號的詳細資訊來傳回。

Azure Deployment Manager 中的新*healthCheck*步驟可讓您宣告指出狀況良好服務的 HTTP 程式碼，或者，對於更複雜的 REST 結果，您甚至可以指定正則運算式，如果兩者相符，則表示狀況良好的回應。

使用 Azure Deployment Manager 健康狀態檢查設定的流程：

1. 透過您選擇的健全狀況服務提供者，建立您的健全狀況監視。
1. 建立一或多個 healthCheck 步驟，做為 Azure Deployment Manager 首度發行的一部分。 使用下列資訊填寫 healthCheck 步驟：

    1. 健全狀況監視器之 REST API 的 URI （如您的健全狀況服務提供者所定義）。
    1. 驗證資訊。 目前僅支援 API 金鑰樣式驗證。
    1. 定義狀況良好回應的[HTTP 狀態碼](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或正則運算式。 請注意，您可以提供正則運算式，這全都必須符合，才能將回應視為狀況良好，或者您可能會提供運算式，讓回應視為狀況良好。 這兩種方法都受到支援。

    下列 Json 是一個範例：

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. 在您的 Azure Deployment Manager 首度發行中，于適當的時間叫用 healthCheck 步驟。 在下列範例中，會在**stepGroup2**的**postDeploymentSteps**中叫用健康情況檢查步驟。

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

若要逐步解說範例，請參閱[教學課程：在 Azure 中使用健康情況檢查 Deployment Manager](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>健康情況檢查的階段

此時，Azure Deployment Manager 知道如何查詢服務的健全狀況，以及您在首度發行中的哪些階段執行此動作。 不過，Azure Deployment Manager 也可讓您深入設定這些檢查的時間。 HealthCheck 步驟會以3個連續階段執行，它們都有可設定的持續時間： 

1. 等候

    1. 完成部署作業之後，Vm 可能會重新開機、根據新的資料進行重新設定，或甚至是第一次啟動。 服務也需要一些時間，才會開始發出健全狀況信號，讓健全狀況監視提供者將其匯總到有用的專案。 在此」動盪過程中，檢查服務健全狀況可能不合理，因為更新尚未達到穩定狀態。 事實上，服務在狀況良好和狀況不良狀態時，可能會因為資源已結算而不穩定。 
    1. 在等待階段期間，不會監視服務健康狀態。 這是用來允許已部署的資源在開始健康情況檢查程式之前製作時間。 
1. Elastic

    1. 由於在所有情況下，資源在製作穩定之前需要多久的時間才會變長，因此彈性階段可讓您在資源可能不穩定以及需要維持健康狀態良好的情況之間有彈性的時段狀態.
    1. 當彈性階段開始時，Azure Deployment Manager 會開始針對服務健全狀況定期輪詢提供的 REST 端點。 可設定輪詢間隔。 
    1. 如果健全狀況監視傳回，表示服務狀況不良，則會忽略這些信號，彈性階段會繼續，且會繼續輪詢。 
    1. 一旦健全狀況監視傳回指出服務狀況良好的信號，彈性階段就會結束，而 HealthyState 階段就會開始。 
    1. 因此，針對彈性階段指定的持續時間，是在狀況良好的回應視為強制性之前，可花在輪詢服務健康狀態的最大時間量。 
1. HealthyState

    1. 在 HealthyState 階段期間，服務健康狀態會持續以與彈性階段相同的間隔進行輪詢。 
    1. 服務預期會在整個指定的期間內，從健康狀態監視提供者維持狀況良好的信號。 
    1. 如果偵測到任何時間點狀況不良的回應，Azure Deployment Manager 將會停止整個首度發行，並傳回附有狀況不良服務信號的 REST 回應。
    1. HealthyState 持續期間結束後，healthCheck 就會完成，且部署會繼續進行下一個步驟。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Deployment Manager 中整合健康情況監視。 請前往下一篇文章，以了解如何使用部署管理員進行部署。

> [!div class="nextstepaction"]
> [教學課程：在 Azure Deployment Manager 中整合健康情況檢查](./deployment-manager-tutorial-health-check.md)