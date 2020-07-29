---
title: 管理 Azure Service Fabric 網狀應用程式秘密
description: 管理應用程式祕密，以利您安全地建立及部署 Service Fabric Mesh 應用程式。
ms.date: 4/2/2019
ms.topic: conceptual
ms.openlocfilehash: d7946092a0bebe374404870fcd711ad33cc98b11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75461926"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>管理 Service Fabric Mesh 應用程式秘密
Service Fabric Mesh 支援以祕密作為 Azure 資源。 Service Fabric Mesh 秘密可以是任何機密文字資訊，例如儲存體連接字串、密碼，或其他應安全地儲存和傳輸的值。 本文說明如何使用 Service Fabric Secure Store Service 來部署和維護祕密。

Mesh 應用程式秘密包含：
* **祕密**資源，也就是可儲存文字祕密的容器。 包含在**祕密**資源中的祕密會以安全的方式儲存和傳輸。
* 一或多個儲存在**祕密**資源容器中的**祕密/值**資源。 各個**祕密/值**資源可用版本號碼來區分。 您無法修改**祕密/值**資源的版本，而只能附加新版本。

管理祕密的步驟如下：
1. 使用 Inlinedvalue 類別種類和 SecretsStoreRef contentType 定義，在 Azure 資源模型 YAML 或 JSON 檔案中宣告網格**秘密**資源。
2. 在 Azure 資源模型 YAML 或 JSON 檔案中宣告將儲存在**秘密**資源中的網格**秘密/值**資源（來自步驟1）。
3. 將 Mesh 應用程式修改成參考 Mesh 祕密值。
4. 部署或輪流升級 Mesh 應用程式以使用祕密值。
5. 使用 Azure "az" CLI 命令進行 Secure Store Service 生命週期管理。

## <a name="declare-a-mesh-secrets-resource"></a>宣告 Mesh 祕密資源
在 Azure 資源模型 JSON 或 YAML 檔案中，使用 Inlinedvalue 類別種類定義來宣告網格秘密資源。 Mesh 祕密資源支援源自 Secure Store Service 的秘密。 
>
下列範例說明如何在 JSON 檔案中宣告 Mesh 祕密資源：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
下列範例說明如何在 YAML 檔案中宣告 Mesh 祕密資源：
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>宣告 Mesh 祕密/值資源
Mesh 祕密/值資源須依賴在先前的步驟中定義的 Mesh 祕密資源。

關於 "resources" 區段中 "value:" 與 "name:" 欄位之間的關聯性："name:" 字串中以冒號分隔的第二個部分是用於秘密的版本號碼，而冒號前面的名稱必須符合它所依賴的 Mesh 秘密值。 以元素 ```name: mysecret:1.0``` 為例，版本號碼為 1.0，而名稱 ```mysecret``` 必須符合先前定義的 ```"value": "mysecret"```。

>
下列範例說明如何在 JSON 檔案中宣告 Mesh 祕密/值資源：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
下列範例說明如何在 YAML 檔案中宣告 Mesh 祕密/值資源：
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>將 Mesh 應用程式修改成參考 Mesh 祕密值
Service Fabric Mesh 應用程式必須可辨識下列兩個字串才能使用 Secure Store Service 祕密值：
1. Microsoft.ServiceFabricMesh/Secrets.name 包含檔案的名稱，且會包含純文字格式的祕密值。
2. Windows 或 Linux 環境變數 "Fabric_SettingPath" 包含可存取 Secure Store Service 祕密值所在檔案的目錄路徑。 就 Windows 裝載的 Mesh 應用程式而言，此路徑為 "C:\Settings"；就 Linux 裝載的 Mesh 應用程式而言，則是 "/var/settings"。

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>部署或使用輪流升級讓 Mesh 應用程式使用祕密值
只有資源模型宣告的部署，才可建立祕密和/或具有版本的祕密/值。 建立這些資源的唯一方式，是使用 **az mesh deployment** 命令藉以傳遞資源模型 JSON 或 YAML 檔案，如下所示：

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>適用於 Secure Store Service 生命週期管理的 Azure CLI 命令

### <a name="create-a-new-secrets-resource"></a>建立新的祕密資源
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
傳遞 **template-file** 或 **template-uri** (勿同時傳遞)。

例如：
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az 網狀部署 create--HTTPs： \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>顯示祕密
傳回祕密的描述 (但不傳回值)。
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>刪除祕密

- Mesh 應用程式正在參考的祕密無法刪除。
- 刪除祕密資源時，將一併刪除所有的秘密/資源版本。
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>列出訂用帳戶中的祕密
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>列出資源群組中的祕密
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>列出祕密的所有版本
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>顯示祕密版本值
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>刪除祕密版本值
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>後續步驟 
若要深入了解 Service Fabric Mesh，請閱讀下列概觀：
- [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)
