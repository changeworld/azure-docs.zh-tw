---
title: 在 Azure Stack Edge Pro GPU 裝置上啟用 Azure 監視器
description: 說明如何啟用 Azure Stack Edge Pro GPU 裝置上的 Azure 監視器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 3485c6ca5c2672fa48b6118a78600b9745994ce1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466181"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 裝置上啟用 Azure 監視器

當您執行多個計算應用程式時，在 Azure Stack Edge Pro GPU 裝置上監視容器是很重要的。 Azure 監視器可讓您從在裝置上執行的 Kubernetes 叢集收集容器記錄和記憶體和處理器計量。

本文說明在您的裝置上啟用 Azure 監視器以及收集 Log Analytics 工作區中容器記錄所需的步驟。 Azure Stack Edge Pro GPU 裝置目前不支援 Azure 監視器計量存放區。 


## <a name="prerequisites"></a>必要條件

開始之前，您需要：

- Azure Stack Edge Pro 裝置。 根據 [教學課程：啟用您的裝置](azure-stack-edge-gpu-deploy-activate.md)中的步驟，確定已啟用裝置。
- 您已根據教學課程完成 **設定計算** 步驟：在裝置上的 [Azure Stack Edge Pro 裝置上設定計算](azure-stack-edge-gpu-deploy-configure-compute.md) 。 您的裝置應該有相關聯的 IoT 中樞資源、IoT 裝置和 IoT Edge 裝置。


## <a name="create-log-analytics-workspace"></a>建立 Log Analytics 工作區。

請採取下列步驟來建立 log analytics 工作區。 Log analytics 工作區是用來收集和儲存記錄資料的邏輯儲存體單位。

1. 在 [Azure 入口網站中，選取 [ **+ 建立資源** 並搜尋 **Log Analytics 工作區** ]，然後選取 [ **建立**]。 
1. 在 [ **建立 Log Analytics] 工作區** 中，設定下列設定。 接受其餘的預設值。

    1. 在 [ **基本** ] 索引標籤上，提供工作區的訂用帳戶、資源群組、名稱和區域。 

        ![Log Analytics 工作區的 [基本] 索引標籤](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. 在 [ **定價層** ] 索引標籤上，接受預設的 **隨用隨付方案**。

        ![Log Analytics 工作區的定價索引標籤](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. 在 [ **審核 + 建立** ] 索引標籤上，檢查工作區的資訊，然後選取 [ **建立**]。

        ![審核 + 建立 Log Analytics 工作區](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

如需詳細資訊，請參閱透過 [Azure 入口網站建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)中的詳細步驟。



## <a name="enable-container-insights"></a>啟用容器深入解析

採取下列步驟，在您的工作區上啟用容器深入解析。 

1. 遵循 [如何新增 Azure 監視器容器解決方案](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)中的詳細步驟。 使用下列範本檔案 `containerSolution.json` ：

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. 取得資源識別碼和位置。 移至 `Your Log Analytics workspace > General > Properties`。 複製下列資訊：

    - **資源識別碼** ，也就是 Azure Log Analytics 工作區的完整 AZURE 資源識別碼。 
    - Azure 區域的 **位置**。

    ![Log Analytics 工作區的屬性](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. 使用下列參數檔 `containerSolutionParams.json` 。 `workspaceResourceId`以資源識別碼取代，並 `workspaceRegion` 以先前步驟中複製的位置取代。

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    以下是已啟用容器深入解析之 Log Analytics 工作區的輸出範例：
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>在您的裝置上設定 Azure 監視器

1. 移至新建立的 Log Analytics 資源，然後複製 **工作區識別碼** 和 **主要金鑰** (工作區金鑰) 。

    ![Log Analytics 工作區中的代理程式管理](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    請儲存此資訊，因為您將在稍後的步驟中使用它。

1. [連接到裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 
    
1. 使用 log analytics 工作區識別碼和工作區金鑰搭配下列 Cmdlet：

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. 啟用 Azure 監視器之後，您應該會在 Log Analytics 工作區中看到記錄。 若要查看部署在裝置上的 Kubernetes 叢集狀態，請移至 **Azure 監視器 > 深入解析 > 容器**。 針對 [環境] 選項，選取 [ **全部**]。 

    ![Log Analytics 工作區中的計量](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何透過 [Kubernetes 儀表板監視 Kubernetes 工作負載](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。
