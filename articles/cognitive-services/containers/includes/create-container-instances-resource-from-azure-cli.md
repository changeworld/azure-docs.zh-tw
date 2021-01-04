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
ms.openlocfilehash: 903c36bc228faaac4cbf13fd00ca1b398e2f8add
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704827"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>從 Azure CLI 建立 Azure 容器實例資源

下列 YAML 會定義 Azure 容器實例資源。 將內容複寫並貼到名為的新檔案中， `my-aci.yaml` 並將批註的值取代為您自己的值。 如需有效的 YAML，請參閱 [範本格式][template-format] 。 請參閱 [容器存放庫和映射][repositories-and-images] 以取得可用的映射名稱及其對應的存放庫。 如需容器實例之 YAML 參考的詳細資訊，請參閱 [YAML 參考： Azure 容器實例][aci-yaml-ref]。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
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
> 並非所有位置都有相同的 CPU 和記憶體可用性。 請參閱 [位置和資源][location-to-resource] 表格，以取得每個位置和 OS 之容器可用資源的清單。

我們會依賴我們為命令建立的 YAML 檔案 [`az container create`][azure-container-create] 。 從 Azure CLI 中，執行 `az container create` 以您自己的取代的命令 `<resource-group>` 。 此外，為了保護 YAML 部署內的值，請參閱 [安全的值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

命令的輸出會在 `Running...` 一段時間後變更為 JSON 字串（代表新建立的 ACI 資源）時有效。 容器映射很可能無法使用一段時間，但現在已部署資源。

> [!TIP]
> 請密切注意公開預覽 Azure 認知服務供應專案的位置，因為 YAML 需要據以調整以符合地點。

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
