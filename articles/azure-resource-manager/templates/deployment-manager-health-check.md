---
title: 健康情況整合推出-Azure Deployment Manager
description: 描述如何使用 Azure 部署管理員在許多區域中部署服務。 它會顯示安全部署實務，以便在推出到所有區域之前確認部署的穩定性。
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: 63879350eba897cfe5a793309e5129323fe8bbde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372369"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>介紹將健康情況整合推出至 Azure Deployment Manager (公開預覽) 

[Azure Deployment Manager](./deployment-manager-overview.md) 可讓您執行 Azure Resource Manager 資源的分段推出。 資源會依區域依序部署至區域。 Azure Deployment Manager 的整合健康情況檢查可以監視推出，並自動停止有問題的推出，讓您可以進行疑難排解並降低影響的規模。 這項功能可以減少更新中的回歸造成的服務無法使用。

## <a name="health-monitoring-providers"></a>健康情況監視提供者

為了讓健康情況整合盡量簡化，Microsoft 持續與一些最頂尖的服務健康情況監視公司合作，為您提供簡單的複製/貼上解決方案，以整合健康情況檢查與您的部署。 如果您還沒有使用健康情況監視，這些是開始使用的絕佳解決方案：

| ![azure 部署管理員健康情況監視提供者 azure 監視器](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![azure 部署管理員健康情況監視提供者 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理員健康情況監視提供者 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理員健康情況監視提供者 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure 監視器，Microsoft 的完整 stack 可檢視性平臺，適用于雲端原生 & 混合式監視和分析。 |Datadog，適用于新式雲端環境的領先監視和分析平臺。 瞭解 [Datadog 如何與 Azure Deployment Manager 整合](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，這是一個私用和公用雲端服務監視解決方案。 瞭解 [Site24x7 如何與 Azure Deployment Manager 整合](https://www.site24x7.com/azure/adm.html)。| Wavefront，適用于多雲端應用程式環境的監視與分析平臺。 瞭解 [Wavefront 如何與 Azure Deployment Manager 整合](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>如何判斷服務健康狀態

[健康情況監視提供者](#health-monitoring-providers) 提供數種機制來監視服務，並在發生任何服務健康狀態問題時發出警示。 [Azure 監視器](../../azure-monitor/overview.md) 是其中一個這類供應專案的範例。 當超過特定臨界值時，可以使用 Azure 監視器來建立警示。 例如，當您將新的更新部署至您的服務時，您的記憶體和 CPU 使用率超過預期層級的尖峰。 收到通知時，您可以採取更正動作。

這些健康狀態提供者通常會提供 REST Api，讓您可以透過程式設計的方式檢查服務監視的狀態。 REST Api 可以使用簡單的狀況良好/狀況不良信號來傳回， (由 HTTP 回應碼) 和/或所接收之信號的詳細資訊來決定。

Azure Deployment Manager 中的新 *healthCheck* 步驟可讓您宣告指出狀況良好服務的 HTTP 程式碼，或針對更複雜的 REST 結果，您甚至可以指定正則運算式（如果相符的話）表示狀況良好的回應。

使用 Azure Deployment Manager 健康情況檢查來取得設定的流程：

1. 透過您選擇的健全狀況服務提供者來建立您的健全狀況監視。
1. 在 Azure Deployment Manager 首度發行中建立一或多個 healthCheck 步驟。 在 healthCheck 步驟中填寫下列資訊：

    1. 健康情況監視的 REST API URI (如健全狀況服務提供者) 所定義。
    1. 驗證資訊。 目前只支援 API 金鑰樣式驗證。 針對 Azure 監視器，驗證類型應設定為– "RolloutIdentity"，因為用於 Azure Deployment Manager 推出的使用者指派受控識別可針對 Azure 監視器進行擴充。
    1. 定義狀況良好回應的[HTTP 狀態碼](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或正則運算式。 請注意，您可以提供正則運算式，這些運算式全都必須符合，才能讓回應被視為狀況良好，或您可以提供運算式，讓回應被視為狀況良好時必須符合。 這兩種方法都受到支援。

    下列 Json 是整合 Azure 監視器與 Azure Deployment Manager 的範例，它會利用 RolloutIdentity 並建立在沒有警示的情況下推出的健康情況檢查。 唯一支援的 Azure 監視器 API： [Alerts –取得全部](/rest/api/monitor/alertsmanagement/alerts/getall)。

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
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
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    下列 Json 是所有其他健康情況監視提供者的範例：

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

若要逐步解說範例，請參閱 [教學課程：在 Azure 中使用健康情況檢查 Deployment Manager](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>健康情況檢查的階段

此時，Azure Deployment Manager 知道如何查詢您的服務健康情況，以及您首度推出中的哪個階段。 不過，Azure Deployment Manager 也可讓您深入設定這些檢查的時間。 HealthCheck 步驟會以三個循序執行，這些階段全都具有可設定的持續時間：

1. 等候

    1. 部署作業完成之後，Vm 可能會重新開機、根據新資料重新設定，甚至是第一次啟動的 Vm。 服務也需要一些時間，才會開始發出健康情況信號，並由健康情況監視提供者匯總到有用的部分。 在此動盪過程中，檢查服務健康狀態可能並不合理，因為更新尚未達到穩定狀態。 的確，在資源結算的情況下，服務可能會在狀況良好和狀況不良狀態之間不穩定。
    1. 在等候階段，不會監視服務健康狀態。 這是用來允許部署的資源在開始健康情況檢查程式之前製作時間。
1. Elastic

    1. 由於在所有情況下，資源可能需要多少時間才會變得穩定，因此彈性階段可在資源可能不穩定，以及必須維持正常穩定狀態的情況下，提供有彈性的時間間隔。
    1. 當彈性階段開始時，Azure Deployment Manager 會定期輪詢提供的 REST 端點以進行服務健康狀態。 輪詢間隔是可設定的。
    1. 如果健全狀況監視器回傳了表示服務狀況不良的信號，則會忽略這些信號，彈性階段會繼續，而輪詢會繼續進行。
    1. 一旦健康狀態監視器回傳，表示服務狀況良好的信號，彈性階段就會結束，且 HealthyState 階段會開始。
    1. 因此，針對彈性階段所指定的持續時間，就是在將狀況良好的回應視為強制之前，可花在輪詢服務健康狀態的最大時間量。
1. HealthyState

    1. 在 HealthyState 階段期間，服務健康狀態會持續以與彈性階段相同的間隔輪詢。
    1. 服務預期會針對整個指定的持續時間，維護健康情況監視提供者的狀況良好信號。
    1. 如果在任何時間點偵測到狀況不良的回應，Azure Deployment Manager 將會停止整個推出，並傳回帶有狀況不良服務信號的 REST 回應。
    1. HealthyState 持續期間結束後，healthCheck 就會完成，且部署會繼續進行下一個步驟。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Deployment Manager 中整合健康情況監視。 請前往下一篇文章，以了解如何使用部署管理員進行部署。

> [!div class="nextstepaction"]
> [教學課程：在 Azure Deployment Manager 中整合健康情況檢查](./deployment-manager-tutorial-health-check.md)