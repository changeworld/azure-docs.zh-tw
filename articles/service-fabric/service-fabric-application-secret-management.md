---
title: 管理 Azure Service Fabric 應用程式秘密
description: 了解如何保護 Service Fabric 應用程式中的祕密值 (無平台限制)。
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: 18090dd3e4046da2069e3035be4edb4d2f979204
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583244"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>在 Service Fabric 應用程式中管理已加密的祕密
本指南將逐步引導您完成管理 Service Fabric 應用程式中密碼的步驟。 密碼可以是任何機密資訊，例如儲存體連接字串、密碼或其他不會以純文字處理的值。

在 Service Fabric 應用程式中使用加密的祕密包含三個步驟：
* 設定加密憑證並將祕密加密。
* 指定應用程式中已加密的祕密。
* 從服務程式碼解開已加密的祕密。

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>設定加密憑證並將祕密加密
設定加密憑證，並使用該憑證來對 Windows 和 Linux 之間不同的祕密進行加密。
* [設定加密憑證，並在 Windows 叢集上將祕密加密。][secret-management-windows-specific-link]
* [設定加密憑證，並在 Linux 叢集上將秘密加密。][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>指定應用程式中已加密的祕密
上一個步驟說明了如何以憑證對祕密進行加密，並產生要在應用程式中使用的 base-64 編碼字串。 此 base-64 編碼字串可以在服務的 Settings.xml 中指定為已加密的[參數][parameters-link]，或在服務的 ServiceManifest.xml 中指定為已加密的[環境變數][environment-variables-link]。

在服務的 Settings.xml 組態檔中指定已加密的[參數][parameters-link]，並將 `IsEncrypted` 屬性設為 `true`：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
在服務的 ServiceManifest.xml 檔案中指定已加密的[環境變數][environment-variables-link]，並將 `Type` 屬性設為 `Encrypted`：
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

您也應該在應用程式資訊清單中指定憑證，以將秘密包含在 Service Fabric 應用程式中。 將**SecretsCertificate**元素新增至**ApplicationManifest.xml** ，並包含所需的憑證指紋。

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> 啟用指定 SecretsCertificate 的應用程式時，Service Fabric 會尋找相符的憑證，並將應用程式在憑證私密金鑰的完整許可權下執行的身分識別授與。 Service Fabric 也會監視憑證的變更，並據以重新套用許可權。 若要偵測一般名稱所宣告之憑證的變更，Service Fabric 會執行定期工作，以尋找所有相符的憑證，並將它與快取的指紋清單進行比較。 當偵測到新的指紋時，表示該主體的憑證已更新。 此工作會在叢集的每個節點上每分鐘執行一次。
>
> 雖然 SecretsCertificate 允許以主體為基礎的宣告，但請注意，加密的設定會系結至用來加密用戶端上設定的金鑰組。 您必須確定原始加密憑證（或對等專案）符合以主體為基礎的宣告，並已在可能裝載應用程式之叢集的每個節點上安裝，包括其對應的私密金鑰。 所有符合以主旨為基礎的宣告，並使用與原始加密憑證相同的金鑰組建立的有效憑證，都會被視為對等。
>

### <a name="inject-application-secrets-into-application-instances"></a>將應用程式密碼插入應用程式執行個體內
在理想情況下，部署至不同的環境應儘可能自動化。 這可以藉由在建置環境中執行密碼加密，並在建立應用程式執行個體時提供加密的密碼做為參數來實現。

#### <a name="use-overridable-parameters-in-settingsxml"></a>在 Settings.xml 中使用可覆寫參數
Settings.xml 組態檔允許可以在應用程式建立時提供的可覆寫參數。 使用 `MustOverride` 屬性而非提供參數的值︰

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

若要覆寫 Settings.xml 中的值，請宣告 ApplicationManifest.xml 中服務的覆寫參數︰

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

現在可以在建立應用程式執行個體時，將值指定為「應用程式參數」 ** 。 可以使用 PowerShell 編寫指令碼 (或以 C# 撰寫) 來建立應用程式執行個體，使其在建置流程中很容易整合。

若使用 PowerShell，則參數會提供給 `New-ServiceFabricApplication` 命令當做 [雜湊表](https://technet.microsoft.com/library/ee692803.aspx)：

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

若使用 C#，則應用程式參數在 `ApplicationDescription` 中會指定為 `NameValueCollection`：

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>從服務程式碼解開已加密的祕密
用於存取[參數][parameters-link]和[環境變數][environment-variables-link]的 API 可輕易地對已加密的值進行解密。 由於加密的字串包含用於加密的憑證相關資訊，因此您不需要手動指定憑證。 只需要在執行服務的節點上安裝憑證。

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>後續步驟
* Service Fabric[秘密存放區](service-fabric-application-secret-store.md) 
* 深入了解[應用程式及服務安全性](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md