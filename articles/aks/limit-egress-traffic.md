---
title: 限制 Azure Kubernetes Service (AKS) 中的連出流量
description: 了解在 Azure Kubernetes Service (AKS) 中控制連出流量所需的連接埠和位址
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 06/29/2020
ms.custom: fasttrack-edit
author: palma21
ms.openlocfilehash: 33355251a06ba076be3677b84e383793f9f25193
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570377"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes Service (AKS) 中叢集節點的連出流量

本文提供必要的詳細資料，可讓您保護來自 Azure Kubernetes Service (AKS) 的輸出流量。 它包含基本 AKS 部署的叢集需求，以及選用附加元件和功能的其他需求。 [如需如何使用 Azure 防火牆來設定這些需求的結尾，將會提供範例](#restrict-egress-traffic-using-azure-firewall)。 不過，您可以將此資訊套用至任何輸出限制方法或設備。

## <a name="background"></a>背景

AKS 叢集會部署在虛擬網路上。 此網路可由 AKS) 所建立，或由使用者事先) 預先設定的自訂 (來管理 (。 無論是哪一種情況，叢集都有該虛擬網路外的服務 **輸出** 相依性 (服務沒有) 的輸入相依性。

基於管理和操作目的，AKS 叢集中的節點需要存取特定連接埠和完整網域名稱 (FQDN)。 節點需要這些端點才能與 API 伺服器通訊，或下載並安裝核心 Kubernetes 叢集元件和節點安全性更新。 例如，叢集必須從 Microsoft Container Registry 提取基礎系統容器映射 (MCR) 。

AKS 輸出相依性幾乎完全以 Fqdn 定義，其後面沒有靜態位址。 缺少靜態位址表示網路安全性群組不能用來鎖定來自 AKS 叢集的輸出流量。 

根據預設，AKS 叢集具有不受限制的輸出 (連出) 網際網路存取。 此網路存取層級可讓您執行的節點和服務視需要存取外部資源。 如果您想要限制連出流量，則必須能夠存取有限數量的連接埠和位址，才能維持狀況良好的叢集維護工作。 保護輸出位址最簡單的解決方案是使用防火牆裝置，該裝置可以根據功能變數名稱來控制輸出流量。 例如，Azure 防火牆可以根據目的地的 FQDN 限制輸出 HTTP 和 HTTPS 流量。 您也可以設定慣用的防火牆和安全性規則，以允許這些必要的埠和位址。

> [!IMPORTANT]
> 此文件僅涵蓋如何鎖定離開 AKS 子網路的流量。 依預設，AKS 沒有輸入需求。  不支援使用網路安全性群組來封鎖 **內部子網流量** (nsg) 和防火牆。 若要控制和封鎖叢集中的流量，請使用 [***網路原則***][network-policy]。

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS 叢集所需的輸出網路規則和 Fqdn

AKS 叢集需要下列網路和 FQDN/應用程式規則，如果您想要設定 Azure 防火牆以外的解決方案，您可以使用它們。

* 適用於非 HTTP/S 流量的 IP 地址相依性 (TCP 與 UDP 流量)
* FQDN HTTP/HTTPS 端點可以放在您的防火牆裝置。
* 萬用字元 HTTP/HTTPS 端點是相依性，可能會因數個限定詞而不同于您的 AKS 叢集。
* AKS 會使用許可控制器將 FQDN 做為環境變數插入 kube 系統和閘道管理員系統下的所有部署，以確保節點和 API 伺服器之間的所有系統通訊都使用 API 伺服器 FQDN，而不是 API 伺服器 IP。 
* 如果您有需要與 API 伺服器通訊的應用程式或解決方案，則必須新增 **額外** 的網路規則，以允許 *對 api 伺服器 IP 的埠443進行 TCP 通訊*。
* 在罕見的情況下，如果有維護作業，則您的 API 伺服器 IP 可能會變更。 可變更 API 伺服器 IP 的預定維護作業，一律會事先進行通訊。


### <a name="azure-global-required-network-rules"></a>Azure 全球所需的網路規則

必要的網路規則和 IP 位址相依性如下：

| 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用於節點和控制平面之間的通道通訊安全通訊。 [私人](private-clusters.md)叢集不需要這項功能|
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用於節點和控制平面之間的通道通訊安全通訊。 [私人](private-clusters.md)叢集不需要這項功能 |
| **`*:123`** 或者， **`ntp.ubuntu.com:123`** 如果使用 Azure 防火牆網路規則，則 ()   | UDP      | 123     | 網路時間通訊協定需要 (NTP) Linux 節點上的時間同步處理。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果您使用自訂的 DNS 伺服器，則必須確定叢集節點可存取它們。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果執行存取 API 伺服器的 pod/部署，則為必要項，這些 pod/部署會使用 API IP。 [私人](private-clusters.md)叢集不需要這項功能  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure 全域必要的 FQDN/應用程式規則 

需要下列 FQDN/應用程式規則：

| 目的地 FQDN                 | 連接埠            | 使用      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | 節點 < > API 伺服器通訊的必要元件。 取代 *\<location\>* 為您的 AKS 叢集部署所在的區域。 |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | 存取 Microsoft Container Registry 中的映射所需 (MCR) 。 此登錄包含第一方映射/圖表 (例如，coreDNS 等 ) 。 您必須要有這些映射，才能正確建立和運作叢集，包括調整和升級作業。  |
| **`*.cdn.mscr.io`**              | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`management.azure.com`**       | **`HTTPS:443`** | 針對 Azure API 進行 Kubernetes 作業所需。 |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory 驗證的必要。 |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | 此位址是用於快取 *apt-get* 作業的 Microsoft 套件存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | 此位址適用于下載和安裝所需的二進位檔（例如 kubenet 和 Azure CNI）所需的存放庫。 |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure 中國世紀需要網路規則

必要的網路規則和 IP 位址相依性如下：

| 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用於節點和控制平面之間的通道通訊安全通訊。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用於節點和控制平面之間的通道通訊安全通訊。 |
| **`*:22`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Or* <br/> **`APIServerIP:22`** `(only known after cluster creation)`  | TCP           | 22      | 用於節點和控制平面之間的通道通訊安全通訊。 |
| **`*:123`** 或者， **`ntp.ubuntu.com:123`** 如果使用 Azure 防火牆網路規則，則 ()   | UDP      | 123     | 網路時間通訊協定需要 (NTP) Linux 節點上的時間同步處理。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果您使用自訂的 DNS 伺服器，則必須確定叢集節點可存取它們。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果執行存取 API 伺服器的 pod/部署，則為必要項，這些 pod/部署會使用 API IP。  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure 中國世紀需要 FQDN/應用程式規則

需要下列 FQDN/應用程式規則：

| 目的地 FQDN                               | 連接埠            | 使用      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 節點 < > API 伺服器通訊的必要元件。 取代 *\<location\>* 為您的 AKS 叢集部署所在的區域。 |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 節點 < > API 伺服器通訊的必要元件。 取代 *\<location\>* 為您的 AKS 叢集部署所在的區域。 |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | 存取 Microsoft Container Registry 中的映射所需 (MCR) 。 此登錄包含第一方映射/圖表 (例如，coreDNS 等 ) 。 您必須要有這些映射，才能正確建立和運作叢集，包括調整和升級作業。 |
| **`*.cdn.mscr.io`**                            | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | 針對 Azure API 進行 Kubernetes 作業所需。 |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory 驗證的必要。 |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | 此位址是用於快取 *apt-get* 作業的 Microsoft 套件存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | 此位址適用于下載和安裝所需的二進位檔（例如 kubenet 和 Azure CNI）所需的存放庫。 |

### <a name="azure-us-government-required-network-rules"></a>Azure 美國政府所需的網路規則

必要的網路規則和 IP 位址相依性如下：

| 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用於節點和控制平面之間的通道通訊安全通訊。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用於節點和控制平面之間的通道通訊安全通訊。 |
| **`*:123`** 或者， **`ntp.ubuntu.com:123`** 如果使用 Azure 防火牆網路規則，則 ()   | UDP      | 123     | 網路時間通訊協定需要 (NTP) Linux 節點上的時間同步處理。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果您使用自訂的 DNS 伺服器，則必須確定叢集節點可存取它們。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果執行存取 API 伺服器的 pod/部署，則為必要項，這些 pod/部署會使用 API IP。  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 美國政府需要 FQDN/應用程式規則 

需要下列 FQDN/應用程式規則：

| 目的地 FQDN                                        | 連接埠            | 使用      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | 節點 < > API 伺服器通訊的必要元件。 取代 *\<location\>* 為您的 AKS 叢集部署所在的區域。|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | 存取 Microsoft Container Registry 中的映射所需 (MCR) 。 此登錄包含第一方映射/圖表 (例如，coreDNS 等 ) 。 您必須要有這些映射，才能正確建立和運作叢集，包括調整和升級作業。 |
| **`*.cdn.mscr.io`**                                     | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Azure 內容傳遞網路支援的 MCR 儲存體 (CDN) 所需。 |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | 針對 Azure API 進行 Kubernetes 作業所需。 |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory 驗證的必要。 |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | 此位址是用於快取 *apt-get* 作業的 Microsoft 套件存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | 此位址適用於安裝必要的二進位檔 (例如 kubenet 和 Azure CNI) 所需的存放庫。 |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>AKS 叢集的選用建議 FQDN/應用程式規則

下列 FQDN/應用程式規則是選擇性的，但建議用於 AKS 叢集：

| 目的地 FQDN                                                               | 連接埠          | 使用      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | 此位址可讓 Linux 叢集節點下載所需的安全性修補程式和更新。 |

如果您選擇封鎖/不允許這些 Fqdn，節點將只會在您進行 [節點映射升級](node-image-upgrade.md) 或叢集 [升級](upgrade-cluster.md)時收到作業系統更新。

## <a name="gpu-enabled-aks-clusters"></a>已啟用 GPU 的 AKS 叢集

### <a name="required-fqdn--application-rules"></a>必要的 FQDN/應用程式規則

已啟用 GPU 之 AKS 叢集需要下列 FQDN/應用程式規則：

| 目的地 FQDN                        | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | 此位址是用來在以 GPU 為基礎的節點上進行正確的驅動程式安裝和運作。 |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | 此位址是用來在以 GPU 為基礎的節點上進行正確的驅動程式安裝和運作。 |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | 此位址是用來在以 GPU 為基礎的節點上進行正確的驅動程式安裝和運作。 |

## <a name="windows-server-based-node-pools"></a>以 Windows Server 為基礎的節點集區 

### <a name="required-fqdn--application-rules"></a>必要的 FQDN/應用程式規則

使用 Windows Server 型節點集區時，需要下列 FQDN/應用程式規則：

| 目的地 FQDN                                                           | 連接埠      | 使用      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | 安裝與 Windows 相關的二進位檔 |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | 安裝與 Windows 相關的二進位檔 |

## <a name="aks-addons-and-integrations"></a>AKS 附加元件和整合

### <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器

有兩個選項可讓您存取容器的 Azure 監視器，您可以允許 Azure 監視器 [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **或** 提供所需 FQDN/應用程式規則的存取權。

#### <a name="required-network-rules"></a>必要的網路規則

需要下列 FQDN/應用程式規則：

| 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | 此端點是用來將計量資料和記錄傳送至 Azure 監視器和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必要的 FQDN/應用程式規則

已啟用適用於容器之 Azure 監視器的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | 此端點會用於使用 Azure 監視器的計量和監視遙測。 |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Azure 監視器使用此端點來擷取 log analytics 資料。 |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Omsagent 會使用此端點來驗證 log analytics 服務。 |
| *.monitoring.azure.com | **`HTTPS:443`** | 此端點是用來將計量資料傳送至 Azure 監視器。 |

### <a name="azure-dev-spaces"></a>Azure 開發人員空間

更新您的防火牆或安全性設定，以允許下列所有 Fqdn 和 [Azure Dev Spaces 基礎結構服務][dev-spaces-service-tags]之間的網路流量。

#### <a name="required-network-rules"></a>必要的網路規則

| 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | 此端點是用來將計量資料和記錄傳送至 Azure 監視器和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必要的 FQDN/應用程式規則 

已啟用 Azure Dev Spaces 的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | 此位址是用來提取 Linux Alpine 和其他 Azure Dev Spaces 映像 |
| `gcr.io` | **`HTTPS:443`** | 此位址是用來提取 helm/tiller 映像 |
| `storage.googleapis.com` | **`HTTPS:443`** | 此位址是用來提取 helm/tiller 映像 |


### <a name="azure-policy"></a>Azure 原則

#### <a name="required-fqdn--application-rules"></a>必要的 FQDN/應用程式規則 

已啟用 Azure 原則的 AKS 叢集需要下列 FQDN/應用程式規則。

| FQDN                                          | 連接埠      | 使用      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | 此位址是用來確保 Azure 原則的正確運作。 (目前在 AKS 中為預覽狀態) |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | 此位址是用來從 GitHub 提取內建原則，以確保 Azure 原則的正確運作。 (目前在 AKS 中為預覽狀態) |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure 原則附加元件，其會將遙測資料傳送至 Application Insights 端點。 |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>使用 Azure 防火牆限制輸出流量

Azure 防火牆會提供 Azure Kubernetes Service (`AzureKubernetesService`) FQDN 標籤，以簡化此設定。 

> [!NOTE]
> FQDN 標籤包含上列所有 Fqdn，並會自動保持在最新狀態。
>
> 針對生產案例，建議在 Azure 防火牆上至少有20個前端 Ip，以避免發生 SNAT 埠耗盡問題。

以下是部署的範例架構：

![已鎖定拓撲](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* 公用輸入強制流經防火牆篩選器
  * AKS 代理程式節點會獨立于專用子網中。
  * [Azure 防火牆](../firewall/overview.md) 會部署在它自己的子網中。
  * DNAT 規則會將 FW 公用 IP 轉譯為 LB 前端 IP。
* 輸出要求會使用[使用者定義的路由](egress-outboundtype.md)，從代理程式節點開始至 Azure 防火牆內部 IP
  * 來自 AKS 代理程式節點的要求會遵循已放置於部署 AKS 叢集之子網路上的 UDR。
  * Azure 防火牆會從公用 IP 前端輸出虛擬網路
  * 存取公用網際網路或其他 Azure 服務，會流入和流出防火牆前端 IP 位址
  * （選擇性） AKS 控制平面的存取權受 [API 伺服器授權的 ip 範圍](./api-server-authorized-ip-ranges.md)所保護，其中包含防火牆公用前端 ip 位址。
* 內部流量
  * （選擇性）您也可以使用內部流量（除了 [公用 Load Balancer](load-balancer-standard.md) ），也可以將 [內部 Load Balancer](internal-lb.md) 用於內部流量，也就是您可以在自己的子網上隔離。


下列步驟會使用 Azure 防火牆的 FQDN 標籤 `AzureKubernetesService` 來限制來自 AKS 叢集的輸出流量，並提供如何透過防火牆設定公用輸入流量的範例。


### <a name="set-configuration-via-environment-variables"></a>透過環境變數設定組態

定義一組要在資源建立時使用的環境變數。

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>建立有多個子網路的虛擬網路

布建具有兩個不同子網的虛擬網路，一個用於叢集，一個用於防火牆。 （選擇性）您也可以建立一個用於內部服務輸入。

![空的網路拓撲](media/limit-egress-traffic/empty-network.png)

建立資源群組以保存所有資源。

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

建立具有兩個子網的虛擬網路，以裝載 AKS 叢集和 Azure 防火牆。 每個都將有自己的子網路。 讓我們從 AKS 網路開始。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>使用 UDR 建立和設定 Azure 防火牆

您必須設定 Azure 防火牆的連入與連出規則。 防火牆的主要目的是要讓組織將細微的輸入和輸出流量規則設定入和移出 AKS 叢集。

![防火牆和 UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> 如果您的叢集或應用程式所建立的大量輸出連線會導向至相同或較小的目的地子集，您可能需要更多的防火牆前端 ip，以避免提高每個前端 IP 的埠。
> 如需有關如何建立具有多個 Ip 的 Azure 防火牆的詳細資訊，請參閱 [**這裡**](../firewall/quick-create-multiple-ip-template.md)

建立將用來作為 Azure 防火牆前端位址的標準 SKU 公用 IP 資源。

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

註冊預覽 CLI 延伸模組以建立 Azure 防火牆。
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
現在可以將先前建立的 IP 位址指派給防火牆前端。

> [!NOTE]
> 將公用 IP 位址設定為 Azure 防火牆可能需要幾分鐘的時間。
> 若要在網路規則上利用 FQDN，我們必須啟用 DNS proxy，當啟用時，防火牆會接聽埠53，並將 DNS 要求轉寄至上述指定的 DNS 伺服器。 這可讓防火牆自動轉譯該 FQDN。

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

當上一個命令成功之後，儲存防火牆前端 IP 位址，以供稍後設定。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> 如果您對 AKS API 伺服器使用具有 [授權 ip 位址範圍](./api-server-authorized-ip-ranges.md)的安全存取，則需要將防火牆公用 ip 新增至授權的 ip 範圍。

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>在 Azure 防火牆中建立具有躍點的 UDR

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要變更任何 Azure 的預設路由，您可藉由建立路由表來執行此動作。

建立要與指定子網路相關聯的空路由表。 路由表會將下一個躍點定義為上面建立的 Azure 防火牆。 每個子網路可以有零個或一個與其相關聯的路由表。

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

請參閱[虛擬網路路由表文件](../virtual-network/virtual-networks-udr-overview.md#user-defined)，以了解如何覆寫 Azure 的預設系統路由，或將其他路由新增至子網路的路由表。

### <a name="adding-firewall-rules"></a>新增防火牆規則

以下是您可以用來在防火牆上設定的三個網路規則，您可能需要根據您的部署來調整這些規則。 第一個規則可讓您透過 TCP 存取埠9000。 第二個規則允許透過 UDP 存取埠1194和 123 (如果您要部署到 Azure 中國的世紀，您可能需要 [更多](#azure-china-21vianet-required-network-rules)) 。 這兩個規則只會允許目的地為 Azure 區域 CIDR （在此案例中為美國東部）的流量。 最後，我們會透過 UDP 新增第三個網路規則，以開啟埠123至 `ntp.ubuntu.com` FQDN (新增 FQDN 作為網路規則是 Azure 防火牆的其中一個特定功能，而且您必須在使用自己的選項時加以調整) 。

設定網路規則之後，我們也會使用來新增應用程式規則， `AzureKubernetesService` 其中涵蓋可透過 TCP 埠443和埠80存取的所有必要 fqdn。

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

若要深入了解 Azure 防火牆服務，請參閱 [Azure 防火牆文件](../firewall/overview.md)。

### <a name="associate-the-route-table-to-aks"></a>將路由表關聯至 AKS

若要將叢集與防火牆建立關聯，叢集子網路的專用子網路必須參考上面建立的路由表。 藉由發出命令給保存叢集和防火牆的虛擬網路，來更新叢集子網路的路由表，即可完成關聯。

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>將具有 UDR 連出類型的 AKS 部署到現有網路

現在 AKS 叢集可以部署到現有的虛擬網路中。 我們也會使用[輸出類型 `userDefinedRouting` ](egress-outboundtype.md)，這項功能可確保任何輸出流量都會透過防火牆強制執行，而且預設不會有其他輸出路徑 (預設會使用 Load Balancer 輸出類型) 。

![AKS 部署](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>建立可存取現有虛擬網路內佈建的服務主體

AKS 會使用服務主體來建立叢集資源。 在建立時傳遞的服務主體會用來建立基礎 AKS 資源，例如 AKS 所使用的儲存體資源、Ip 和負載平衡器 (您也可以改為使用 [受控識別](use-managed-identity.md)) 。 如果未授與下列適當的許可權，您將無法布建 AKS 叢集。

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

現在，以先前命令輸出自動產生的服務主體應用程式識別碼和服務主體密碼取代 `APPID` 和 `PASSWORD`。 我們將參考 VNET 資源識別碼，以將許可權授與服務主體，讓 AKS 能夠將資源部署到其中。

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

您可以在 [此](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)檢查所需的詳細許可權。

> [!NOTE]
> 如果您使用的是 kubenet 網路外掛程式，您必須將 AKS 服務主體或受控識別許可權授與預先建立的路由表，因為 kubenet 需要路由表來新增資源清單路由規則。 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>部署 AKS

最後，AKS 叢集可以部署到我們專用於叢集的現有子網中。 要部署到的目標子網路是使用環境變數 `$SUBNETID` 來定義。 我們並未在先前的步驟中定義 `$SUBNETID` 變數。 若要設定子網路識別碼的值，您可以使用下列命令：

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

您將定義輸出類型，以使用已存在於子網的 UDR。 此設定可讓 AKS 略過負載平衡器的設定和 IP 布建。

> [!IMPORTANT]
> 如需輸出類型 UDR （包括限制）的詳細資訊，請參閱 [**輸出輸出類型 UDR**](egress-outboundtype.md#limitations)。


> [!TIP]
> 您可以將其他功能新增至叢集部署，例如 [**私人叢集中**](private-clusters.md)。 
>
> 您可以新增 [**api 伺服器授權 IP 範圍**](api-server-authorized-ip-ranges.md) 的 AKS 功能，以將 api 伺服器存取限制為僅限防火牆的公用端點。 [授權的 IP 範圍] 功能在圖表中表示為選擇性。 啟用授權的 IP 範圍功能以限制 API 伺服器存取時，您的開發人員工具必須使用來自防火牆虛擬網路的 Jumpbox，或者您必須將所有開發人員端點新增至授權 IP 範圍。

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>讓開發人員能夠存取 API 伺服器

如果您在上一個步驟中使用叢集的授權 IP 範圍，您必須將開發人員工具的 IP 位址新增至已核准 IP 範圍的 AKS 叢集清單，才能從該處存取 API 伺服器。 另一個選項是在防火牆虛擬網路中的個別子網路內，使用所需的工具來設定 Jumpbox。

使用下列命令，在核准的範圍內新增另一個 IP 位址

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用 [az aks get-認證] [az-aks] 命令，將設定 `kubectl` 為連線至新建立的 Kubernetes 叢集。 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>部署公用服務
您現在可以開始公開服務，並將應用程式部署到此叢集。 在此範例中，我們會公開公用服務，但您也可以選擇透過 [內部負載平衡器](internal-lb.md)公開內部服務。

![公用服務 DNAT](media/limit-egress-traffic/aks-create-svc.png)

將下列 yaml 複製到名為 `example.yaml` 的檔案，以部署 Azure 投票 App 應用程式。

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

執行下列動作來部署服務：

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>將 DNAT 規則新增至 Azure 防火牆

> [!IMPORTANT]
> 當您使用 Azure 防火牆來限制連出流量，並建立使用者定義路由 (UDR) 來強制執行所有連出流量時，請務必在防火牆中建立適當的 DNAT 規則，以正確地允許連入流量。 將 Azure 防火牆搭配 UDR 使用，會因為非對稱式路由而中斷連入設定。 (如果 AKS 子網路的預設路由會前往防火牆私人 IP 位址，但是您使用的是公用負載平衡器 - 類型為 LoadBalancer 的連入或 Kubernetes 服務，則會發生此問題)。 在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 由於防火牆是具狀態，其會捨棄傳回封包，因為防火牆並不知道已建立的工作階段。 若要了解如何整合 Azure 防火牆與您的連入或服務負載平衡器，請參閱[整合 Azure 防火牆與 Azure Standard Load Balancer](../firewall/integrate-lb.md) \(部分機器翻譯\)。


若要設定連入連線能力，必須將 DNAT 規則寫入至 Azure 防火牆。 若要測試對叢集的連線能力，會針對防火牆前端公用 IP 位址定義規則，以路由傳送至內部服務所公開的內部 IP。

目的地位址可以自訂，因為它是防火牆上要存取的埠。 轉譯的位址必須是內部負載平衡器的 IP 位址。 轉譯的連接埠必須是針對您 Kubernetes 服務公開的連接埠。

您必須指定指派給 Kubernetes 服務所建立之負載平衡器的內部 IP 位址。 執行下列命令來擷取位址：

```bash
kubectl get services
```

所需的 IP 位址將列在 EXTERNAL-IP (外部 IP) 欄中，如下所示。

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

藉由執行下列動作來取得服務 IP：
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

藉由執行下列動作來新增 NAT 規則：
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>驗證連線能力

在瀏覽器中瀏覽至 Azure 防火牆前端 IP 位址，以驗證連線能力。

您應該會看到 AKS 投票應用程式。 在此範例中，防火牆公用 IP 為 `52.253.228.132` 。


![螢幕擷取畫面顯示具有貓、狗和重設等按鈕的 K S 投票應用程式。](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>清除資源

若要清理 Azure 資源，請刪除 AKS 資源群組。

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解當您想要限制叢集的輸出流量時，所允許的埠和位址。 您也已瞭解如何使用 Azure 防火牆來保護您的輸出流量。 

如有需要，您可以將上述步驟一般化，以將流量轉送到您偏好的輸出解決方案，請遵循 [輸出類型 `userDefinedRoute` 檔](egress-outboundtype.md)。

如果您想要限制 pod 之間的通訊方式，以及叢集中的東部-西部流量限制，請參閱 [使用 AKS 中的網路原則來保護 pod 之間的流量][network-policy]。

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
