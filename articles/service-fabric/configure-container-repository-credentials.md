---
title: Azure Service Fabric-設定容器存放庫認證
description: 設定存放庫認證以從 container registry 下載映射
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 47a3fb39693bf6143d4033eed437f65b7e63eabb
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978674"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>設定應用程式的存放庫認證以下載容器映射

新增 `RepositoryCredentials` 至 `ContainerHostPolicies` 應用程式資訊清單的區段，以設定 container registry 驗證。 在下列範例中，為您的容器登錄新增帳戶和密碼 (*myregistry.azurecr.io*) ，讓服務能夠從存放庫下載容器映射。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

建議您使用部署至叢集所有節點的加密憑證來加密存放庫密碼。 當 Service Fabric 將服務套件部署至叢集時，會使用加密憑證將加密文字解密。 Invoke-ServiceFabricEncryptText Cmdlet 會用來建立密碼的加密文字，其已新增至 ApplicationManifest.xml 檔案。
如需憑證和加密語義的詳細資訊，請參閱 [秘密管理](service-fabric-application-secret-management.md) 。

## <a name="configure-cluster-wide-credentials"></a>設定整個叢集的認證

Service Fabric 可讓您設定整個叢集的認證，以供應用程式用來作為預設存放庫認證。

您可以 `UseDefaultRepositoryCredentials` 使用或值將屬性加入 ApplicationManifest.xml 中，以啟用或停用這項功能 `ContainerHostPolicies` `true` `false` 。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric 接著會使用可在區段底下的 ClusterManifest 中指定的預設存放庫認證 `Hosting` 。  如果 `UseDefaultRepositoryCredentials` 是 `true`，Service Fabric 會從 ClusterManifest 中讀取下列值：

* DefaultContainerRepositoryAccountName (字串)
* DefaultContainerRepositoryPassword (字串)
* IsDefaultContainerRepositoryPasswordEncrypted (布林值)
* DefaultContainerRepositoryPasswordType (字串) 

以下是可在 ClusterManifestTemplate.json 檔案區段內新增的內容範例 `Hosting` 。 您 `Hosting` 可以在建立叢集時或稍後在設定升級中新增區段。 如需詳細資訊，請參閱[變更 Azure Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)及[管理 Azure Service Fabric 應用程式祕密](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>使用權杖作為登錄認證

Service Fabric 支援使用權杖作為認證，以下載容器的映射。  這項功能會利用基礎虛擬機器擴展集的 *受控識別* 來向登錄進行驗證，而不需要管理使用者認證。  如需詳細資訊，請參閱 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別。  使用此功能需要遵循下列步驟：

1. 請確定已為 VM 啟用 *系統指派的受控識別* 。

    ![Azure 入口網站：建立虛擬機器擴展集識別選項](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> 若為使用者指派的受控識別，請略過此步驟。 只要擴展集只與單一使用者指派的受控識別相關聯，其餘步驟的運作方式就會相同。

2. 授與虛擬機器擴展集的許可權，以從登錄提取/讀取映射。 從 Azure 入口網站之 Azure Container Registry 的 [存取控制] (IAM) 分頁]，為您的虛擬機器新增 *角色指派* ：

    ![將 VM 主體新增至 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 接下來，修改您的應用程式資訊清單。 在 `ContainerHostPolicies` 區段中，加入屬性 `‘UseTokenAuthenticationCredentials=”true”` 。

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > 當為 true 時，會將旗標 `UseDefaultRepositoryCredentials` 設為 true， `UseTokenAuthenticationCredentials` 會在部署期間造成錯誤。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Container registry 驗證](../container-registry/container-registry-authentication.md)。
