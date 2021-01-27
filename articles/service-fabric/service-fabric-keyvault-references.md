---
title: Azure Service Fabric-使用 Service Fabric 應用程式 KeyVault 參考
description: 本文說明如何使用適用于應用程式秘密的 service fabric KeyVaultReference 支援。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898591"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Azure 部署 Service Fabric 應用程式的 KeyVaultReference 支援

建立雲端應用程式時，常見的挑戰是如何安全地將秘密散發給您的應用程式。 例如，您可能會想要將資料庫金鑰部署到您的應用程式，而不需在管線或運算子期間公開索引鍵。 Service Fabric 的 KeyVaultReference 支援可讓您輕鬆地將秘密部署到您的應用程式，只要參考儲存在 Key Vault 中的密碼 URL 即可。 Service Fabric 將會代表您應用程式的受控識別來處理提取該秘密，並使用秘密來啟用應用程式。

> [!NOTE]
> Service Fabric 應用程式的 KeyVaultReference 支援是 GA (預覽版) 從 Service Fabric 版本 7.2 CU5 開始。 建議您在使用這項功能之前，先升級為此版本。

> [!NOTE]
> Service Fabric 應用程式的 KeyVaultReference 支援僅支援已建立 [版本](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) 的密碼。 不支援無版本秘密。 Key Vault 必須與您的 service fabric 叢集位於相同的訂用帳戶中。 

## <a name="prerequisites"></a>先決條件

- Service Fabric 應用程式的受控識別

    Service Fabric KeyVaultReference 支援使用應用程式的受控識別來代表應用程式提取秘密，因此您的應用程式必須透過和指派受控識別來部署。 請依照本 [檔](concepts-managed-identity.md) 為您的應用程式啟用受控識別。

- 集中秘密儲存 (CSS) 。

    中央秘密存放區 (CSS) 是 Service Fabric 的加密本機秘密快取。 這項功能會在從 Key Vault 提取秘密後，使用 CSS 來保護和保存秘密。 若要使用這項功能，也需要啟用此選用的系統服務。 遵循這 [份檔](service-fabric-application-secret-store.md) 來啟用和設定 CSS。

- 授與應用程式的受控識別存取權給 keyvault

    參考此 [檔](how-to-grant-access-other-resources.md) ，瞭解如何將受控識別存取權授與 keyvault。 另請注意，如果您使用系統指派的受控識別，則只會在應用程式部署之後建立受控識別。 這可能會建立競爭條件，其中應用程式會先嘗試存取秘密，才能取得保存庫的存取權。 系統指派的身分識別名稱會是 `{cluster name}/{application name}/{service name}` 。
    
## <a name="use-keyvaultreferences-in-your-application"></a>在您的應用程式中使用 KeyVaultReferences
KeyVaultReferences 可透過數種方式使用
- [作為環境變數](#as-an-environment-variable)
- [以檔案形式掛接到您的容器中](#mounted-as-a-file-into-your-container)
- [作為容器存放庫密碼的參考](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>作為環境變數

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>以檔案形式掛接到您的容器中

- 將區段新增至 settings.xml

    `MySecret`使用類型 `KeyVaultReference` 和值定義參數`<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- 參考 ApplicationManifest.xml 中的新區段 `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 使用服務程式代碼中的秘密

    下所列的每個參數 `<Section  Name=MySecrets>` 都是 EnvironmentVariable SecretPath 所指向之資料夾下的檔案。 下列 c # 程式碼片段說明如何從您的應用程式讀取 MySecret。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > 掛接點會控制要掛接包含秘密值之檔案的資料夾。

### <a name="as-a-reference-to-a-container-repository-password"></a>作為容器存放庫密碼的參考

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>後續步驟

* [Azure KeyVault 檔](../key-vault/index.yml)
* [深入瞭解集中秘密存放區](service-fabric-application-secret-store.md)
* [瞭解 Service Fabric 應用程式的受控識別](concepts-managed-identity.md)
