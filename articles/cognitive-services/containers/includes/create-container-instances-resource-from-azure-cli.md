---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Azure CLI 建立 Azure 容器實例資源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b72eff3ffac37d2d10cd74c345eac8289b651
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585671"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>從 Azure CLI 建立 Azure 容器實例資源

下列 YAML 會定義 Azure 容器實例資源。 將內容複寫並貼到名為的新檔案， `my-aci.yaml` 並以您自己的值取代加上批註的值。 如需有效的 YAML，請參閱[範本格式][template-format]。 請參閱[容器存放庫和映射][repositories-and-images]，以取得可用映射名稱及其對應的儲存機制。 如需容器實例之 YAML 參考的詳細資訊，請參閱[YAML 參考： Azure 容器實例][aci-yaml-ref]。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access.
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
> 並非所有位置都具有相同的 CPU 和記憶體可用性。 如需每個位置和 OS 的容器可用資源清單，請參閱[location 和 resources][location-to-resource]資料表。

我們會依賴我們為命令所建立的 YAML 檔案 [`az container create`][azure-container-create] 。 從 Azure CLI 執行 `az container create` 命令，並將取代為 `<resource-group>` 您自己的。 此外，為了保護 YAML 部署內的值，請參閱[安全的值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

此命令的輸出會在 `Running...` 經過一段時間後，輸出變更為代表新建立之 ACI 資源的 JSON 字串。 容器映射的時間可能不會超過一段時間，但現在已部署資源。

> [!TIP]
> 請密切注意公開預覽 Azure 認知服務供應專案的位置，因為 YAML 需要適當地調整以符合該位置。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
