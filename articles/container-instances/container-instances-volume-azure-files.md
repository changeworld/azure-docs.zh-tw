---
title: 將 Azure 檔卷裝載到容器組
description: 了解如何掛接 Azure 檔案磁碟區來保存 Azure 容器執行個體的狀態
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561332"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>在 Azure 容器執行個體中掛接 Azure 檔案共用

根據預設，Azure 容器執行個體都是無狀態的。 如果容器損毀或停止，其所有狀態都會遺失。 若要在容器超過存留期後保存其狀態，您必須從外部存放區掛接磁碟區。 如本文所示，Azure 容器實例可以裝載使用[Azure 檔](../storage/files/storage-files-introduction.md)創建的 Azure 檔共用。 Azure 檔提供託管在 Azure 存儲中完全託管的檔共用，這些共用可通過行業標準伺服器訊息區 （SMB） 協定訪問。 將 Azure 檔案共用與 Azure 容器執行個體搭配使用，可提供類似於將 Azure 檔案共用與 Azure 虛擬機器搭配使用的檔案共用功能。

> [!NOTE]
> 目前只有 Linux 容器才能掛接 Azure 檔案共用。 在[概述](container-instances-overview.md#linux-and-windows-containers)中查找當前平臺差異。
>
> 將 Azure 檔共用安裝到容器實例類似于 Docker[綁定裝載](https://docs.docker.com/storage/bind-mounts/)。 請注意，如果將共用裝載到存在檔或目錄的容器目錄中，則這些檔或目錄將被裝載遮蓋，並且在容器運行時無法訪問。
>

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在搭配使用 Azure 檔案共用與 Azure 容器執行個體前，您必須先建立 Azure 檔案共用。 請執行下列指令碼來建立儲存體帳戶，以裝載檔案共用和共用本身。 儲存體帳戶名稱必須為全域獨有，指令碼才能為基礎字串加上隨機值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>取得儲存體認證

若要在 Azure 容器執行個體中掛接 Azure 檔案共用來作為磁碟區，您需要三個值：儲存體帳戶名稱、共用名稱和儲存體存取金鑰。

* **存儲帳戶名稱**- 如果您使用前面的腳本，則存儲帳戶名稱存儲在變數中`$ACI_PERS_STORAGE_ACCOUNT_NAME`。 若要查看帳戶名稱，請輸入：

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **共用名稱稱**- 此值已為已知（定義`acishare`為前面的腳本中）

* **存儲帳戶金鑰**- 可以使用以下命令找到此值：

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>部署容器和裝載卷 - CLI

要使用 Azure CLI 將 Azure 檔共用裝載為容器中的卷，請在使用[az 容器創建][az-container-create]容器創建容器時指定共用和卷裝載點。 如果按照前面的步驟操作，則可以使用以下命令來裝載之前創建的共用，以創建容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

該`--dns-name-label`值必須在創建容器實例的 Azure 區域中是唯一的。 如果您在執行命令時收到 **DNS 名稱標籤**錯誤訊息，請更新上方命令中的值。

## <a name="manage-files-in-mounted-volume"></a>管理已掛接磁碟區中的檔案

啟動容器後，可以使用通過 Microsoft [aci-hello 檔][aci-hellofiles]映射部署的簡單 Web 應用在指定的裝載路徑上的 Azure 檔共用中創建小文字檔。 使用 [az container show][az-container-show] 命令取得 Web 應用程式的完整網域名稱 (FQDN)：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

使用應用保存文本後，可以使用[Azure 門戶][portal]或 Microsoft [Azure 存儲資源管理器][storage-explorer]等工具檢索和檢查寫入檔共用的檔。

## <a name="deploy-container-and-mount-volume---yaml"></a>部署容器和裝載卷 - YAML

還可以使用 Azure CLI 和[YAML 範本](container-instances-multi-container-yaml.md)部署容器組並將卷裝入容器中。 在部署由多個容器組成的容器組時，通過 YAML 範本進行部署是首選方法。

以下 YAML 範本定義一個容器組，其中一個容器`aci-hellofiles`使用映射創建。 容器裝載以前作為卷創建的 Azure 檔*共用*。 如果指示，請輸入承載檔共用的存儲帳戶的名稱和存儲金鑰。 

與 CLI 示例中一樣`dnsNameLabel`，該值必須在創建容器實例的 Azure 區域中是唯一的。 如果需要，更新 YAML 檔中的值。

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

若要透過 YAML 範本進行部署，請將上述 YAML 儲存到名為 `deploy-aci.yaml` 的檔案，然後使用  參數執行 [az container create`--file`][az-container-create] 命令：

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>部署容器和裝載卷 - 資源管理器

除了 CLI 和 YAML 部署之外，還可以使用 Azure[資源管理器範本](/azure/templates/microsoft.containerinstance/containergroups)部署容器組並將卷裝入容器中。

首先，填入範本的容器群組 `properties` 區段中的 `volumes` 陣列。 

然後，對於要在其中裝載卷的每個容器，在容器定義的`volumeMounts``properties`部分填充陣列。

以下資源管理器範本定義一個容器組，其中一個容器使用`aci-hellofiles`映射創建。 容器裝載以前作為卷創建的 Azure 檔*共用*。 如果指示，請輸入承載檔共用的存儲帳戶的名稱和存儲金鑰。 

與前面的示例中一樣，該`dnsNameLabel`值必須在創建容器實例的 Azure 區域中是唯一的。 如果需要，更新範本中的值。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

若要透過 Resource Manager 範本進行部署，請將上述 JSON 儲存到名為 `deploy-aci.json` 的檔案，然後使用  參數執行 [az group deployment create`--template-file`][az-group-deployment-create] 命令：

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>掛接多個磁碟區

要在容器實例中裝載多個卷，必須使用 Azure[資源管理器範本](/azure/templates/microsoft.containerinstance/containergroups)、YAML 檔或其他程式設計方法進行部署。 要使用範本或 YAML 檔，請提供共用詳細資訊，並通過在檔`volumes``properties`部分中填充陣列來定義卷。 

例如，如果在存儲帳戶*myStorageAccount*中創建了名為*share1*和*share2*的兩`volumes`個 Azure 檔共用，則資源管理器範本中的陣列將顯示類似于以下內容：

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

接下來，針對您想要掛接磁碟區所在容器群組中的每個容器，填入容器定義之 `properties` 區段中的 `volumeMounts` 陣列。 例如，這會掛接兩個先前定義的磁碟區：*myvolume1* 和 *myvolume2*：

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>後續步驟

了解如何在 Azure 容器執行個體中掛接其他類型的磁碟區：

* [在 Azure 容器執行個體中掛接 emptyDir 磁碟區](container-instances-volume-emptydir.md)
* [在 Azure 容器執行個體中掛接 gitRepo 磁碟區](container-instances-volume-gitrepo.md)
* [在 Azure 容器執行個體中掛接秘密磁碟區](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create