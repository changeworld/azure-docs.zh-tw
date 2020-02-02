---
title: Azure Service Fabric-設定容器存放庫認證
description: 設定存放庫認證以從 container registry 下載映射
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934984"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>設定應用程式的儲存機制認證以下載容器映射

藉由將 `RepositoryCredentials` 新增至應用程式資訊清單的 `ContainerHostPolicies` 區段來設定容器登錄驗證。 新增容器登錄的帳戶和密碼（在下列範例中為*myregistry.azurecr.io* ），這可讓服務從存放庫下載容器映射。

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

建議您使用部署到叢集所有節點的加密憑證，以加密存放庫密碼。 當 Service Fabric 將服務套件部署至叢集時，會使用加密憑證將加密文字解密。 Invoke-ServiceFabricEncryptText Cmdlet 會用來建立密碼的加密文字，其已新增至 ApplicationManifest.xml 檔案。
如需憑證和加密語義的詳細資訊，請參閱[秘密管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>設定整個叢集的認證

Service Fabric 可讓您設定整個叢集的認證，以供應用程式用來作為預設存放庫認證。

若要啟用或停用這項功能，您可以在 ApplicationManifest 中，將 `UseDefaultRepositoryCredentials` 屬性新增至具有 `true` 或 `false` 值的 `ContainerHostPolicies`。

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

Service Fabric 接著會使用預設存放庫認證，您可以在 [`Hosting`] 區段下的 [ClusterManifest] 中指定。  如果 `UseDefaultRepositoryCredentials` 是 `true`，Service Fabric 會從 ClusterManifest 中讀取下列值：

* DefaultContainerRepositoryAccountName (字串)
* DefaultContainerRepositoryPassword (字串)
* IsDefaultContainerRepositoryPasswordEncrypted (布林值)
* DefaultContainerRepositoryPasswordType （字串）

以下是可在 ClusterManifestTemplate 的 `Hosting` 區段內新增的範例。 您可以在建立叢集時或稍後於設定升級中新增 [`Hosting`] 區段。 如需詳細資訊，請參閱[變更 Azure Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)及[管理 Azure Service Fabric 應用程式祕密](service-fabric-application-secret-management.md)

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

## <a name="use-tokens-as-registry-credentials"></a>使用權杖做為登錄認證

Service Fabric 支援使用權杖做為認證，以下載容器的映射。  此功能會利用基礎虛擬機器擴展集的*受控識別*來向登錄進行驗證，而不必管理使用者認證。  如需詳細資訊，請參閱[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。  使用此功能需要進行下列步驟：

1. 請確定 VM 已啟用*系統指派的受控識別*。

    ![Azure 入口網站：建立虛擬機器擴展集識別選項](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. 授與虛擬機器擴展集的許可權，從登錄中提取/讀取映射。 從 Azure 入口網站中 Azure Container Registry 的 [存取控制（IAM）] 分頁中，為您的虛擬機器新增*角色指派*：

    ![將 VM 主體新增至 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 接下來，修改您的應用程式資訊清單。 在 [`ContainerHostPolicies`] 區段中，將屬性加入 `‘UseTokenAuthenticationCredentials=”true”`。

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
    > 旗標 `UseDefaultRepositoryCredentials` 設定為 true，而 `UseTokenAuthenticationCredentials` 為 true 時，會在部署期間造成錯誤。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Container registry 驗證](../container-registry/container-registry-authentication.md)。
