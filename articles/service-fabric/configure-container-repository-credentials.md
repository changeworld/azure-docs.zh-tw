---
title: Azure 服務結構 - 配置容器存儲庫憑據
description: 配置存儲庫憑據以從容器註冊表下載映射
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934984"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>為應用程式佈建存儲庫憑據以下載容器映射

通過添加到`RepositoryCredentials`應用程式清單`ContainerHostPolicies`的部分來配置容器註冊表身份驗證。 添加容器註冊表的帳戶和*密碼（myregistry.azurecr.io*在下面的示例中），這允許服務從存儲庫下載容器映射。

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

建議使用部署到群集所有節點的加密證書來加密存儲庫密碼。 當 Service Fabric 將服務套件部署至叢集時，會使用加密憑證將加密文字解密。 Invoke-ServiceFabricEncryptText Cmdlet 會用來建立密碼的加密文字，其已新增至 ApplicationManifest.xml 檔案。
有關證書和加密語義的更多資訊，請參閱[金鑰管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>設定整個叢集的認證

Service Fabric 允許您配置群集範圍的憑據，這些憑據可以由應用程式用作預設存儲庫憑據。

`UseDefaultRepositoryCredentials`此功能可以通過將屬性`ContainerHostPolicies`添加到 具有`true`或 值的 ApplicationManifest.xml 中來`false`啟用或禁用此功能。

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

然後，Service Fabric 使用預設存儲庫憑據，可在`Hosting`"群集清單"中分區下指定。  如果 `UseDefaultRepositoryCredentials` 是 `true`，Service Fabric 會從 ClusterManifest 中讀取下列值：

* DefaultContainerRepositoryAccountName (字串)
* DefaultContainerRepositoryPassword (字串)
* IsDefaultContainerRepositoryPasswordEncrypted (布林值)
* 預設容器存儲庫密碼類型（字串）

下面是在群集清單範本.json 檔中`Hosting`可以添加的內容的示例。 可以在`Hosting`群集創建時或在配置升級中添加該部分。 如需詳細資訊，請參閱[變更 Azure Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)及[管理 Azure Service Fabric 應用程式祕密](service-fabric-application-secret-management.md)

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

## <a name="use-tokens-as-registry-credentials"></a>使用權杖作為註冊表憑據

Service Fabric 支援使用權杖作為憑據來下載容器的圖像。  此功能利用基礎虛擬機器規模集的*託管標識*對註冊表進行身份驗證，無需管理使用者憑據。  有關詳細資訊[，請參閱 Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)。  使用此功能需要以下步驟：

1. 確保為 VM 啟用*系統分配的託管標識*。

    ![Azure 門戶：創建虛擬機器縮放集標識選項](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. 向虛擬機器縮放集授予從註冊表中提取/讀取映射的許可權。 從 Azure 容器註冊表在 Azure 門戶中的存取控制 （IAM） 邊欄選項卡中，為虛擬機器添加*角色指派*：

    ![將 VM 主體添加到 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 接下來，修改應用程式清單。 在`ContainerHostPolicies`部分中，添加屬性`‘UseTokenAuthenticationCredentials=”true”`。

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
    > 設置為`UseDefaultRepositoryCredentials`true 時`UseTokenAuthenticationCredentials`的標誌為 true 將導致部署期間出錯。

## <a name="next-steps"></a>後續步驟

* 查看有關[容器註冊表身份驗證](../container-registry/container-registry-authentication.md)的更多。
