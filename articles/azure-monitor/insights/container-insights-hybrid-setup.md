---
title: 使用 Azure 監視器配置用於容器的混合庫伯奈斯群集 |微軟文檔
description: 本文介紹如何為容器配置 Azure 監視器，以監視託管在 Azure 堆疊或其他環境中的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198049"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>使用 Azure 監視器為容器配置混合庫伯奈斯群集

容器的 Azure 監視器為 Azure 上的 Azure 庫伯奈服務 （AKS） 和[AKS 引擎](https://github.com/Azure/aks-engine)提供了豐富的監視體驗，這是託管在 Azure 上的自管理的 Kubernetes 群集。 本文介紹如何啟用對 Azure 外部託管的 Kubernetes 群集的監視，並實現類似的監視體驗。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您有下列項目：

* Log Analytics 工作區。

    容器的 Azure 監視器支援 Azure[產品中按區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)列出的區域中的日誌分析工作區。 要創建自己的工作區，可以通過[Azure 資源管理器](../platform/template-workspace-configuration.md)[、PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)或在[Azure 門戶](../learn/quick-create-workspace.md)中創建它。

    >[!NOTE]
    >不支援將具有相同群集名稱的多個群集監視到同一日誌分析工作區。 群集名稱必須是唯一的。
    >

* 您是**日誌分析參與者角色**的成員，以啟用容器監視。 有關如何控制對日誌分析工作區的訪問的詳細資訊，請參閱[管理對工作區和日誌資料的訪問](../platform/manage-access.md)

* [HELM 用戶端](https://helm.sh/docs/using_helm/)將裝載 Azure 監視器，用於指定 Kubernetes 群集的容器圖表。

* Linux 的日誌分析代理的容器化版本需要以下代理和防火牆配置資訊才能與 Azure 監視器通信：

    |代理程式資源|連接埠 |
    |------|---------|   
    |*.ods.opinsights.azure.com |連接埠 443 |  
    |*.oms.opinsights.azure.com |連接埠 443 |  
    |*.blob.core.windows.net |連接埠 443 |  
    |*.dc.services.visualstudio.com |連接埠 443 |

* 容器化代理要求 Kubelet 的`cAdvisor secure port: 10250``unsecure port :10255`或要在群集中的所有節點上打開，以收集性能指標。 如果 Kubelet 的 cAdvisor 尚未配置，我們建議您在 Kubelet 的 cAdvisor 上進行配置`secure port: 10250`。

* 容器化代理要求在容器上指定以下環境變數，以便與群集中的 Kubernetes API 服務進行通信，以收集清單資料 -`KUBERNETES_SERVICE_HOST`和`KUBERNETES_PORT_443_TCP_PORT`。

>[!IMPORTANT]
>用於監視混合 Kubernetes 群集的最小代理版本是 ciprod10182019 或更高版本。

## <a name="supported-configurations"></a>支援的設定

容器的 Azure 監視器正式支援以下操作。

- 環境：本地庫伯奈斯、Azure 和 Azure 堆疊上的 AKS 引擎。 有關詳細資訊，請參閱[Azure 堆疊上的 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- 庫伯內斯的版本和支援策略與[支援的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。
- 容器運行時：碼頭和莫比
- 主節點和工作節點的 Linux 作業系統版本：Ubuntu （18.04 LTS 和 16.04 LTS）
- 支援存取控制：庫伯奈斯 RBAC 和非 RBAC

## <a name="enable-monitoring"></a>啟用監視

為混合 Kubernetes 群集的容器啟用 Azure 監視器包括按循序執行以下步驟。

1. 使用容器見解解決方案配置日誌分析工作區。

2. 使用日誌分析工作區為容器 HELM 圖表啟用 Azure 監視器。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>如何添加 Azure 監視器容器解決方案

可以使用 Azure PowerShell Cmdlet`New-AzResourceGroupDeployment`或使用 Azure CLI 使用提供的 Azure 資源管理器範本部署解決方案。

若您不熟悉使用範本來部署資源的概念，請參閱：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須運行 Azure CLI 版本 2.0.59 或更高版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

- **工作區 ResourceId** - 日誌分析工作區的完整資源識別碼。
- **工作區區域**- 工作區在其中創建的區域，在從 Azure 門戶查看時，該區域也稱為工作區屬性中**的位置**。

要首先標識`workspaceResourceId`**容器SolutionParams.json**檔中參數值所需的日誌分析工作區的完整資源識別碼，請執行以下步驟，然後運行 PowerShell Cmdlet 或 Azure CLI 命令以添加解決方案。

1. 列出您有權訪問的所有訂閱，使用以下命令：

    ```azurecli
    az account list --all -o table
    ```

    輸出看起來會像下面這樣：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    複製**訂閱 Id**的值。

2. 使用以下命令切換到託管日誌分析工作區的訂閱：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 下面的示例以預設 JSON 格式顯示訂閱中的工作區清單。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在輸出中，查找工作區名稱，然後在欄位 ID 下複製該日誌分析工作區的完整資源**ID。**

4. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
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

5. 將此檔另存為容器Solution.json 到本地資料夾。

6. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 使用步驟 3 中複製的值編輯**工作區 ResourceId**的值，並且**工作區區域**在運行 Azure CLI 命令 az[監視器日誌分析工作區顯示](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)後複製**區域**值。

8. 將此檔另存為容器SolutionParams.json 到本地資料夾。

9. 您已準備好部署此範本。

   * 要使用 Azure PowerShell 進行部署，請使用包含範本的資料夾中的以下命令：

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```powershell
       provisioningState       : Succeeded
       ```

   * 要使用 Azure CLI 進行部署，運行以下命令：

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```azurecli
       provisioningState       : Succeeded
       ```

       啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="install-the-chart"></a>安裝圖表

要啟用 HELM 圖表，可以執行以下操作：

1. 通過運行以下命令將 Azure 圖表存儲庫添加到本地清單：

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. 通過運行以下命令安裝圖表：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    如果日誌分析工作區位於 Azure 中國中，請運行以下命令：

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    如果日誌分析工作區位於 Azure 美國政府中，請運行以下命令：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>配置代理資料收集

使用圖表版本 1.0.0，代理資料收集設置從 ConfigMap 進行控制。 請參閱[此處](container-insights-agent-config.md)有關代理資料收集設置的文檔。

成功部署圖表後，可以在 Azure 監視器中查看 Azure 監視器中混合 Kubernetes 群集的資料，查看 Azure 門戶中的容器。  

>[!NOTE]
>從代理到在 Azure 日誌分析工作區中提交，引入延遲大約需要 5 到 10 分鐘。 群集的狀態顯示值 **"無資料**"或 **"未知"，** 直到 Azure 監視器中所有必需的監視資料都可用。

## <a name="troubleshooting"></a>疑難排解

如果在嘗試為混合 Kubernetes 群集啟用監視時遇到錯誤，請複製 PowerShell 腳本[TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1)並將其保存到電腦上的資料夾中。 此腳本用於説明檢測和修復遇到的問題。 它旨在檢測和嘗試更正的問題如下：

* 指定的日誌分析工作區有效
* 日誌分析工作區配置了容器的 Azure 監視器解決方案。 如果沒有，請配置工作區。
* OmsAgent 複本集窗格正在運行
* OmsAgent 守護進程子窗格正在運行
* OmsAgent 運行狀況服務正在運行
* 在容器化代理上配置的日誌分析工作區 ID 和金鑰與 Insight 配置的工作區匹配。
* 驗證所有 Linux 工作節點`kubernetes.io/role=agent`都有用於計畫 rs pod 的標籤。 如果不存在，請添加它。
* 驗證`cAdvisor secure port:10250`或`unsecure port: 10255`打開群集中的所有節點。

要使用 Azure PowerShell 執行，請使用包含腳本的資料夾中的以下命令：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>後續步驟

啟用監視以收集混合 Kubernetes 群集的運行狀況和資源利用率以及運行在它們的工作負載後，[瞭解如何將](container-insights-analyze.md)Azure 監視器用於容器。
