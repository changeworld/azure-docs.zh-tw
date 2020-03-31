---
title: 服務結構的 Azure 檔卷驅動程式
description: Service Fabric 支援使用 Azure 檔案服務以備份來自您容器的磁碟區。
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750052"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>服務結構的 Azure 檔卷驅動程式

Azure 檔卷驅動程式是[Docker 卷外掛程式](https://docs.docker.com/engine/extend/plugins_volume/)，它為 Docker 容器提供基於[Azure 檔的](/azure/storage/files/storage-files-introduction)卷。 它打包為 Service Fabric 應用程式，可以部署到 Service Fabric 群集，為群集中的其他 Service Fabric 容器應用程式提供卷。

> [!NOTE]
> Azure 檔卷外掛程式的版本 6.5.661.9590 已發佈，以便獲得一般版本。
>

## <a name="prerequisites"></a>Prerequisites
* Azure 檔案服務磁碟區外掛程式的 Windows 版本只能在 [Windows Server 1709 版](/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 1709 版](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709)或更新版本的作業系統上運作。

* Azure 檔案服務磁碟區外掛程式的 Linux 版本可在 Service Fabric 所支援的所有作業系統版本上運作。

* Azure 檔案磁碟區外掛程式只適用於 Service Fabric 6.2 版及更新版本。

* 請依照 [Azure 檔案服務文件](/azure/storage/files/storage-how-to-create-file-share) \(英文\) 中的指示，為 Service Fabric 容器應用程式建立一個檔案共用作為磁碟區使用。

* 您將會需要安裝[包含 Service Fabric 模組的 Powershell](/azure/service-fabric/service-fabric-get-started) 或 [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)。

* 如果使用 Hyper-V 容器，則需要在 Azure 資源管理器範本（Azure 群集）或 ClusterConfig.json（獨立群集）中的群集清單（本地群集）或結構設置部分中添加以下程式碼片段。

在 ClusterManifest 中，必須在 Hosting 區段中新增下列內容。 在此示例中，卷名稱為**sfazurefile，** 它在群集上偵聽的埠為**19100**。 用群集的正確值替換它們。

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

在 Azure 資源管理器範本（用於 Azure 部署）或 ClusterConfig.json（用於獨立部署）中的"結構設置"部分中，需要添加以下程式碼片段。 同樣，將卷名稱和埠值替換為您自己的。

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>使用服務結構 Azure 檔卷驅動程式部署應用程式範例

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>通過提供的 Powershell 腳本使用 Azure 資源管理器（推薦）

如果群集位於 Azure 中，我們建議使用 Azure 資源管理器應用程式資源管理器應用程式資源模型將應用程式部署到該群集，以便方便使用，並説明向將基礎結構維護為代碼的模式邁進。 此方法無需跟蹤 Azure 檔卷驅動程式的應用版本。 它還使您能夠為每個受支援的作業系統維護單獨的 Azure 資源管理器範本。 該腳本假定您正在部署最新版本的 Azure 檔應用程式，並獲取作業系統類型、群集訂閱 ID 和資源組的參數。 你可以從[服務交換矩陣下載網站](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)下載腳本。 請注意，這會自動將 ListenPort（Azure 檔卷外掛程式偵聽來自 Docker 守護進程的請求的埠）設置為 19100。 您可以通過添加名為"偵聽埠"的參數來更改它。 確保埠不與群集或應用程式使用的任何其他埠衝突。
 

Azure 資源管理器部署命令的 Windows：
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure 資源管理器部署命令的 Linux：
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

成功運行腳本後，可以跳到[配置應用程式部分。](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>獨立群集的手動部署

提供容器卷的服務交換矩陣應用程式可以從[服務交換矩陣下載網站](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)下載。 您可以透過 [PowerShell](./service-fabric-deploy-remove-applications.md)、[CLI](./service-fabric-application-lifecycle-sfctl.md) 或 [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md) 將應用程式部署到叢集。

1. 使用命令列，將目錄更改為下載的應用程式包的根目錄。

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 接下來，將應用程式包複製到映射存儲，並具有 [應用程式包路徑] 和 [ImageStoreConnectionString] 的適當值：

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. 註冊應用程式類型

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. 創建應用程式，密切關注**偵聽埠**應用程式參數值。 此值是 Azure 檔卷外掛程式偵聽來自 Docker 守護進程的請求的埠。 確保提供給應用程式的埠與群集清單中的 VolumePluginPorts 匹配，並且不與群集或應用程式使用的任何其他埠衝突。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter 不支援將 SMB 掛接對應至容器 ([該功能僅在 Windows Server 1709 版上受到支援](/virtualization/windowscontainers/manage-containers/container-storage))。 這條件約束可避免在早於 1709 的版本上使用網路磁碟區對應和 Azure 檔案服務磁碟區驅動程式。

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>在本機開發叢集上部署應用程式
按照上述步驟 1-3 操作[。](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Azure 檔案服務磁碟區外掛程式應用程式的預設服務執行個體計數為 -1，這表示針對叢集中的每個節點都有部署一個服務的執行個體。 但是，將 Azure 檔案服務磁碟區外掛程式應用程式部署到本機開發叢集上時，服務執行個體計數應指定為 1。 這可以透過 **InstanceCount** 應用程式參數來完成。 因此，在本地開發群集上創建 Azure 檔卷外掛程式應用程式的命令是：

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>設定您的應用程式以使用磁碟區
以下程式碼片段顯示了如何在應用程式的應用程式清單檔中指定基於 Azure 檔的卷。 需要關注的特定元素為 **Volume** 標記：

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure 檔卷外掛程式的驅動程式名稱是**sfazure 檔**。 此值為應用程式清單中的 **"卷**"標記元素的**Driver**屬性設置。

在上面的片段中的 **"卷"** 標記中，Azure 檔卷外掛程式需要以下屬性：
- **Source**：這是磁碟區的名稱。 使用者可以為他們的磁碟區選擇任何名稱。
- **目標**- 此屬性是卷映射到正在運行的容器中的位置。 因此，您的目的地不能是容器內的現有位置

如上述程式碼片段的 **DriverOption** 元素中所示，Azure 檔案服務磁碟區外掛程式支援下列驅動程式選項：
- **shareName**：為容器提供磁碟區之 Azure 檔案服務檔案共用的名稱。
- **storageAccountName**：包含 Azure 檔案服務檔案共用之 Azure 儲存體帳戶的名稱。
- **storageAccountKey**：包含 Azure 檔案服務檔案共用之 Azure 儲存體帳戶的存取金鑰。
- **storageAccountFQDN** - 與儲存體帳戶相關聯的網域名稱。 如果未指定 storageAccountFQDN，網域名稱會使用預設的尾碼 (.file.core.windows.net) 和 storageAccountName 來組成。  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>使用您自己的磁碟區或記錄驅動程式
Service Fabric 也允許使用您自己的自訂[磁碟區](https://docs.docker.com/engine/extend/plugins_volume/)或[記錄](https://docs.docker.com/engine/admin/logging/overview/)驅動程式。 如果叢集上未安裝 Docker 磁碟區/記錄驅動程式，可以使用 RDP/SSH 通訊協定來手動安裝它。 您可以透過[虛擬機器擴展集啟動指令碼](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 指令碼](/azure/service-fabric/service-fabric-application-model)，使用這些通訊協定執行安裝。

安裝[適用於 Azure 的 Docker 磁碟區驅動程式](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)的指令碼範例如下所示：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

在您的應用程式中，若要使用自己安裝的磁碟區或記錄驅動程式，您必須在應用程式資訊清單 **ContainerHostPolicies** 底下的 **Volume** 和 **LogConfig** 元素中指定適當的值。

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

指定磁碟區外掛程式時，Service Fabric 會使用指定的參數自動建立磁碟區。 **[磁碟區]** 元素的 **[來源]** 標記是磁碟區的名稱，而 **[驅動程式]** 標記會指定磁碟區驅動程式的外掛程式。 [目的地]**** 標記是 [來源]**** 在執行容器內所對應的位置。 因此，您的目的地不能是您容器內的現有位置。 您可以使用 [DriverOption]**** 標記來指定選項，如下所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

先前資訊清單程式碼片段中所示的磁碟區支援應用程式參數 (尋找 `MyStorageVar` 以取得使用範例)。

如果指定了 Docker 記錄驅動程式，您就必須在叢集中部署代理程式 (或容器) 來處理記錄。 [DriverOption]**** 標記可以用來指定記錄驅動程式的選項。

## <a name="next-steps"></a>後續步驟
* 若要查看容器範例 (包括磁碟區驅動程式)，請瀏覽 [Service Fabric 容器範例](https://github.com/Azure-Samples/service-fabric-containers) \(英文\)
* 若要將容器部署到 Service Fabric 叢集，請參閱[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)一文
