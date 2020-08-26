---
title: Azure Service Fabric 中央秘密存放區
description: 本文說明如何在 Azure Service Fabric 中使用中央秘密存放區。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869750"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric 中的集中秘密存放區 
本文說明如何使用 Azure Service Fabric 中的中央秘密存放區 (CSS) ，在 Service Fabric 應用程式中建立秘密。 CSS 是本機秘密存放區快取，可將機密資料（例如密碼、權杖和金鑰）保持在記憶體中加密。

  > [!NOTE] 
  > 當您第一次在 SF 7.1 版之前啟用 CSS 時。 CU3，啟動可能會失敗，並在發生下列情況時使 CSS 處於永久狀況不良的狀態：已在 Windows 驗證的叢集上啟用 CSS;CSS 已在任何叢集上啟用，但宣告不 `EncryptionCertificateThumbprint` 正確，或節點上未安裝對應的憑證/ACL。 若為 Windows 驗證叢集，請到7.1。 請先 CU3 再繼續。 若為其他叢集，請再次檢查這些不固定或進入7.1。 CU3.
  
## <a name="enable-central-secrets-store"></a>啟用中央秘密存放區
將下列腳本新增至中的叢集設定， `fabricSettings` 以啟用 CSS。 建議您針對 CSS 使用叢集憑證以外的憑證。 請確定加密憑證已安裝在所有節點上，且 `NetworkService` 具有憑證私密金鑰的讀取權限。
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
                    "value":  "1"
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
您可以使用 REST API 來建立秘密資源。
  > [!NOTE] 
  > 如果叢集使用 windows 驗證，則會透過不安全的 HTTP 通道傳送 REST 要求。 建議使用具有安全端點的 X509 型叢集。

若要 `supersecret` 使用 REST API 建立秘密資源，請對進行 PUT 要求 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` 。 您需要叢集憑證或系統管理員用戶端憑證來建立秘密資源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>設定秘密值

使用下列腳本來使用 REST API 設定秘密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>檢查秘密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>在您的應用程式中使用秘密

遵循這些步驟，在您的 Service Fabric 應用程式中使用秘密。

1. 使用下列程式碼片段，在 **settings.xml** 檔案中新增區段。 請注意，此值的格式為 { `secretname:version` }。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 匯入 **ApplicationManifest.xml**中的區段。
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

   環境變數 `SecretPath` 會指向儲存所有秘密的目錄。 區段底下所列的每個參數 `testsecrets` 都會儲存在不同的檔案中。 應用程式現在可以使用密碼，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 將秘密掛接至容器。 在容器內提供秘密所需的唯一變更就是中的 `specify` 掛接點 `<ConfigPackage>` 。
下列程式碼片段是修改過的 **ApplicationManifest.xml**。  

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
   秘密可在容器內的掛接點底下取得。

1. 您可以藉由指定，將秘密系結至進程環境變數 `Type='SecretsStoreRef` 。 下列程式碼片段是如何 `supersecret` `ver1` `MySuperSecret` 在 **ServiceManifest.xml**中將版本系結至環境變數的範例。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>後續步驟
深入瞭解 [應用程式和服務安全性](service-fabric-application-and-service-security.md)。
