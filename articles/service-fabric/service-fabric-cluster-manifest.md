---
title: 配置 Azure 服務結構獨立群集
description: 了解如何設定獨立或內部部署 Service Fabric 叢集。
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458368"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>獨立 Windows 叢集的組態設定
本文說明如何在 ClusterConfig.json** 檔案中設定獨立 Azure Service Fabric 叢集的組態設定。 您將會使用此檔案來指定叢集節點、安全性設定，以及容錯和升級網域方面之網路拓撲的相關資訊。  變更或新增組態設定之後，您可以[建立獨立叢集](service-fabric-cluster-creation-for-windows-server.md)或[升級獨立叢集的組態](service-fabric-cluster-config-upgrade-windows-server.md)。

當您[下載獨立的 Service Fabric 套件](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)時，ClusterConfig.json 範例也會包含在內。 名稱中包含 "DevCluster" 的範例，會使用邏輯節點，建立三個節點皆位於相同電腦上的叢集。 在這三個節點中，至少必須將一個節點標示為主要節點。 此類型的叢集可用於開發或測試環境。 但不支援做為生產叢集。 名稱中包含 "MultiMachine" 的範例，能協助建立生產等級的叢集，其中每個節點都會位於不同的電腦上。 這些叢集的主要節點數目會以叢集的[可靠性層級](#reliability)為基礎。 在 5.7 版，API 版本 05-2017 中，我們移除了可靠性層級屬性。 但是，我們的程式碼會計算叢集的最佳化可靠性層級。 請不要嘗試在 5.7 版及更新版本中設定這個屬性的值。

* ClusterConfig.Unsecure.DevCluster.json 和 ClusterConfig.Unsecure.MultiMachine.json 分別示範如何建立不安全的測試或生產叢集。

* 群集 Config.Windows.DevCluster.json 和群集 Config.Windows.MultiMachine.json 演示如何使用[Windows 安全性](service-fabric-windows-cluster-windows-security.md)創建受保護的測試或生產群集。

* ClusterConfig.X509.DevCluster.json 和 ClusterConfig.X509.MultiMachine.json 演示如何使用[基於 X509 證書的安全性](service-fabric-windows-cluster-x509-security.md)創建保護的測試或生產群集。

現在，讓我們檢視 ClusterConfig.json 檔案的各個區段。

## <a name="general-cluster-configurations"></a>一般叢集組態
一般叢集組態會涵蓋廣泛的叢集特有組態，如下列 JSON 程式碼片段所示：

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

若要為 Service Fabric 叢集提供任何易記名稱，您可以將它指派給 name 變數。 ClusterConfigurationVersion 是叢集的版本號碼。 請在每次升級 Service Fabric 叢集時上調此號碼。 讓 apiVersion 繼續設定為預設值。

## <a name="nodes-on-the-cluster"></a>叢集上的節點
您可以使用 nodes 區段，在 Service Fabric 叢集上設定節點，如下列程式碼片段所示：
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Service Fabric 叢集至少必須包含 3 個節點。 您可以根據您的設定，在此區段中新增更多節點。 下表說明每個節點的組態設定：

| **節點配置** | **描述** |
| --- | --- |
| nodeName |您可以為節點提供易記名稱。 |
| iPAddress |開啟命令視窗並輸入 `ipconfig`，以找出節點的 IP 位址。 記下 IPV4 位址，並將它指派給 iPAddress 變數。 |
| nodeTypeRef |每個節點都可以指派不同的節點類型。 [節點類型](#node-types)會於下一節中定義。 |
| faultDomain |容錯網域可讓叢集管理員定義實體節點，這類節點可能會在相同時間因為共用實體的相依性 (例如電源和網路來源) 而發生錯誤。 |
| upgradeDomain |升級網域說明大約會在相同時間關閉以進行 Service Fabric 升級的節點集。 因為它們不會受到任何實體需求所限制，您可以選擇要將哪些節點指派給哪些升級網域。 |

## <a name="cluster-properties"></a>叢集屬性
ClusterConfig.json 中的 [屬性] 區段是用來設定叢集，如下所示：

### <a name="reliability"></a>可靠性
reliabilityLevel 的概念會定義可以在叢集主要節點上執行之 Service Fabric 系統服務的複本或執行個體數目。 它會決定這些服務以及叢集的可靠性。 其值會由系統在建立和升級叢集時計算。

### <a name="diagnostics"></a>診斷
在 diagnosticsStore 區段中，您可以設定參數，以啟用診斷以及疑難排解節點或叢集的失敗，如下列程式碼片段所示： 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

metadata 是叢集診斷的說明，而且可根據您的設定來進行設定。 這些變數有助於收集 ETW 追蹤記錄、損毀傾印，以及效能計數器。 如需 ETW 追蹤記錄的詳細資訊，請參閱 [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) 和 [ETW 追蹤](https://msdn.microsoft.com/library/ms751538.aspx)。 包含[損毀傾印](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/)和[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)的所有記錄可導向至電腦上的 connectionString 資料夾。 您也可以使用 AzureStorage 來儲存診斷。 請參閱下列程式碼片段範例：

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>安全性
security 區段對於安全獨立的 Service Fabric 叢集是必要的項目。 下列程式碼片段示範此區段的一部分：

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

metadata 是安全叢集的說明，而且可根據您的設定來進行設定。 ClusterCredentialType 和 ServerCredentialType 決定叢集和節點會實作的安全性類型。 對於基於證書的安全性，可以將它們設置為*X509，* 也可以設置為基於證書的安全性的 Windows，也可以設置為基於活動目錄的安全性的*Windows。* 其餘的 security 區段則是根據安全性類型。 如需如何填滿其餘 security 區段的相關資訊，請參閱[獨立叢集中的憑證式安全性](service-fabric-windows-cluster-x509-security.md)或[獨立叢集中的 Windows 安全性](service-fabric-windows-cluster-windows-security.md)。

### <a name="node-types"></a>節點類型
nodeTypes 區段說明叢集所擁有的節點類型。 至少必須針對叢集指定一個節點類型，如下列程式碼片段所示： 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

name 是此特定節點類型的易記名稱。 若要建立此節點類型的節點，請將其易記名稱指派給該節點的 nodeTypeRef 變數，如[先前所述](#nodes-on-the-cluster)。 為每個節點類型定義將會使用的連接端點。 您可以為這些連接端點選擇任意的連接埠號碼，只要它們不會與此叢集中的任何其他端點發生衝突即可。 視 [reliabilityLevel](#reliability) 而定，多節點叢集中會有一或多個主要節點 (也就是 isPrimary 設為 true**)。 如需 nodeTypes 和 reliabilityLevel 的詳細資訊，以及為了了解主要和非主要節點類型，請參閱 [Service Fabric 叢集容量規劃考量](service-fabric-cluster-capacity.md)。 

#### <a name="endpoints-used-to-configure-the-node-types"></a>用來設定節點類型的端點
* clientConnectionEndpointPort 是在使用用戶端 API 時，用戶端用來連線到叢集的連接埠。 
* clusterConnectionEndpointPort 是節點用來彼此通訊的連接埠。
* leaseDriverEndpointPort 是叢集租用驅動程式用來了解節點是否仍在作用中的連接埠。 
* serviceConnectionEndpointPort 是節點上部署的應用程式和服務用來與該特定節點的 Service Fabric 用戶端通訊的連接埠。
* httpGatewayEndpointPort 是 Service Fabric Explorer 用來連線到叢集的連接埠。
* ephemeralPorts 會覆寫 [OS 所使用的動態連接埠](https://support.microsoft.com/kb/929851)。 Service Fabric 會使用一部分連接埠做為應用程式連接埠，其餘連接埠則可供 OS 使用。 它也會將此範圍對應至 OS 中存在的現有範圍，因此不論用途為何，您都可以使用範例 JSON 檔案中給定的範圍。 請確保頭尾連接埠之間相差至少 255。 如果這項差異太低，您可能會遇到衝突，因為這個範圍會與 OS 共用。 若要查看所設定的動態連接埠範圍，請執行 `netsh int ipv4 show dynamicport tcp`。
* applicationPorts 是 Service Fabric 應用程式所使用的連接埠。 應用程式連接埠範圍應該足以涵蓋應用程式的端點需求。 此範圍應該排除於電腦上的動態連接埠範圍 (也就是在組態中設定的 ephemeralPorts 範圍) 之外。 Service Fabric 會在每當需要新連接埠時使用這些連接埠，並負責在防火牆上開啟這些連接埠。 
* reverseProxyEndpointPort 是選擇性的反向 Proxy 端點。 如需詳細資訊，請參閱 [Service Fabric 反向 Proxy](service-fabric-reverseproxy.md)。 

### <a name="log-settings"></a>記錄設定
在 fabricSettings 區段中，您可以設定 Service Fabric 資料和記錄的根目錄。 您只能在初始叢集建立期間自訂這些目錄。 請參閱此區段的下列程式碼片段範例：

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

建議您使用非作業系統磁碟機做為 FabricDataRoot 和 FabricLogRoot。 這類磁碟機比較不會讓作業系統停止回應。 如果您只自訂資料根目錄，則記錄根目錄會以資料根目錄的下一個層級來取代。

### <a name="stateful-reliable-services-settings"></a>具狀態可靠服務設定
在 KtlLogger 區段中，您可以設定 Reliable Services 的全域組態設定。 如需這些設定的詳細資訊，請參閱[設定具狀態可靠服務](service-fabric-reliable-services-configuration.md)。 下列範例示範如何變更所建立的共用交易記錄，以備份具狀態服務的任何可靠集合：

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>附加元件功能
若要設定附加功能，請將 apiVersion 設定為 04-2017 或更高版本，並將 addonFeatures 設定如下：

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
所有可用的附加功能都可以在[服務結構 REST API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures)中看到。

### <a name="container-support"></a>容器支援
若要啟用獨立叢集的 Windows Server 容器和 Hyper-V 容器的容器支援，必須啟用 DnsService 附加功能。

## <a name="next-steps"></a>後續步驟
根據獨立群集設置配置完整的*ClusterConfig.json*檔後，可以部署群集。 請遵循[建立獨立 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)中的步驟來進行。 

如果您已部署獨立叢集，您也可以[升級獨立叢集的組態](service-fabric-cluster-config-upgrade-windows-server.md)。 

了解如何[使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)。

