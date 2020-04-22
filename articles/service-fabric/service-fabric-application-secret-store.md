---
title: Azure 服務結構中央機密存儲
description: 本文介紹如何在 Azure 服務結構中使用中央機密存儲。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770421"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure 服務結構中的中央機密存儲 
本文介紹如何在 Azure 服務結構中使用中央機密存儲 (CSS) 在服務結構應用程式中創建機密。 CSS 是本地機密存儲快取,用於在記憶體中加密敏感資料(如密碼、權杖和密鑰)。

## <a name="enable-central-secrets-store"></a>開啟中央機密儲存
將以下文稿添加到群集配置下`fabricSettings`,以啟用 CSS。 我們建議您使用 CSS 的群集證書以外的證書。 確保加密憑證安裝在所有節點上,`NetworkService`並且已讀取憑證私鑰的許可權。
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
## <a name="declare-a-secret-resource"></a>宣告機密資源
您可以使用 REST API 建立機密資源。
  > [!NOTE] 
  > 如果群集使用視窗身份驗證,則 REST 請求通過不安全的 HTTP 通道發送。 建議使用基於 X509 的群集,該群集具有安全終結點。

要使用`supersecret`REST API 創建機密資源`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`,請對 發出 PUT 請求。 您需要群集證書或管理用戶端證書來創建機密資源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>設定機密值

使用以下文稿使用 REST API 設置機密值。
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

1. 在**設定.xml**檔中添加包含以下代碼段的部分。 請注意,該值採用格式`secretname:version`= 。

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

   環境變數`SecretPath`將指向存儲所有機密的目錄。 `testsecrets`節下列出的每個參數都存儲在單獨的檔中。 應用程式現在可以使用機密,如下所示:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 將機密安裝到容器中。 使機密在容器內可用所需的唯一更改是到`specify`中的`<ConfigPackage>`裝載點。
以下代碼段是修改後**的 應用程式清單.xml**。  

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

1. 可以通過`Type='SecretsStoreRef`指定 將機密綁定到進程環境變數。 以下代碼段是如何在**ServiceManifest.xml**`supersecret`中`ver1`將版本`MySuperSecret`綁定到 環境變數的範例。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>後續步驟
瞭解有關[應用程式和服務安全性](service-fabric-application-and-service-security.md)的更多。
