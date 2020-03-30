---
title: 運行狀況集成推出 - Azure 部署管理員
description: 描述如何使用 Azure 部署管理員在許多區域中部署服務。 它會顯示安全部署實務，以便在推出到所有區域之前確認部署的穩定性。
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273796"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>向 Azure 部署管理員引入運行狀況集成推出（公共預覽版）

[Azure 部署管理員](./deployment-manager-overview.md)允許您執行 Azure 資源管理器資源的分階段推出。 資源按區域有序部署。 Azure 部署管理員 的集成運行狀況檢查可以監視推出，並自動停止有問題的推出，以便您可以疑難排解並減少影響的規模。 此功能可以減少更新中回歸導致的服務不可用。

## <a name="health-monitoring-providers"></a>健康監測提供者

為了讓健康情況整合盡量簡化，Microsoft 持續與一些最頂尖的服務健康情況監視公司合作，為您提供簡單的複製/貼上解決方案，以整合健康情況檢查與您的部署。 如果您尚未使用運行狀況監視器，則以下是從以下方面開始的絕佳解決方案：

| ![azure 部署管理員運行狀況監視器提供程式資料狗](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理員運行狀況監視器提供程式網站 24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理員運行狀況監視器提供程式波前](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog，現代雲環境的領先監控和分析平臺。 瞭解[Datadog 如何與 Azure 部署管理員集成](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，一體式私有和公共雲服務監控解決方案。 瞭解[Site24x7 如何與 Azure 部署管理員集成](https://www.site24x7.com/azure/adm.html)。| 波前，多雲應用環境的監控和分析平臺。 瞭解[Wavefront 如何與 Azure 部署管理員集成](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>如何確定服務運行狀況

[運行狀況監控提供程式](#health-monitoring-providers)提供了多種機制來監控服務和提醒您任何服務運行狀況問題。 [Azure 監視器](../../azure-monitor/overview.md)是此類產品/服務的示例。 Azure 監視器可用於在超過某些閾值時創建警報。 例如，在將新的更新部署到服務時，記憶體和 CPU 利用率的峰值超出預期級別。 通知後，您可以採取糾正措施。

這些運行狀況提供程式通常提供 REST API，以便可以以程式設計方式檢查服務監視器的狀態。 REST API 可以返回一個簡單的健康/不正常信號（由 HTTP 回應代碼確定）和/或有關其接收信號的詳細資訊。

Azure 部署管理員中的新*運行狀況檢查*步驟允許您聲明指示正常服務的 HTTP 代碼，或者，對於更複雜的 REST 結果，甚至可以指定正則運算式，如果它們匹配，則指示正常回應。

使用 Azure 部署管理員運行狀況檢查獲取安裝程式的流：

1. 通過您選擇的健康服務提供者創建您的健康監視器。
1. 創建一個或多個運行狀況檢查步驟，作為 Azure 部署管理員部署部署部署部署的一部分。 填寫運行狀況檢查步驟，並提供以下資訊：

    1. 用於運行狀況監視器的 REST API 的 URI（由運行狀況服務提供者定義）。
    1. 驗證資訊。 目前僅支援 API 金鑰樣式身份驗證。
    1. 定義正常回應的[HTTP 狀態碼](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或正則運算式。 請注意，您可以提供正則運算式，ALL 必須匹配該運算式才能認為回應為健康，或者您可以提供 ANY 必須匹配的運算式，以便回應被視為正常。 支援這兩種方法。

    以下 Json 就是一個示例：

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

1. 在 Azure 部署管理員 推出中的適當時間調用運行狀況檢查步驟。 在下面的示例中，在**步驟Group2**的部署**後步驟**中調用了運行狀況檢查步驟。

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

要演練一個示例，請參閱[教程：在 Azure 部署管理員 中使用運行狀況檢查](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>運行狀況檢查階段

此時，Azure 部署管理員知道如何查詢服務的運行狀況，以及部署中的哪些階段可以執行此操作。 但是，Azure 部署管理員還允許對這些檢查的時間進行深度配置。 運行狀況檢查步驟在 3 個順序階段執行，所有這些階段都有可配置的持續時間： 

1. 等候

    1. 部署操作完成後，VM 可能會重新開機、根據新資料重新配置，甚至初次開機。 服務還需要一段時間才能開始發出運行狀況信號，以便運行狀況監測提供者將其聚合為有用內容。 在此動盪過程中，檢查服務運行狀況可能沒有意義，因為更新尚未達到穩定狀態。 事實上，隨著資源的解決，該服務可能在健康狀態和不健康狀態之間搖擺不定。 
    1. 在"等待"階段，不會監視服務運行狀況。 這用於允許已部署的資源在開始運行狀況檢查過程之前進行烘焙。 
1. Elastic

    1. 由於不可能在所有情況下知道資源在穩定之前烘烤需要多長時間，因此彈性階段允許在資源可能不穩定和需要資源保持健康穩定之間留出一段靈活的時間段狀態。
    1. 彈性階段開始時，Azure 部署管理員開始定期輪詢提供的 REST 終結點以進行服務運行狀況。 輪詢間隔是可配置的。 
    1. 如果運行狀況監視器返回時帶有指示服務不正常的信號，則忽略這些信號，彈性階段繼續，輪詢將繼續。 
    1. 一旦運行狀況監視器返回，並顯示服務正常，彈性階段結束，健康狀態階段開始。 
    1. 因此，為彈性階段指定的持續時間是在考慮正常回應之前，可以用於輪詢服務運行狀況的最大時間量。 
1. HealthyState

    1. 在 HealthState 階段，服務運行狀況會以與彈性相相同的間隔持續輪詢。 
    1. 該服務應在整個指定持續時間內保持來自運行狀況監控提供程式的健康信號。 
    1. 如果在任何時候檢測到不正常的回應，Azure 部署管理員將停止整個部署，並返回攜帶不正常服務信號的 REST 回應。
    1. 運行狀況持續時間結束後，運行狀況檢查將完成，並且部署將繼續執行下一步。

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了如何在 Azure 部署管理員中集成運行狀況監視。 請前往下一篇文章，以了解如何使用部署管理員進行部署。

> [!div class="nextstepaction"]
> [教程：在 Azure 部署管理員中集成運行狀況檢查](./deployment-manager-tutorial-health-check.md)