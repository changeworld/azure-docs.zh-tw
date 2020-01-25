---
title: 在 Service Fabric 網狀應用程式中使用以 Azure 檔案儲存體為基礎的磁片區
description: 了解如何使用 Azure CLI 在服務內裝載以檔案儲存體為基礎的磁碟區，以在 Azure Service Fabric Mesh 應用程式中儲存狀態。
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718815"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>在 Service Fabric Mesh 應用程式中裝載以檔案儲存體為基礎的磁碟區 

本文說明如何在 Service Fabric Mesh 應用程式的服務中，裝載以檔案儲存體為基礎的磁碟區。  檔案儲存體磁碟區驅動程式是用來將檔案儲存體共用裝載至容器的 Docker 磁碟區驅動程式，可供用來保存服務狀態。 磁碟區會提供一般用途的檔案儲存體，並可讓您使用一般磁碟 I/O 檔案 API 來讀取/寫入檔案。  若要深入了解磁碟區和用於儲存應用程式資料的選項，請閱讀[儲存狀態](service-fabric-mesh-storing-state.md)。

若要在服務中裝載磁碟區，請在 Service Fabric Mesh 應用程式中建立磁碟區資源，然後在服務中參考該磁碟區。  您可以在[以 YAML 為基礎的資源檔](#declare-a-volume-resource-and-update-the-service-resource-yaml)或[以 JSON 為基礎的部署範本](#declare-a-volume-resource-and-update-the-service-resource-json)中，宣告磁碟區資源並在服務資源中加以參考。 在裝載磁碟區之前，請先建立 Azure 儲存體帳戶和[檔案儲存體中的檔案共用](/azure/storage/files/storage-how-to-create-file-share)。

## <a name="prerequisites"></a>必要條件
> [!NOTE]
> **WINDOWS RS5 開發電腦上部署的已知問題：** RS5 Windows 機器上的 Powershell Cmdlet SmbGlobalMapping 有 open bug，可防止掛接 Azurefile 磁片區。 以下是在本機開發電腦上裝載以 AzureFile 為基礎的磁片區時，所遇到的範例錯誤。
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
問題的因應措施是1）以 Powershell 系統管理員身分執行下列命令，2）重新開機電腦。
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成本文。 

若要依照本文的指示在本機使用 Azure CLI，請確認 `az --version` 至少傳回 `azure-cli (2.0.43)`。  請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 (或更新) Azure Service Fabric Mesh CLI 擴充功能模組。

若要登入 Azure 並設定您的訂用帳戶：

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>建立儲存體帳戶和檔案共用 (選擇性)
要有儲存體帳戶和檔案共用才能裝載檔案儲存體磁碟區。  您可以使用現有的 Azure 儲存體帳戶和檔案共用，也可以建立資源：

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>取得儲存體帳戶名稱和金鑰以及檔案共用名稱
在下列幾節中，儲存體帳戶名稱、儲存體帳戶金鑰和檔案共用名稱會以 `<storageAccountName>`、`<storageAccountKey>` 和 `<fileShareName>` 來表示。 

列出儲存體帳戶，並取得儲存體帳戶名稱和您想要使用的檔案共用：
```azurecli-interactive
az storage account list
```

取得檔案共用的名稱：
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

取得儲存體帳戶金鑰 ("key1")：
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

您也可以在 [Azure 入口網站](https://portal.azure.com)中尋找這些值：
* `<storageAccountName>` - 位於 [儲存體帳戶] 下方，這是用來建立檔案共用的儲存體帳戶名稱。
* `<storageAccountKey>` - 在 [儲存體帳戶] 下方選取您的儲存體帳戶，然後選取 [存取金鑰]，並使用 **key1** 下方的值。
* `<fileShareName>` - 在 [儲存體帳戶] 下方選取您的儲存體帳戶，然後選取 [檔案]。 要使用的名稱是所建立檔案共用的名稱。

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>宣告磁碟區資源並更新服務資源 (JSON)

為您在上一個步驟中找到的 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 值新增參數。 

建立磁碟區資源作為應用程式資源的對等。 指定名稱和提供者 (指定 "SFAzureFile" 可使用以檔案儲存體為基礎的磁碟區)。 在 `azureFileParameters` 中，為您在上一個步驟中找到的 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 值新增參數。

若要在服務中裝載磁碟區，請將 `volumeRefs` 新增至服務的 `codePackages` 元素。  `name` 是磁碟區的資源識別碼 (或磁碟區資源的部署範本參數)，以及 volume.yaml 資源檔中所宣告磁碟區的名稱。  `destinationPath` 是要作為磁碟區裝載目的地的本機目錄。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>宣告磁碟區資源並更新服務資源 (YAML)

在應用程式的「App resources」目錄中新增 volume.yaml 檔案。  指定名稱和提供者 (指定 "SFAzureFile" 可使用以檔案儲存體為基礎的磁碟區)。 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 是您在上一個步驟中找到的值。

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

更新「Service Resources」目錄中的 service.yaml 檔案，以在服務中裝載磁碟區。  將 `volumeRefs` 元素新增至 `codePackages` 元素。  `name` 是磁碟區的資源識別碼 (或磁碟區資源的部署範本參數)，以及 volume.yaml 資源檔中所宣告磁碟區的名稱。  `destinationPath` 是要作為磁碟區裝載目的地的本機目錄。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>後續步驟

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上檢視 Azure 檔案服務磁碟區的範例應用程式。
- 若要深入了解 Service Fabric 資源模型，請參閱 [Service Fabric Mesh 資源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。
