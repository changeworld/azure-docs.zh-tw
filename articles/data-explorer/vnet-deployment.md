---
title: 將 Azure 資料總管部署至您的虛擬網路（預覽）
description: 瞭解如何將 Azure 資料總管部署至您的虛擬網路
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e845b44c51b7611cd3f23f8b33e6576aced2d6ca
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851461"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>將 Azure 資料總管部署至您的虛擬網路（預覽）

本文說明當您將 Azure 資料總管叢集部署至自訂 Azure 虛擬網路時，所存在的資源。 這項資訊可協助您將叢集部署到虛擬網路（VNet）中的子網。 如需 Azure 虛擬網路的詳細資訊，請參閱[什麼是 azure 虛擬網路？](/azure/virtual-network/virtual-networks-overview)

   ![vnet 圖表](media/vnet-deployment/vnet-diagram.png)

Azure 資料總管支援將叢集部署至您虛擬網路（VNet）中的子網。 這項功能可讓您：

* 強制執行 Azure 資料總管叢集流量的[網路安全性群組](/azure/virtual-network/security-overview)（NSG）規則。
* 將內部部署網路連線到 Azure 資料總管叢集的子網。
* 使用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)來保護您的資料連線來源（[事件中樞](/azure/event-hubs/event-hubs-about)和[事件方格](/azure/event-grid/overview)）。

> [!NOTE]
> 虛擬網路整合和部署處於預覽模式。 若要啟用這項功能，請開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>在您的 VNet 中存取您的 Azure 資料總管叢集

您可以使用每個服務（引擎和資料管理服務）的下列 IP 位址來存取 Azure 資料總管叢集：

* **私人 IP**：用來存取 VNet 內的叢集。
* **公用 IP**：用來從 VNet 外部存取叢集以進行管理和監視，以及做為從叢集啟動之輸出連線的來源位址。

系統會建立下列 DNS 記錄來存取服務： 

* `[clustername].[geo-region].kusto.windows.net` （引擎） `ingest-[clustername].[geo-region].kusto.windows.net` （資料管理）會對應到每個服務的公用 IP。 

* `private-[clustername].[geo-region].kusto.windows.net` （引擎） `private-ingest-[clustername].[geo-region].kusto.windows.net` （資料管理）會對應到每個服務的私人 IP。

## <a name="plan-subnet-size-in-your-vnet"></a>規劃 VNet 中的子網大小

部署子網之後，就無法改變用來裝載 Azure 資料總管叢集的子網大小。 在您的 VNet 中，Azure 資料總管會針對每個 VM 使用一個私人 IP 位址，並為內部負載平衡器（引擎和資料管理）使用兩個私人 IP 位址。 Azure 網路也會針對每個子網使用五個 IP 位址。 Azure 資料總管會布建兩個適用于資料管理服務的 Vm。 系統會根據使用者設定調整容量來布建引擎服務 Vm。

IP 位址總數：

| 用法 | 位址數目 |
| --- | --- |
| 引擎服務 | 每個實例1個 |
| 資料管理服務 | 2 |
| 內部負載平衡器 | 2 |
| Azure 保留的位址 | 5 |
| **總計** | **#engine_instances + 9** |

> [!IMPORTANT]
> 必須事先規劃子網大小，因為在部署 Azure 資料總管之後，就無法變更它。 因此，請據以保留所需的子網大小。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>用來連接到 Azure 資料總管的服務端點

[Azure 服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)可讓您將 azure 多租使用者資源保護到您的虛擬網路。
將 Azure 資料總管叢集部署至您的子網，可讓您在限制 Azure 資料總管子網的基礎資源時，使用[事件中樞](/azure/event-hubs/event-hubs-about)或[事件方格](/azure/event-grid/overview)來設定資料連線。

> [!NOTE]
> 搭配使用 EventGrid 安裝與[儲存體](/azure/storage/common/storage-introduction)和 [事件中樞] 時，訂用帳戶中所使用的儲存體帳戶可以透過服務端點鎖定至 Azure 資料總管的子網，同時允許[防火牆](/azure/storage/common/storage-network-security)設定中的受信任 azure 平臺服務，但事件中樞無法啟用服務端點，因為它不支援受信任的[azure 平臺服務](/azure/event-hubs/event-hubs-service-endpoints)。

## <a name="dependencies-for-vnet-deployment"></a>VNet 部署的相依性

### <a name="network-security-groups-configuration"></a>網路安全性群組設定

[網路安全性群組（NSG）](/azure/virtual-network/security-overview)提供控制 VNet 內網路存取的能力。 您可以使用兩個端點來存取 Azure 資料總管： HTTPs （443）和 TDS （1433）。 下列 NSG 規則必須設定為允許存取這些端點，以進行叢集的管理、監視和適當操作。

#### <a name="inbound-nsg-configuration"></a>輸入 NSG 設定

| **使用**   | **From**   | **若要**   | **通訊協定**   |
| --- | --- | --- | --- |
| 管理  |[ADX 管理位址](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement （ServiceTag） | ADX 子網：443  | TCP  |
| 健康情況監視  | [ADX 健全狀況監視位址](#health-monitoring-addresses)  | ADX 子網：443  | TCP  |
| ADX 內部通訊  | ADX 子網：所有埠  | ADX 子網：所有埠  | 全部  |
| 允許 Azure 負載平衡器輸入（健康情況探查）  | AzureLoadBalancer  | ADX 子網：80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>輸出 NSG 設定

| **使用**   | **From**   | **若要**   | **通訊協定**   |
| --- | --- | --- | --- |
| Azure 儲存體的相依性  | ADX 子網  | 儲存體：443  | TCP  |
| Azure Data Lake 的相依性  | ADX 子網  | AzureDataLake：443  | TCP  |
| EventHub 內嵌和服務監視  | ADX 子網  | EventHub：443、5671  | TCP  |
| 發行計量  | ADX 子網  | AzureMonitor：443 | TCP  |
| Azure 監視器設定下載  | ADX 子網  | [Azure 監視器設定端點位址](#azure-monitor-configuration-endpoint-addresses)：443 | TCP  |
| Active Directory （如果適用） | ADX 子網 | AzureActiveDirectory：443 | TCP |
| 憑證授權單位 | ADX 子網 | 網際網路：80 | TCP |
| 內部通訊  | ADX 子網  | ADX 子網：所有埠  | 全部  |
| `sql\_request` 和 `http\_request` 外掛程式所使用的埠  | ADX 子網  | 網際網路：自訂  | TCP  |

### <a name="relevant-ip-addresses"></a>相關的 IP 位址

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 資料總管管理 IP 位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 20.37.26.134 |
| 澳大利亞 Central2 | 20.39.99.177 |
| 澳大利亞東部 | 40.82.217.84 |
| 澳大利亞東南部 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| 加拿大中部 | 40.82.188.208 |
| 加拿大東部 | 40.80.255.12 |
| 印度中部 | 40.81.249.251 |
| 美國中部 | 40.67.188.68 |
| 美國中部 EUAP | 40.89.56.69 |
| 東亞 | 20.189.74.103 |
| 美國東部 | 52.224.146.56 |
| 美國東部 2 | 52.232.230.201 |
| 東部美國 2 EUAP | 52.253.226.110 |
| 法國中部 | 40.66.57.91 |
| 法國南部 | 40.82.236.24 |
| 日本東部 | 20.43.89.90 |
| 日本西部 | 40.81.184.86 |
| 南韓中部 | 40.82.156.149 |
| 南韓南部 | 40.80.234.9 |
| 美國中北部 | 40.81.45.254 |
| 北歐 | 52.142.91.221 |
| 南非北部 | 102.133.129.138 |
| 南非西部 | 102.133.0.97 |
| 美國中南部 | 20.45.3.60 |
| 東南亞 | 40.119.203.252 |
| 印度南部 | 40.81.72.110 |
| 英國南部 | 40.81.154.254 |
| 英國西部 | 40.81.122.39 |
| 美國中西部 | 52.159.55.120 |
| 西歐 | 51.145.176.215 |
| 印度西部 | 40.81.88.112 |
| 美國西部 | 13.64.38.225 |
| 美國西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>健全狀況監視位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 191.239.64.128 |
| 澳大利亞中部 2 | 191.239.64.128 |
| 澳大利亞東部 | 191.239.64.128 |
| 澳大利亞東南部 | 191.239.160.47 |
| 巴西南部 | 23.98.145.105 |
| 加拿大中部 | 168.61.212.201 |
| 加拿大東部 | 168.61.212.201 |
| 印度中部 | 23.99.5.162 |
| 美國中部 | 168.61.212.201 |
| 美國中部 EUAP | 168.61.212.201 |
| 東亞 | 168.63.212.33 |
| 美國東部 | 137.116.81.189 |
| 美國東部 2 | 137.116.81.189 |
| 美國東部 2 EUAP | 137.116.81.189 |
| 法國中部 | 23.97.212.5 |
| 法國南部 | 23.97.212.5 |
| 日本東部 | 138.91.19.129 |
| 日本西部 | 138.91.19.129 |
| 南韓中部 | 138.91.19.129 |
| 南韓南部 | 138.91.19.129 |
| 美國中北部 | 23.96.212.108 |
| 北歐 | 191.235.212.69 
| 南非北部 | 104.211.224.189 |
| 南非西部 | 104.211.224.189 |
| 美國中南部 | 23.98.145.105 |
| 印度南部 | 23.99.5.162 |
| 東南亞 | 168.63.173.234 |
| 英國南部 | 23.97.212.5 |
| 英國西部 | 23.97.212.5 |
| 美國中西部 | 168.61.212.201 |
| 西歐 | 23.97.212.5 |
| 印度西部 | 23.99.5.162 |
| 美國西部 | 23.99.5.162 |
| 美國西部 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure 監視器設定端點位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 52.148.86.165 |
| 澳大利亞中部 2 | 52.148.86.165 |
| 澳大利亞東部 | 52.148.86.165 |
| 澳大利亞東南部 | 52.148.86.165 |
| 巴西南部 | 13.68.89.19 |
| 加拿大中部 | 13.90.43.231 |
| 加拿大東部 | 13.90.43.231 |
| 印度中部 | 13.71.25.187 |
| 美國中部 | 52.173.95.68 |
| 美國中部 EUAP | 13.90.43.231 |
| 東亞 | 13.75.117.221 |
| 美國東部 | 13.90.43.231 |
| 美國東部2 | 13.68.89.19 |    
| 美國東部 2 EUAP | 13.68.89.19 |
| 法國中部 | 52.174.4.112 |
| 法國南部 | 52.174.4.112 |
| 日本東部 | 13.75.117.221 |
| 日本西部 | 13.75.117.221 |
| 韓國中部 | 13.75.117.221 |
| 南韓南部 | 13.75.117.221 |
| 美國中北部 | 52.162.240.236 |
| 北歐 | 52.169.237.246 |
| 南非北部 | 13.71.25.187 |
| 南非西部 | 13.71.25.187 |
| 美國中南部 | 13.84.173.99 |
| 印度南部 | 13.71.25.187 |
| 東南亞 | 52.148.86.165 |
| 英國南部 | 52.174.4.112 |
| 英國西部 | 52.169.237.246 |
| 美國中西部 | 52.161.31.69 |
| 西歐 | 52.174.4.112 |
| 印度西部 | 13.71.25.187 |
| 美國西部 | 40.78.70.148 |
| 美國西部 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute 設定

使用 ExpressRoute 將內部部署網路連線到 Azure 虛擬網路。 常見的設定是透過邊界閘道協定（BGP）會話通告預設路由（0.0.0.0/0）。 這會強制將來自虛擬網路的流量轉送到客戶的內部網路，以捨棄流量，進而導致輸出流量中斷。 若要克服此預設值，可以設定[使用者定義的路由（UDR）](/azure/virtual-network/virtual-networks-udr-overview#user-defined) （0.0.0.0/0），而下一個躍點將是*網際網路*。 由於 UDR 的優先順序高於 BGP，因此流量會傳送到網際網路。

## <a name="securing-outbound-traffic-with-firewall"></a>使用防火牆保護輸出流量

如果您想要使用[Azure 防火牆](/azure/firewall/overview)或任何虛擬裝置來保護輸出流量，以限制功能變數名稱，防火牆中必須允許下列完整功能變數名稱（FQDN）。

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

您也需要在子網上定義[路由表](/azure/virtual-network/virtual-networks-udr-overview)，其中包含[管理位址](#azure-data-explorer-management-ip-addresses)和[健全狀況監視位址](#health-monitoring-addresses)與下一個躍點*網際網路*，以避免非對稱式路由問題。

例如，針對**美國西部**區域，必須定義下列 udr：

| 名稱 | 位址首碼 | 下一個躍點 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | 網際網路 |
| ADX_Monitoring | 23.99.5.162/32 | 網際網路 |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本將 Azure 資料總管叢集部署至您的 VNet

若要將 Azure 資料總管叢集部署至您的虛擬網路，請使用將[azure 資料總管叢集部署至您的 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager 範本。

此範本會建立叢集、虛擬網路、子網、網路安全性群組和公用 IP 位址。

## <a name="troubleshooting"></a>疑難排解

在本節中，您將瞭解如何針對部署到[虛擬網路](/azure/virtual-network/virtual-networks-overview)的叢集，針對連線、操作和叢集建立問題進行疑難排解。

### <a name="access-issues"></a>存取問題

如果您在使用公用（cluster.region.kusto.windows.net）或私用（private-cluster.region.kusto.windows.net）端點存取叢集時發生問題，而且懷疑它與虛擬網路設定相關，請執行下列步驟來針對問題進行疑難排解。

#### <a name="check-tcp-connectivity"></a>檢查 TCP 連線能力

第一個步驟包括使用 Windows 或 Linux OS 檢查 TCP 連線能力。

# <a name="windows"></a>[Windows](#tab/windows)

   1. 將[TCping](https://www.elifulkerson.com/projects/tcping.php)下載到連接到叢集的電腦。
   2. 使用下列命令，從來源機器 Ping 目的地：

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. 在連接到叢集的電腦上安裝*netcat*

    ```bash
    $ apt-get install netcat
     ```

   2. 使用下列命令，從來源機器 Ping 目的地：

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

如果測試不成功，請繼續進行下列步驟。 如果測試成功，問題不是因為 TCP 連線問題所造成。 請移至[操作問題](#cluster-creation-and-operations-issues)以進一步進行疑難排解。

#### <a name="check-the-network-security-group-nsg"></a>檢查網路安全性群組（NSG）

   檢查連接到叢集子網的[網路安全性群組](/azure/virtual-network/security-overview)（NSG）是否有輸入規則，允許從用戶端電腦的 IP 存取埠443。

#### <a name="check-route-table"></a>檢查路由表

   如果叢集的子網已將強制通道設定設為防火牆（子網具有包含預設路由 ' 0.0.0.0/0 ' 的[路由表](/azure/virtual-network/virtual-networks-udr-overview)），請確定電腦 IP 位址具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview)為 VirtualNetwork/Internet 的路由。 這是避免非對稱式路由問題的必要條件。

### <a name="ingestion-issues"></a>內嵌問題

如果您遇到內嵌問題，而且懷疑它與虛擬網路設定相關，請執行下列步驟。

#### <a name="check-ingestion-health"></a>檢查內嵌健全狀況

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>檢查資料來源資源的安全性規則

如果計量指出沒有從資料來源處理的事件（已*處理*的事件（事件/IoT 中樞）計量），請確定資料來源資源（事件中樞或儲存體）允許從防火牆規則或服務端點中的叢集子網存取。

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>檢查叢集子網上設定的安全性規則

請確定叢集的子網已正確設定 NSG、UDR 和防火牆規則。 此外，也請測試所有相依端點的網路連線能力。 

### <a name="cluster-creation-and-operations-issues"></a>叢集建立和作業問題

如果您遇到叢集建立或操作問題，而且懷疑它與虛擬網路設定相關，請遵循下列步驟來針對問題進行疑難排解。

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>使用 REST API 診斷虛擬網路

[ARMClient](https://chocolatey.org/packages/ARMClient)是用來使用 PowerShell 來呼叫 REST API。 

1. 使用 ARMClient 登入

   ```powerShell
   armclient login
   ```

1. 叫用診斷作業

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 檢查回應

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 等候操作完成

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   等到 [*狀態*] 屬性顯示為 [*已完成*]，然後 [*屬性*] 欄位應該會顯示：

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

如果 [*結果*] 屬性顯示空的結果，表示所有的網路測試都已通過，而且沒有任何連線中斷。 如果它顯示錯誤，如下所示：輸出相依性 *' {dependencyName}： {埠} ' 可能不滿足（輸出）* ，叢集無法連線到依存的服務端點。 繼續進行下列步驟來進行疑難排解。

#### <a name="check-network-security-group-nsg"></a>檢查網路安全性群組（NSG）

請根據[VNet 部署](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)的相依性中的指示，確定已正確設定[網路安全性群組](/azure/virtual-network/security-overview)

#### <a name="check-route-table"></a>檢查路由表

如果叢集的子網已將強制通道設定為防火牆（具有包含預設路由 ' 0.0.0.0/0 ' 之[路由表](/azure/virtual-network/virtual-networks-udr-overview)的子網），請確定[管理 ip 位址](#azure-data-explorer-management-ip-addresses)和[健全狀況監視 ip 位址](#health-monitoring-addresses)具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools)為*網際網路*的路由，且[來源位址首碼](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)為「*管理-ip/32* 」和「*健全狀況監視-ip/32*」。 這是避免非對稱式路由問題的必要條件。

#### <a name="check-firewall-rules"></a>檢查防火牆規則

如果您強制將通道子網輸出流量傳送到防火牆，請確定防火牆設定中允許所有相依性 FQDN （例如， *blob.core.windows.net*），如[使用防火牆保護輸出流量](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)中所述。