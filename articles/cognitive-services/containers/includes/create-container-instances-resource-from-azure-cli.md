---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Azure CLI 創建 Azure 容器實例資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689423"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>從 Azure CLI 創建 Azure 容器實例資源

下面的 YAML 定義 Azure 容器實例資源。 將內容複寫並粘貼到新檔中，命名`my-aci.yaml`並替換注釋的值。 有關有效的 YAML，請參閱[範本格式][template-format]。 有關可用映射名稱及其相應的存儲庫，請參閱[容器存儲庫和映射][repositories-and-images]。 有關容器實例的 YAML 引用的詳細資訊，請參閱[YAML 引用：Azure 容器實例][aci-yaml-ref]。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> 並非所有位置都具有相同的 CPU 和記憶體可用性。 有關每個位置和作業系統的容器可用資源的清單，請參閱[位置和資源][location-to-resource]表。

我們將依賴于我們為[`az container create`][azure-container-create]命令創建的 YAML 檔。 從 Azure CLI 中`az container create`，執行`<resource-group>`用您自己的 替換 的命令。 此外，為了在 YAML 部署中保護值，請參閱[安全值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

該命令的輸出在`Running...`輸出更改為表示新創建的 ACI 資源的 JSON 字串後，如果有效。 容器映射很可能一段時間內不可用，但現在已部署資源。

> [!TIP]
> 密切關注公共預覽 Azure 認知服務產品的位置，因為 YAML 需要相應地進行調整以匹配該位置。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
