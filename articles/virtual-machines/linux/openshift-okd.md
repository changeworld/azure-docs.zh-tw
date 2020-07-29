---
title: 在 Azure 中部署 OKD
description: 在 Azure 中部署 OKD。
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: a7524ea80906f9df5f63af9e7d672bbef500b0d9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373329"
---
# <a name="deploy-okd-in-azure"></a>在 Azure 中部署 OKD

您可使用這兩種方式之一在 Azure 中部署 OKD (之前稱為 OpenShift Origin)：

- 您可以手動部署所有必要的 Azure 基礎結構元件，然後遵循[OKD 檔](https://docs.okd.io)。
- 您也可以使用能夠簡化 OKD 叢集部署的現有 [Resource Manager 範本](https://github.com/Microsoft/openshift-origin)。

## <a name="deploy-using-the-okd-template"></a>使用 OKD 範本部署

若要使用 Resource Manager 範本進行部署，請使用參數檔案來提供輸入參數。 若要進一步自訂部署，請分支處理 GitHub 存放庫並變更適當的項目。

某些常見的自訂選項包括但不限於：

- 防禦 VM 大小 (azuredeploy.json 中的變數)
- 命名慣例 (azuredeploy.json 中的變數)
- OpenShift 叢集詳細規格，已透過主機檔案 (deployOpenShift.sh) 修改

[OKD 範本](https://github.com/Microsoft/openshift-origin)有多個分支可用於不同版本的 OKD。  根據您的需求，您可以直接從存放庫部署或可以分支處理存放庫，並且在部署前先進行自訂變更。

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。

以下是名為 azuredeploy.parameters.json 且具有所有必要輸入的參數檔案範例。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

以您的特定資訊取代參數。

不同版本可能有不同的參數，因此請確認您所用分支的必要參數。

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署


> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OKD 叢集和所有相關的資源部署到名為 openshiftrg 的資源群組。 此範本可直接參考自 Github 存放庫，同時使用名為 azuredeploy.parameters.json 的本機參數檔案。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根據部署的節點總數，至少需要 30 分鐘才能完成部署。 當部署完成時，OpenShift 主控台的 URL 和 OpenShift 主機的 DNS 名稱會列印到終端機。 或者，您可以從 Azure 入口網站檢視部署的輸出區段。

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

如果您不想困在命令列等候部署完成，請新增 `--no-wait` 作為群組部署的選項之一。 您可以從 Azure 入口網站，在資源群組的部署區段中擷取部署中的輸出。

## <a name="connect-to-the-okd-cluster"></a>連線到 OKD 叢集

部署完成時，使用 `OpenShift Console Url` 可透過瀏覽器連線至 OpenShift 主控台。 或者，您可以透過 SSH 連線到 OKD 主檔。 以下範例會使用部署的輸出：

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以使用 [az group delete](/cli/azure/group) 命令，移除資源群組、OpenShift 叢集和所有相關資源。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-container-platform-3x-post-deployment.md)
- [針對 OpenShift 部署進行疑難排解](./openshift-container-platform-3x-troubleshooting.md)
- [開始使用 OKD](https://docs.okd.io)
