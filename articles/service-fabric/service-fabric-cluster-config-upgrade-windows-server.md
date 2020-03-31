---
title: 升級獨立叢集的組態
description: 了解如何升級可執行獨立 Service Fabric 叢集的組態。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610112"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>升級獨立叢集的組態 

對於現代化系統來說，升級能力攸關產品是否能長期成功。 Azure Service Fabric 叢集是您擁有的資源。 本文說明如何升級獨立 Service Fabric 叢集的組態設定。

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>在 ClusterConfig.json 檔案中自訂叢集設定
獨立群集通過*ClusterConfig.json*檔進行配置。 若要深入了解不同的設定，請參閱[獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)。

您可以在 *"群集 Config.json"* 中的`fabricSettings`["群集屬性](./service-fabric-cluster-manifest.md#cluster-properties)"部分下添加、更新或刪除設置。 

例如，下列 JSON 會將新設定 *MaxDiskQuotaInMB* 新增至 `fabricSettings` 底下的 [Diagnostics]** 區段：

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

在您修改 ClusterConfig.json 檔案中的設定之後，請[測試叢集組態](#test-the-cluster-configuration)，然後[升級叢集組態](#upgrade-the-cluster-configuration)以將設定套用至叢集。 

## <a name="test-the-cluster-configuration"></a>測試叢集組態
起始組態升級之前，您可以執行獨立套件中的 PowerShell 指令碼來對新叢集組態 JSON 進行測試：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

或是使用此指令碼：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

某些配置無法升級，例如端點、群集名稱、節點 IP 等。新的群集配置 JSON 針對舊群集配置 JSON 進行測試，並在 PowerShell 視窗中引發錯誤（如果有問題）。

## <a name="upgrade-the-cluster-configuration"></a>升級叢集組態
要升級群集配置升級，運行[啟動-服務Fabric集群配置升級](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)。 組態升級是按升級網域逐一處理。

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>升級叢集憑證組態
叢集憑證可用於叢集節點之間的驗證。 在進行憑證變換時請格外小心，因為若發生失敗，將會讓叢集節點之間無法通訊。

支援的選項有四個：  

* 單一憑證升級：升級路徑為「憑證 A (主要) -> 憑證 B (主要) -> 憑證 C (主要) -> ...」。

* 雙重憑證升級：升級路徑為「憑證 A (主要) -> 憑證 A (主要) 和 B (次要) -> 憑證 B (主要) -> 憑證 B (主要) 和 C (次要) -> 憑證 C (主要) -> ...」。

* 憑證類型升級：指紋型憑證設定 <-> CommonName 型憑證設定。 例如，憑證指紋 A (主要) 和指紋 B (次要) -> 憑證 CommonName C。

* 憑證簽發者指紋升級：升級路徑為「憑證 CN=A,IssuerThumbprint=IT1 (主要) -> 憑證 CN=A,IssuerThumbprint=IT1,IT2 (主要) -> 憑證 CN=A,IssuerThumbprint=IT2 (主要)」。


## <a name="next-steps"></a>後續步驟
* 了解如何自訂一些 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* 瞭解如何[將群集擴展到和外。](service-fabric-cluster-scale-up-down.md)
* 了解[應用程式升級](service-fabric-application-upgrade.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
