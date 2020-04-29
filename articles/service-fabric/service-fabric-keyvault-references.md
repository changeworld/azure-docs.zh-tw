---
title: Azure Service Fabric-使用 Service Fabric 應用程式 KeyVault 參考
description: 本文說明如何使用應用程式密碼的 service fabric KeyVaultReference 支援。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76545478"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric 應用程式的 KeyVaultReference 支援（預覽）

建立雲端應用程式時，常見的挑戰是如何安全地儲存應用程式所需的秘密。 例如，您可能會想要將容器儲存機制認證儲存在 keyvault 中，並在應用程式資訊清單中參考它。 Service Fabric KeyVaultReference 使用 Service Fabric 受控身分識別，並可讓您輕鬆地參考 keyvault 的秘密。 本文的其餘部分將詳細說明如何使用 Service Fabric KeyVaultReference，並包含一些一般的使用方式。

## <a name="prerequisites"></a>先決條件

- 應用程式的受控識別（MIT）
    
    Service Fabric KeyVaultReference 支援使用應用程式的受控識別，因此規劃以使用 KeyVaultReferences 的應用程式應該使用受控識別。 請遵循這[份檔](concepts-managed-identity.md)，為您的應用程式啟用受控識別。

- 中央秘密存放區（CSS）。

    中央秘密存放區（CSS）是 Service Fabric 的加密本機秘密快取。 CSS 是一個本機秘密存放區快取，可讓機密資料（例如密碼、權杖和金鑰）在記憶體中加密。 提取之後，就會在 CSS 中快取 KeyVaultReference。

    將以下程式新增至下`fabricSettings`的叢集設定，以啟用 KeyVaultReference 支援的所有必要功能。

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
    > 建議您針對 CSS 使用個別的加密憑證。 您可以將它新增至 "CentralSecretService" 區段底下。
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
為了讓變更生效，您也需要變更升級原則，以便在升級完成叢集時，在每個節點上指定強制重新開機 Service Fabric 執行時間。 此重新開機可確保新啟用的系統服務已在每個節點上啟動並執行。 在下列程式碼片段中，forceRestart 是必要的設定。將您現有的值用於其餘的設定。
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
- 將應用程式的受控識別存取權授與 keyvault

    參考這[份檔](how-to-grant-access-other-resources.md)，以瞭解如何將受控識別存取權授與 keyvault。 另請注意，如果您使用系統指派的受控識別，則只有在應用程式部署之後才會建立受控識別。

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault secret 作為應用程式參數
假設應用程式需要讀取儲存在 keyvault 中的後端資料庫密碼，Service Fabric KeyVaultReference 支援人員可輕鬆地進行。 下列範例會`DBPassword`使用 Service Fabric KeyVaultReference 支援，從 keyvault 讀取秘密。

- 將區段新增至設定 .xml

    定義`DBPassword`具有類型`KeyVaultReference`和值的參數`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 參考 ApplicationManifest 中的新區段`<ConfigPackagePolicies>`

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

    服務具現化 Service Fabric 會使用應用程式的受控識別來解析 KeyVaultReference 參數。 底下列出的每`<Section  Name=dbsecrets>`個參數都是 EnvironmentVariable SecretPath 所指向之資料夾下的檔案。 下列 c # 程式碼片段示範如何在您的應用程式中讀取 DBPassword。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 針對容器案例，您可以使用掛接點來控制將裝載的`secrets`位置。

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault secret 作為環境變數

Service Fabric 環境變數現在支援 KeyVaultReference 類型，下列範例示範如何將環境變數系結至儲存在 KeyVault 中的密碼。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault secret 作為容器存放庫密碼
KeyVaultReference 是容器 RepositoryCredentials 支援的類型，以下範例顯示如何使用 keyvault 參考作為容器存放庫密碼。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>常見問題集
- 受控識別必須啟用 KeyVaultReference 支援，如果使用 KeyVaultReference 而不啟用受控識別，您的應用程式啟用將會失敗。

- 如果您使用系統指派的身分識別，則只有在部署應用程式之後才會建立它，而這會建立迴圈相依性。 一旦部署您的應用程式，您就可以將系統指派的身分識別存取權限授與 keyvault。 您可以依名稱 {cluster}/{application name}/{servicename} 來尋找系統指派的身分識別

- Keyvault 必須位於與 service fabric 叢集相同的訂用帳戶中。 

## <a name="next-steps"></a>後續步驟

* [Azure KeyVault 檔](https://docs.microsoft.com/azure/key-vault/)
