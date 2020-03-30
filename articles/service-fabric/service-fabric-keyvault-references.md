---
title: Azure 服務結構 - 使用服務結構應用程式 KeyVault 引用
description: 本文介紹如何使用服務結構 KeyVaultReference 支援來訪問應用程式機密。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545478"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>對服務結構應用程式的關鍵Vault參考支援（預覽）

構建雲應用程式時面臨的一個常見挑戰是如何安全地存儲應用程式所需的機密。 例如，您可能希望將容器存儲庫憑據存儲在金鑰庫中，並在應用程式清單中引用它。 服務結構金鑰庫參考使用服務結構託管標識，便於引用金鑰庫機密。 本文的其餘部分詳細介紹了如何使用服務結構金鑰庫參考，並包括一些典型的用法。

## <a name="prerequisites"></a>Prerequisites

- 應用程式的託管標識 （MIT）
    
    服務結構金鑰庫參考支援使用應用程式的託管標識，因此計畫使用 KeyVault 參照的應用程式應使用託管標識。 請按照[本文檔為](concepts-managed-identity.md)應用程式啟用託管標識。

- 中央機密存儲 （CSS）。

    中央機密存儲 （CSS） 是服務結構加密的本地機密緩存。 CSS 是本地機密存儲緩存，用於在記憶體中加密敏感性資料（如密碼、權杖和金鑰）。 金鑰庫引用（一旦提取）將緩存在 CSS 中。

    將下面內容添加到下面的群集配置中`fabricSettings`，以啟用 KeyVaultReference 支援所需的所有功能。

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
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > 建議對 CSS 使用單獨的加密證書。 您可以在"中央保密服務"部分下添加它。
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
為了使更改生效，您還需要更改升級策略，以便在升級通過群集時為每個節點上強制重新開機 Service Fabric 運行時。 此重新開機可確保在每個節點上啟動新啟用的系統服務並運行。 在下面的程式碼片段中，力重新開機是基本設置;將現有值用於其餘設置。
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- 向金鑰庫授予應用程式的託管身份存取權限

    參考[本文檔](how-to-grant-access-other-resources.md)，瞭解如何授予對金鑰保存庫的託管標識存取權限。 另請注意，如果使用系統分配的託管標識，則僅在應用程式部署後創建託管標識。

## <a name="keyvault-secret-as-application-parameter"></a>金鑰庫金鑰作為應用程式參數
假設應用程式需要讀取存儲在金鑰庫中的後端資料庫密碼，服務結構金鑰庫參考支援使其變得簡單。 下面的示例使用`DBPassword`服務交換矩陣金鑰庫參考支援從金鑰保存庫讀取機密。

- 將節添加到設置.xml

    使用`DBPassword`類型`KeyVaultReference`和值定義參數`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 引用應用程式清單.xml 中的新部分，`<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 在應用程式中使用 KeyVault 參考

    服務具現化上的服務結構將使用應用程式的託管標識解析 KeyVault 參考參數。 下面`<Section  Name=dbsecrets>`列出的每個參數都將是環境變數秘密路徑指向的資料夾下的檔。 下面的 C# 程式碼片段演示如何在應用程式中讀取 DBPassword。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 對於容器方案，可以使用 MountPoint 來控制將裝載的位置`secrets`。

## <a name="keyvault-secret-as-environment-variable"></a>作為環境變數的金鑰庫金鑰庫金鑰

服務結構環境變數現在支援 KeyVault 參考類型，下面的示例演示如何將環境變數綁定到存儲在 KeyVault 中的秘密。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>金鑰庫金鑰作為容器存儲庫密碼
KeyVault 參考是容器存儲庫憑據支援的類型，下面的示例演示如何將金鑰保存庫引用用作容器存儲庫密碼。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>常見問題集
- 需要為 KeyVaultReference 支援啟用託管標識，如果使用 KeyVault 參考而不啟用託管標識，則應用程式啟動將失敗。

- 如果使用系統分配的標識，則僅在部署應用程式後創建該標識，這將創建迴圈依賴項。 部署應用程式後，可以向金鑰庫授予系統分配的標識存取權限。 您可以按名稱 [群集]/[應用程式名稱]/[服務名稱] 查找系統分配的標識。

- 金鑰保存庫需要與服務結構群集處於同一訂閱中。 

## <a name="next-steps"></a>後續步驟

* [Azure 金鑰庫文檔](https://docs.microsoft.com/azure/key-vault/)
