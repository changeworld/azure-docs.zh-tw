---
title: Azure Service Fabric-使用 Service Fabric 應用程式 KeyVault 參考
description: 本文說明如何使用適用于應用程式秘密的 service fabric KeyVaultReference 支援。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: c4de6ae17ae728e1dbadbd6d6e2d94c0e1471112
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261136"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference Service Fabric 應用程式的支援 (預覽) 

建立雲端應用程式時，常見的挑戰是如何安全地儲存您的應用程式所需的秘密。 例如，您可能想要將容器存放庫認證儲存在 keyvault 中，並在應用程式資訊清單中加以參考。 Service Fabric KeyVaultReference 會使用 Service Fabric 受控身分識別，並可讓您輕鬆地參考 keyvault 秘密。 本文的其餘部分將詳細說明如何使用 Service Fabric KeyVaultReference，並包含一些一般使用方式。

> [!IMPORTANT]
> 不建議在生產環境中使用此預覽功能。

> [!NOTE]
> KeyVault 參考預覽功能僅支援已建立 [版本](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning) 的密碼。 不支援無版本秘密。

## <a name="prerequisites"></a>Prerequisites

- 應用程式 (MIT) 的受控識別
    
    Service Fabric KeyVaultReference 支援使用應用程式的受控識別，因此規劃使用 KeyVaultReferences 的應用程式應該使用受控識別。 請依照本 [檔](concepts-managed-identity.md) 為您的應用程式啟用受控識別。

- 集中秘密儲存 (CSS) 。

    中央秘密存放區 (CSS) 是 Service Fabric 的加密本機秘密快取。 CSS 是本機秘密存放區快取，可將機密資料（例如密碼、權杖和金鑰）保持在記憶體中加密。 提取之後，KeyVaultReference 就會在 CSS 中快取。

    將下列新增至中的叢集設定 `fabricSettings` ，以啟用 KeyVaultReference 支援所需的所有必要功能。

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
    > 建議您針對 CSS 使用個別的加密憑證。 您可以將它新增至 "CentralSecretService" 區段。
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
為了讓變更生效，您也必須變更升級原則，以便在升級透過叢集進行升級時，指定每個節點上的 Service Fabric 執行時間的強制重新開機。 此重新開機可確保新啟用的系統服務已在每個節點上啟動並執行。 在下列程式碼片段中，forceRestart 是必要的設定;使用您現有的值來進行其餘的設定。
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
- 授與應用程式的受控識別存取權給 keyvault

    參考此 [檔](how-to-grant-access-other-resources.md) ，瞭解如何將受控識別存取權授與 keyvault。 另請注意，如果您使用系統指派的受控識別，則只會在應用程式部署之後建立受控識別。

## <a name="keyvault-secret-as-application-parameter"></a>將秘密 Keyvault 為應用程式參數
假設應用程式需要讀取儲存在 keyvault 中的後端資料庫密碼，Service Fabric KeyVaultReference 支援可讓您輕鬆進行。 下列範例會 `DBPassword` 使用 Service Fabric KeyVaultReference 支援，從 keyvault 讀取秘密。

- 將區段新增至 settings.xml

    `DBPassword`使用類型 `KeyVaultReference` 和值定義參數`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 參考 ApplicationManifest.xml 中的新區段 `<ConfigPackagePolicies>`

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

- 在您的應用程式中使用 KeyVaultReference

    服務具現化的 Service Fabric 將會使用應用程式的受控識別來解析 KeyVaultReference 參數。 下所列的每個參數 `<Section  Name=dbsecrets>` 都是 EnvironmentVariable SecretPath 所指向之資料夾下的檔案。 以下 c # 程式碼片段示範如何在您的應用程式中讀取 DBPassword。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 在容器案例中，您可以使用該掛接點來控制將掛接的位置 `secrets` 。

## <a name="keyvault-secret-as-environment-variable"></a>將秘密 Keyvault 為環境變數

Service Fabric 環境變數現在支援 KeyVaultReference 類型，下列範例示範如何將環境變數系結至儲存在 KeyVault 中的秘密。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>將秘密 Keyvault 為容器存放庫密碼
KeyVaultReference 是支援的容器 RepositoryCredentials 類型，以下範例顯示如何使用 keyvault 參考作為容器存放庫密碼。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>常見問題集
- 受控識別必須啟用 KeyVaultReference 支援，如果使用 KeyVaultReference 而不啟用受控識別，則您的應用程式啟用將會失敗。

- 如果您使用系統指派的身分識別，它只會在應用程式部署之後建立，而這會建立迴圈相依性。 部署應用程式之後，您可以授與系統指派的身分識別存取權給 keyvault。 您可以依名稱 {cluster}/{application name} 尋找系統指派的身分識別/{servicename}

- Keyvault 必須與您的 service fabric 叢集位於相同的訂用帳戶中。 

## <a name="next-steps"></a>後續步驟

* [Azure KeyVault 檔](../key-vault/index.yml)
