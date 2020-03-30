---
title: Azure 服務結構中央機密存儲
description: 本文介紹如何在 Azure 服務結構中使用中央機密存儲。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589159"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure 服務結構中的中央機密存儲 
本文介紹如何在 Azure 服務結構中使用中央機密存儲 （CSS） 在服務結構應用程式中創建機密。 CSS 是本地機密存儲緩存，用於在記憶體中加密敏感性資料（如密碼、權杖和金鑰）。

## <a name="enable-central-secrets-store"></a>啟用中央機密存儲
將以下腳本添加到群集配置下`fabricSettings`，以啟用 CSS。 我們建議您使用 CSS 的群集證書以外的證書。 確保加密證書安裝在所有節點上，`NetworkService`並且已讀取證書私密金鑰的許可權。
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
## <a name="declare-a-secret-resource"></a>聲明機密資源
可以使用 Azure 資源管理器範本或 REST API 創建機密資源。

### <a name="use-resource-manager"></a>使用 Resource Manager

使用以下範本使用資源管理器創建機密資源。 範本創建一個秘密`supersecret`資源，但尚未為機密資源設置任何值。


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

要使用`supersecret`REST API 創建機密資源，請對`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`發出 PUT 請求。 您需要群集證書或管理用戶端憑證來創建機密資源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>設置機密值

### <a name="use-the-resource-manager-template"></a>使用資源管理器範本

使用以下資源管理器範本創建和設置機密值。 此範本將`supersecret`機密資源的秘密值集為版本`ver1`。
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

使用以下腳本使用 REST API 設置機密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>檢查機密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>使用應用程式中的機密

按照以下步驟在 Service Fabric 應用程式中使用機密。

1. 在**設置.xml**檔中添加包含以下程式碼片段的部分。 請注意，該值採用格式 =`secretname:version`。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 導入**應用程式清單.xml**中的部分。
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

   環境變數`SecretPath`將指向存儲所有機密的目錄。 `testsecrets`節下列出的每個參數都存儲在單獨的檔中。 應用程式現在可以使用機密，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 將機密安裝到容器中。 使機密在容器內可用所需的唯一更改是 到`specify`中的`<ConfigPackage>`裝載點。
以下程式碼片段是修改後的**應用程式清單.xml**。  

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
   機密可在容器內的裝載點下使用。

1. 可以通過指定`Type='SecretsStoreRef`將機密綁定到進程環境變數。 以下程式碼片段是如何在**ServiceManifest.xml**`supersecret`中`ver1`將版本綁定到`MySuperSecret`環境變數的示例。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>後續步驟
瞭解有關[應用程式和服務安全性](service-fabric-application-and-service-security.md)的更多。
