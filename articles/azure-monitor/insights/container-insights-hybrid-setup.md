---
title: 使用適用于容器的 Azure 監視器來設定混合式 Kubernetes 叢集 |Microsoft Docs
description: 本文說明如何設定容器的 Azure 監視器，以監視 Azure Stack 或其他環境上裝載的 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2ca977f572ee9f60c1ca72fc472f3a6ee6c6362
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498894"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>使用適用于容器的 Azure 監視器來設定混合式 Kubernetes 叢集

適用于容器的 Azure 監視器會針對 Azure 上的 Azure Kubernetes Service （AKS）和[AKS 引擎](https://github.com/Azure/aks-engine)提供豐富的監視體驗，這是裝載于 azure 上的自我管理 Kubernetes 叢集。 本文說明如何啟用在 Azure 外部裝載的 Kubernetes 叢集監視，並達成類似的監視體驗。

## <a name="supported-configurations"></a>支援的設定

下列設定已正式支援容器的 Azure 監視器。

- 環境

    - Kubernetes 內部部署
    
    - Azure 上的 AKS 引擎和 Azure Stack。 如需詳細資訊，請參閱[AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
    
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html)第4版和更高版本、內部部署或其他雲端環境。

- Kubernetes 和支援原則的版本與[支援的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。

- 支援下列容器執行時間： Docker、Moby 和 CRI 相容的執行時間，例如 CRI-O 和 ContainerD。

- 支援的主要和背景工作節點的 Linux OS 版本為： Ubuntu （18.04 LTS 和 16.04 LTS），Red Hat Enterprise Linux CoreOS 43.81。

- 支援的存取控制： Kubernetes RBAC 和非 RBAC

## <a name="prerequisites"></a>先決條件

開始之前，請確定您有下列項目：

- [Log Analytics 工作區](../platform/design-logs-deployment.md)。

    容器的 Azure 監視器支援[依區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)在 Azure 產品中列出的區域中的 Log Analytics 工作區。 若要建立您自己的工作區，可以透過[Azure Resource Manager](../platform/template-workspace-configuration.md)、透過[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)或在[Azure 入口網站](../learn/quick-create-workspace.md)中建立。

    >[!NOTE]
    >不支援對相同的 Log Analytics 工作區啟用多個具有相同叢集名稱的叢集監視。 叢集名稱必須是唯一的。
    >

- 您是**Log Analytics 參與者角色**的成員，可啟用容器監視。 如需有關如何控制 Log Analytics 工作區存取權的詳細資訊，請參閱[管理工作區和記錄資料的存取權](../platform/manage-access.md)。

- 若要查看監視資料，您必須在 Log Analytics 工作區中具有[*Log analytics 讀者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色，並使用容器的 Azure 監視器進行設定。

- [HELM 用戶端](https://helm.sh/docs/using_helm/)，以針對指定的 Kubernetes 叢集，將容器的 Azure 監視器上架圖表。

- 適用于 Linux 的 Log Analytics 代理程式容器化版本需要下列 proxy 和防火牆設定資訊，才能與 Azure 監視器進行通訊：

    |代理程式資源|連接埠 |
    |------|---------|
    |*.ods.opinsights.azure.com |連接埠 443 |
    |*.oms.opinsights.azure.com |連接埠 443 |
    |*. dc.services.visualstudio.com |連接埠 443 |

- 容器化代理程式需要在 `cAdvisor secure port: 10250` 叢 `unsecure port :10255` 集中的所有節點上開啟 Kubelet 的或，才能收集效能計量。 我們建議您 `secure port: 10250` 在 Kubelet 的 cAdvisor 上進行設定（如果尚未設定）。

- 容器化代理程式需要在容器上指定下列環境變數，才能與叢集中的 Kubernetes API 服務進行通訊，以收集清查資料- `KUBERNETES_SERVICE_HOST` 和 `KUBERNETES_PORT_443_TCP_PORT` 。

>[!IMPORTANT]
>監視混合式 Kubernetes 叢集所支援的最低代理程式版本是 ciprod10182019 或更新版本。

## <a name="enable-monitoring"></a>啟用監視

針對混合式 Kubernetes 叢集的容器啟用 Azure 監視器包含依序執行下列步驟。

1. 使用 Container Insights 解決方案來設定您的 Log Analytics 工作區。

2. 使用 Log Analytics 工作區啟用容器的 Azure 監視器 HELM 圖表。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>如何新增 Azure 監視器容器解決方案

您可以使用 Azure PowerShell Cmdlet 或 Azure CLI，透過提供的 Azure Resource Manager 範本來部署解決方案 `New-AzResourceGroupDeployment` 。

若您不熟悉使用範本來部署資源的概念，請參閱：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 版2.0.59 或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

- **workspaceResourceId** -Log Analytics 工作區的完整資源識別碼。
- **workspaceRegion** -工作區建立所在的區域，在從 Azure 入口網站進行觀看時，也稱為工作區屬性中的**位置**。

若要先識別containerSolutionParams.json 檔案中參數值所需的 Log Analytics 工作區的完整資源識別碼 `workspaceResourceId` ，請執行下列步驟，然後執行 PowerShell Cmdlet 或 Azure CLI 命令來新增解決方案。 **containerSolutionParams.json**

1. 使用下列命令，列出您有權存取的所有訂用帳戶：

    ```azurecli
    az account list --all -o table
    ```

    輸出看起來會像下面這樣：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    複製**SubscriptionId**的值。

2. 使用下列命令，切換至裝載 Log Analytics 工作區的訂用帳戶：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 下列範例會以預設的 JSON 格式顯示訂用帳戶中的工作區清單。

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在輸出中，尋找工作區名稱，然後將該 Log Analytics 工作區的完整資源識別碼複製到欄位**識別碼**之下。

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

5. 將此檔案儲存為本機資料夾上的 containerSolution.js。

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

7. 使用您在步驟3中複製的值來編輯**workspaceResourceId**的值，並在執行 Azure CLI 命令[az monitor log analytics workspace show](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)之後，針對**workspaceRegion**複製**區域**值。

8. 將此檔案儲存為本機資料夾上的 containerSolutionParams.js。

9. 您已準備好部署此範本。

   - 若要使用 Azure PowerShell 進行部署，請在包含範本的資料夾中使用下列命令：

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```powershell
       provisioningState       : Succeeded
       ```

   - 若要使用 Azure CLI 進行部署，請執行下列命令：

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```azurecli
       provisioningState       : Succeeded
       ```

       啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="install-the-helm-chart"></a>安裝 HELM 圖表

在本節中，您會針對容器的 Azure 監視器安裝容器化代理程式。 在繼續之前，您必須識別參數所需的工作區識別碼 `omsagent.secret.wsid` ，以及參數所需的主鍵 `omsagent.secret.key` 。 您可以執行下列步驟來識別這項資訊，然後執行命令以使用 HELM 圖表來安裝代理程式。

1. 執行下列命令來識別工作區識別碼：

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    在輸出中，尋找功能變數名稱底下的工作區名稱 **，然後**在 [ **customerID**] 欄位下複製該 Log Analytics 工作區的工作區識別碼。

2. 執行下列命令來識別工作區的主要金鑰：

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    在輸出中，尋找 [ **primarySharedKey**] 欄位下的 [主要金鑰]，然後複製值。

>[!NOTE]
>下列命令僅適用于 Helm 第2版。 使用參數不適用於 `--name` Helm 第3版。 

>[!NOTE]
>如果您的 Kubernetes 叢集會透過 proxy 伺服器進行通訊，請 `omsagent.proxy` 使用 proxy 伺服器的 URL 來設定參數。 如果叢集未透過 proxy 伺服器進行通訊，則您不需要指定此參數。 如需詳細資訊，請參閱本文稍後的[設定 proxy 端點](#configure-proxy-endpoint)。

3. 執行下列命令，將 Azure 圖表存放庫新增至您的本機清單：

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

4. 執行下列命令來安裝圖表：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    如果 Log Analytics 工作區位於 Azure 中國世紀，請執行下列命令：

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    如果 Log Analytics 工作區位於 Azure 美國政府，請執行下列命令：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>使用 API 模型啟用 Helm 圖表

您可以在 AKS 引擎叢集規格 json 檔案（也稱為 API 模型）中指定附加元件。 在此附加元件中，提供所 `WorkspaceGUID` `WorkspaceKey` 收集監視資料儲存所在之 Log Analytics 工作區的 base64 編碼版本。 您可以 `WorkspaceGUID` `WorkspaceKey` 在上一節中找到和使用步驟1和2。

您可以在此範例中找到 Azure Stack 中樞叢集支援的 API 定義- [kubernetes-container-monitoring_existing_workspace_id_and_key.js](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)。 具體而言，請在 **kubernetesConfig** 中尋找 **addons** 屬性：

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>設定代理程式資料收集

開始 with chart 1.0.0 版，代理程式資料集合設定是由 ConfigMap 控制。 請參閱[這裡](container-insights-agent-config.md)的關於代理程式資料收集設定的檔。

成功部署圖表之後，您可以從 Azure 入口網站的容器 Azure 監視器中，檢查混合式 Kubernetes 叢集的資料。  

>[!NOTE]
>從代理程式到 Azure Log Analytics 工作區中的認可，內嵌延遲大約需要五到十分鐘。 叢集的狀態會顯示 [**沒有資料**] 或 [**未知**] 的值，直到 Azure 監視器中有所有必要的監視資料為止。

## <a name="configure-proxy-endpoint"></a>設定 proxy 端點

從圖表版本2.7.1 開始，圖表將支援以 chart 參數指定 proxy 端點 `omsagent.proxy` 。 這可讓它透過您的 proxy 伺服器進行通訊。 容器代理程式和 Azure 監視器的 Azure 監視器之間的通訊可以是 HTTP 或 HTTPS proxy 伺服器，同時支援匿名和基本驗證（使用者名稱/密碼）。

Proxy 設定值具有下列語法：`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>如果您的 proxy 伺服器不需要驗證，您仍然需要指定虛擬使用者名稱/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 描述 |
|--------|-------------|
|通訊協定 | http 或 https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|密碼 | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器的位址或 FQDN |
|連接埠 | Proxy 伺服器的選擇性埠號碼 |

例如： `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

如果您將通訊協定指定為**HTTP**，則會使用 SSL/TLS 安全連線來建立 HTTP 要求。 您的 proxy 伺服器必須支援 SSL/TLS 通訊協定。

## <a name="troubleshooting"></a>疑難排解

如果您在嘗試啟用混合式 Kubernetes 叢集的監視時遇到錯誤，請複製 PowerShell 腳本[TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1)並將它儲存到您電腦上的資料夾。 此腳本的目的是為了協助偵測並修正所遇到的問題。 其設計用來偵測和嘗試更正的問題如下：

- 指定的 Log Analytics 工作區有效
- Log Analytics 工作區是使用 [容器的 Azure 監視器] 解決方案進行設定。 如果沒有，請設定工作區。
- OmsAgent replicaset pod 正在執行
- OmsAgent daemonset pod 正在執行
- OmsAgent 健全狀況服務正在執行
- 在容器化代理程式上設定的 Log Analytics 工作區識別碼和金鑰，與深入解析所設定的工作區相符。
- 驗證所有 Linux 背景工作角色節點都有 `kubernetes.io/role=agent` 標籤可排程 rs pod。 如果不存在，請將它加入。
- 驗證或在叢集中 `cAdvisor secure port:10250` `unsecure port: 10255` 的所有節點上開啟。

若要使用 Azure PowerShell 執行，請在包含腳本的資料夾中使用下列命令：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>後續步驟

啟用監視以收集混合式 Kubernetes 叢集的健康情況和資源使用率，以及在其上執行的工作負載時，請瞭解[如何使用](container-insights-analyze.md)容器的 Azure 監視器。
