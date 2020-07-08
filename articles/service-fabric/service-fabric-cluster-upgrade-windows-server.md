---
title: 升級獨立叢集的版本
description: 升級執行獨立 Service Fabric 叢集的 Azure Service Fabric 程式碼。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 5921fc9038e53f34e23f6fd97111c71b29699dc5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793137"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>升級在您叢集上執行之 Service Fabric 的版本 

對於現代化系統來說，升級能力攸關產品是否能長期成功。 Azure Service Fabric 叢集是您擁有的資源。 此文章說明如何將在您獨立叢集上執行的 Service Fabric 版本升級。

> [!NOTE]
> 請確定您的叢集一律執行支援的 Service Fabric 版本。 當 Microsoft 宣布發行新版本的 Service Fabric 時，從宣布當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。 那時就有新的版本可選擇。
>
>

只有當您使用生產樣式節點組態，其中每個 Service Fabric 節點是配置在不同實體或虛擬機器時，才能將叢集升級到新的版本。 如果您擁有的開發叢集在單一的實體或虛擬機器上有多個 Service Fabric 節點，您必須以新版本重新建立叢集。

兩個不同的工作流程可以將您的叢集升級至最新版本或支援的 Service Fabric 版本。 一個工作流程適用於具備連線能力而可自動下載最新版本的叢集。 另一個工作流程則適用於不具備連線能力因而無法下載最新 Service Fabric 版本的叢集。

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>啟用自動升級叢集 Service Fabric 版本的功能
若要設定叢集，讓其在 Microsoft 發行新版本時下載 Service Fabric 更新，請將 `fabricClusterAutoupgradeEnabled` 叢集組態設定為 *true*。 若要手動選取要讓叢集執行的受支援 Service Fabric 版本，請將 `fabricClusterAutoupgradeEnabled` 叢集組態設定為 *false*。

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>升級具有連線能力而可下載最新程式碼和組態的叢集
如果您的叢集節點與 [Microsoft 下載中心](https://download.microsoft.com)的網際網路連線，則可以使用這些步驟將您的叢集升級至支援的版本。

對於可以連線至 [Microsoft 下載中心](https://download.microsoft.com)的叢集，Microsoft 會定期檢查新版本 Service Fabric 的可用性。

當新的 Service Fabric 版本可用時，系統會將套件下載至本機叢集並佈建以進行升級。 除了通知客戶這個新版本之外，系統會顯示明確的叢集健全狀況警告，如下所示：

「現行的叢集版本 [version#] 將於 [date] 結束支援。」

叢集執行最新版本後，警告就會消失。

當您看到叢集健康情況警告時，請升級叢集︰

1. 從具有系統管理員存取權的任何電腦，將叢集連接至列為叢集中節點的所有電腦。 執行此指令碼所在的電腦不一定是叢集的一部分。

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 取得您可以升級的 Service Fabric 版本清單。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    您應該會看到如下的輸出：

    ![取得 Service Fabric 版本][getfabversions]
3. 使用 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell 命令開始將叢集升級為可用版本。

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   若要監視升級的進度，您可以使用 Service Fabric Explorer 或執行下列 PowerShell 命令：

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    如果不符合叢集健康狀態原則，則會回復升級。 若要為 Start-ServiceFabricClusterUpgrade 命令指定自訂的健康狀態原則，請參閱 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) 的文件。

    在解決導致復原的問題後，請依照先前所述的相同步驟再次起始升級。

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>升級*沒有連線能力*因而無法下載最新程式碼和組態的叢集
如果您的叢集節點未與 [Microsoft 下載中心](https://download.microsoft.com)的網際網路連線，則可以使用這些步驟將您的叢集升級至支援的版本。

> [!NOTE]
> 如果您正在執行未連線至網際網路的叢集，您必須關注 [Service Fabric 小組部落格](https://blogs.msdn.microsoft.com/azureservicefabric/) \(英文\)，以便得知新版本的消息。 系統不會顯示叢集健康狀態警告來提醒您有新版本。  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>自動佈建與手動佈建
若要啟用自動下載與註冊最新版程式碼的功能，請設定 Service Fabric 更新服務。 如需指示，請參閱[獨立套件](service-fabric-cluster-standalone-package-contents.md)中的 *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt*。

如需進行手動程序，請遵循以下指示。

請修改叢集組態以將下列屬性設定為 false，再開始進行組態升級：

```json
"fabricClusterAutoupgradeEnabled": false,
```

如需使用方式的詳細資料，請參閱 [Start-ServiceFabricClusterConfigurationUpgrade PowerShell command](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)。 在您開始組態升級之前，請確實更新 JSON 中的 'clusterConfigurationVersion'。

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>叢集升級工作流程

1. 從叢集中的其中一個節點執行 [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade)，並記下 *TargetCodeVersion*。

2. 從與網際網路連線的電腦執行下列程式碼，以根據目前版本列出所有升級相容版本 ，並從相關聯的下載連結下載對應封裝：

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. 從具有系統管理員存取權的任何電腦，將叢集連接至列為叢集中節點的所有電腦。 執行此指令碼所在的電腦不一定是叢集的一部分。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. 將下載的套件複製到叢集映像存放區。

5. 註冊所複製的套件。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. 開始將叢集升級為可用版本。

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    您可以在 Service Fabric Explorer 上或執行下列 PowerShell 命令來監視升級進度：

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    如果不符合叢集健康狀態原則，則會回復升級。 若要為 Start-ServiceFabricClusterUpgrade 命令指定自訂的健康狀態原則，請參閱 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) 的文件。

    在解決導致復原的問題後，請依照先前所述的相同步驟再次起始升級。

## <a name="next-steps"></a>後續步驟
* [升級獨立叢集的組態](service-fabric-cluster-config-upgrade-windows-server.md)
* 自訂部分 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* [相應放大和相應縮小叢集](service-fabric-cluster-scale-in-out.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
