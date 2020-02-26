---
title: Azure Service Fabric 集中式秘密存放區
description: 本文說明如何在 Azure Service Fabric 中使用中央秘密存放區。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589159"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric 中的中央秘密存放區 
本文說明如何在 Azure Service Fabric 中使用中央秘密存放區（CSS），在 Service Fabric 的應用程式中建立秘密。 CSS 是一個本機秘密存放區快取，可讓機密資料（例如密碼、權杖和金鑰）在記憶體中加密。

## <a name="enable-central-secrets-store"></a>啟用集中式秘密存放區
將下列腳本新增至 [`fabricSettings`] 底下的叢集設定，以啟用 CSS。 我們建議您不要使用適用于 CSS 的叢集憑證以外的憑證。 請確定已在所有節點上安裝加密憑證，且該 `NetworkService` 具有憑證私密金鑰的讀取權限。
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>宣告秘密資源
您可以使用 Azure Resource Manager 範本或 REST API 來建立秘密資源。

### <a name="use-resource-manager"></a>使用 Resource Manager

使用下列範本來使用 Resource Manager 來建立秘密資源。 此範本會建立 `supersecret` 的秘密資源，但尚未為密碼資源設定任何值。


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>使用 REST API

若要使用 REST API 建立 `supersecret` 秘密資源，請提出 PUT 要求給 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`。 您需要叢集憑證或管理用戶端憑證，才能建立秘密資源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>設定密碼值

### <a name="use-the-resource-manager-template"></a>使用 Resource Manager 範本

使用下列 Resource Manager 範本來建立及設定秘密值。 此範本會將 `supersecret` 秘密資源的秘密值設定為 `ver1`版本。
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>使用 REST API

使用下列腳本來使用 REST API 來設定密碼值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>檢查秘密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>在您的應用程式中使用秘密

請遵循下列步驟，在您的 Service Fabric 應用程式中使用密碼。

1. 使用下列程式碼片段，在**設定 .xml**檔案中新增區段。 請注意，此值的格式為 {`secretname:version`}。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 匯入**ApplicationManifest**中的區段。
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   環境變數 `SecretPath` 會指向所有秘密儲存所在的目錄。 `testsecrets` 區段底下所列的每個參數都會儲存在不同的檔案中。 應用程式現在可以使用密碼，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 將秘密掛接至容器。 在容器內提供秘密所需的唯一變更，就是 `specify` `<ConfigPackage>`中的掛接點。
下列程式碼片段是修改過的**ApplicationManifest。**  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   您容器內的掛接點下會提供密碼。

1. 您可以藉由指定 `Type='SecretsStoreRef`，將秘密系結至進程環境變數。 下列程式碼片段是如何將 `supersecret` 版本 `ver1` 系結至**ServiceManifest**中的環境變數 `MySuperSecret` 的範例。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>後續步驟
深入瞭解[應用程式和服務安全性](service-fabric-application-and-service-security.md)。
