---
title: Service Fabric 的 Azure 檔案儲存體磁片區驅動程式
description: Service Fabric 支援使用 Azure 檔案服務以備份來自您容器的磁碟區。
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247688"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Service Fabric 的 Azure 檔案儲存體磁片區驅動程式

Azure 檔案儲存體磁片區驅動程式是[docker 磁片區外掛程式](https://docs.docker.com/engine/extend/plugins_volume/)，可針對 Docker 容器提供以[Azure 檔案儲存體](../storage/files/storage-files-introduction.md)為基礎的磁片區。 它封裝為 Service Fabric 應用程式，可部署至 Service Fabric 叢集，以提供叢集內其他 Service Fabric 容器應用程式的磁片區。

> [!NOTE]
> 已發行 Azure 檔案儲存體磁片區外掛程式的版本6.5.661.9590，以供正式運作。
>

## <a name="prerequisites"></a>必要條件
* Azure 檔案服務磁碟區外掛程式的 Windows 版本只能在 [Windows Server 1709 版](/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 1709 版](/windows/whats-new/whats-new-windows-10-version-1709)或更新版本的作業系統上運作。

* Azure 檔案服務磁碟區外掛程式的 Linux 版本可在 Service Fabric 所支援的所有作業系統版本上運作。

* Azure 檔案磁碟區外掛程式只適用於 Service Fabric 6.2 版及更新版本。

* 請依照 [Azure 檔案服務文件](../storage/files/storage-how-to-create-file-share.md) \(英文\) 中的指示，為 Service Fabric 容器應用程式建立一個檔案共用作為磁碟區使用。

* 您將會需要安裝[包含 Service Fabric 模組的 Powershell](./service-fabric-get-started.md) 或 [SFCTL](./service-fabric-cli.md)。

* 如果您使用 Hyper-v 容器，則必須在 Azure Resource Manager 範本中的 ClusterManifest (本機叢集) 或 fabricSettings 區段中新增下列程式碼片段， (Azure 叢集) 或 ClusterConfig.js獨立叢集 (上的) 。

在 ClusterManifest 中，必須在 Hosting 區段中新增下列內容。 在此範例中，磁片區名稱是**sfazurefile** ，而它在叢集上接聽的埠是**19100**。 請以您叢集的正確值來取代它們。

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

在 Azure Resource Manager 範本的 [fabricSettings] 區段中 (適用于 Azure 部署) 或 (上的 [獨立部署的 ClusterConfig.js]) ，必須新增下列程式碼片段。 同樣地，使用您自己的名稱和埠值來取代。

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

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>使用 Service Fabric Azure 檔案儲存體磁片區驅動程式部署範例應用程式

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>使用 Azure Resource Manager 透過提供的 Powershell 腳本 (建議) 

如果您的叢集是以 Azure 為基礎，建議您使用 Azure Resource Manager 應用程式資源模型將應用程式部署至該叢集以方便使用，並協助移至維護基礎結構即程式碼的模型。 這種方法不需要追蹤 Azure 檔案儲存體磁片區驅動程式的應用程式版本。 它也可讓您為每個支援的 OS 維護個別的 Azure Resource Manager 範本。 腳本會假設您要部署最新版本的 Azure 檔案儲存體應用程式，並採用 OS 類型、叢集訂用帳戶識別碼和資源群組的參數。 您可以從[Service Fabric 下載網站](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)下載腳本。 請注意，這會自動設定 ListenPort，這是 Azure 檔案儲存體磁片區外掛程式接聽來自 Docker daemon 的要求到19100的埠。 您可以新增名為 "listenPort" 的參數來變更它。 請確定埠不會與叢集或應用程式所使用的任何其他埠衝突。
 

適用于 Windows 的 Azure Resource Manager 部署命令：
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

適用于 Linux 的 Azure Resource Manager 部署命令：
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

成功執行腳本後，您可以跳到設定[您的應用程式一節。](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>獨立叢集的手動部署

提供容器磁片區的 Service Fabric 應用程式可以從[Service Fabric 下載網站](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)下載。 您可以透過 [PowerShell](./service-fabric-deploy-remove-applications.md)、[CLI](./service-fabric-application-lifecycle-sfctl.md) 或 [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md) 將應用程式部署到叢集。

1. 使用命令列，將目錄變更為所下載應用程式套件的根目錄。

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 接下來，使用 [ApplicationPackagePath] 和 [ImageStoreConnectionString] 的適當值，將應用程式套件複製到映射存放區：

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

4. 建立應用程式，並特別注意**ListenPort**應用程式參數值。 此值是 Azure 檔案儲存體磁片區外掛程式接聽來自 Docker daemon 之要求的埠。 請確定提供給應用程式的埠符合 ClusterManifest 中的 VolumePluginPorts，而且不會與叢集或應用程式所使用的任何其他埠衝突。

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
遵循上述的步驟 1-3 [。](#manual-deployment-for-standalone-clusters)

 Azure 檔案服務磁碟區外掛程式應用程式的預設服務執行個體計數為 -1，這表示針對叢集中的每個節點都有部署一個服務的執行個體。 但是，將 Azure 檔案服務磁碟區外掛程式應用程式部署到本機開發叢集上時，服務執行個體計數應指定為 1。 這可以透過 **InstanceCount** 應用程式參數來完成。 因此，在本機開發叢集上建立 Azure 檔案儲存體磁片區外掛程式應用程式的命令為：

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>設定您的應用程式以使用磁碟區
下列程式碼片段顯示如何在應用程式的應用程式資訊清單檔中指定以 Azure 檔案儲存體為基礎的磁片區。 需要關注的特定元素為 **Volume** 標記：

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

Azure 檔案儲存體磁片區外掛程式的驅動程式名稱是**sfazurefile**。 此值是針對應用程式資訊清單中的**Volume** tag 元素的**Driver**屬性所設定。

在上述程式碼片段中的**磁片**區標記中，Azure 檔案儲存體磁片區外掛程式需要下列屬性：
- **Source**：這是磁碟區的名稱。 使用者可以為他們的磁碟區選擇任何名稱。
- **目的地**-此屬性是磁片區在執行中容器內所對應的位置。 因此，您的目的地不能是容器內的現有位置

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
Service Fabric 也允許使用您自己的自訂[磁碟區](https://docs.docker.com/engine/extend/plugins_volume/)或[記錄](https://docs.docker.com/engine/admin/logging/overview/)驅動程式。 如果叢集上未安裝 Docker 磁碟區/記錄驅動程式，可以使用 RDP/SSH 通訊協定來手動安裝它。 您可以透過[虛擬機器擴展集啟動指令碼](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 指令碼](./service-fabric-application-model.md)，使用這些通訊協定執行安裝。

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
* 若要將容器部署到 Service Fabric 叢集，請參閱[在 Service Fabric 上部署容器](./service-fabric-get-started-containers.md)一文
