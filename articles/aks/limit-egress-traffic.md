---
title: 限制 Azure Kubernetes Service 中的輸出流量（AKS）
description: 瞭解在 Azure Kubernetes Service （AKS）中控制輸出流量所需的埠和位址
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080184"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes Service 中叢集節點的輸出流量（AKS）

根據預設，AKS 叢集具有不受限制的輸出（傳出）網際網路存取。 此網路存取層級可讓您執行的節點和服務視需要存取外部資源。 如果您想要限制輸出流量，則必須能夠存取有限數目的埠和位址，才能維持狀況良好的叢集維護工作。 根據預設，您的叢集會設定為只使用 Microsoft Container Registry （MCR）或 Azure Container Registry （ACR）中的基本系統容器映射。 設定您慣用的防火牆和安全性規則，以允許這些必要的埠和位址。

本文詳細說明必要的網路埠和完整功能變數名稱（Fqdn），以及當您在 AKS 叢集中限制輸出流量時，是選擇性的。

> [!IMPORTANT]
> 本檔僅涵蓋如何鎖定離開 AKS 子網的流量。 AKS 沒有輸入需求。  不支援使用網路安全性群組（Nsg）和防火牆封鎖內部子網流量。 若要控制和封鎖叢集中的流量，請使用[網路原則][network-policy]。

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.66 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>輸出流量概觀

基於管理和操作的目的，AKS 叢集中的節點需要存取特定埠和完整功能變數名稱（Fqdn）。 這些動作可能是與 API 伺服器通訊，或下載並安裝核心 Kubernetes 叢集元件和節點安全性更新。 根據預設，傳出（輸出）網際網路流量不會限制在 AKS 叢集中的節點。 叢集可能會從外部存放庫提取基本系統容器映射。

若要提高 AKS 叢集的安全性，您可能會想要限制傳出流量。 叢集已設定為從 MCR 或 ACR 提取基礎系統容器映射。 如果您以這種方式鎖定輸出流量，請定義特定埠和 Fqdn，以允許 AKS 節點正確地與所需的外部服務進行通訊。 如果沒有這些授權的埠和 Fqdn，您的 AKS 節點就無法與 API 伺服器通訊或安裝核心元件。

您可以使用[Azure 防火牆][azure-firewall]或協力廠商防火牆設備來保護您的輸出流量，並定義這些必要的埠和位址。 AKS 不會自動為您建立這些規則。 當您在網路防火牆中建立適當的規則時，可以參考下列埠和位址。

> [!IMPORTANT]
> 當您使用 Azure 防火牆來限制輸出流量，並建立使用者定義的路由（UDR）來強制執行所有輸出流量時，請務必在防火牆中建立適當的 DNAT 規則，以正確地允許輸入流量。 使用 Azure 防火牆搭配 UDR 會中斷輸入設定，因為非對稱式路由。 （如果 AKS 子網具有移至防火牆私人 IP 位址的預設路由，但您使用的是下列類型的公用負載平衡器-輸入或 Kubernetes 服務，就會發生此問題： LoadBalancer）。 在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 因為防火牆是具狀態的，所以它會捨棄傳回的封包，因為防火牆並不知道已建立的會話。 若要瞭解如何整合 Azure 防火牆與您的輸入或服務負載平衡器，請參閱[整合 Azure 防火牆與 azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb)。
> 您可以使用輸出背景工作角色節點 IP 與 API 伺服器的 IP 之間的網路規則，鎖定 TCP 埠9000、TCP 埠22和 UDP 埠1194的流量。

在 AKS 中，有兩組埠和位址：

* AKS 叢集所[需的埠和位址](#required-ports-and-addresses-for-aks-clusters)會詳細說明授權輸出流量的最低需求。
* 並非所有案例都需要[選擇性的 AKS 叢集建議位址和埠](#optional-recommended-addresses-and-ports-for-aks-clusters)，但是與其他服務（例如 Azure 監視器）的整合將無法正確運作。 請參閱這份選擇性埠和 Fqdn 清單，並授權您的 AKS 叢集中使用的任何服務和元件。

> [!NOTE]
> 限制輸出流量僅適用于新的 AKS 叢集。 針對現有的叢集，請使用 `az aks upgrade` 命令[執行叢集升級][aks-upgrade]作業，然後才限制輸出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 叢集所需的埠和位址

AKS 叢集需要下列輸出埠/網路規則：

* TCP 埠*443*
* 如果您有需要與 API 伺服器交談的應用程式，則需要 TCP [IPAddrOfYourAPIServer]：443。  這種變更可以在建立叢集之後設定。
* TCP 埠*9000*、tcp 埠*22*和 UDP 埠*1194* ，通道前端 pod 會與 API 伺服器上的通道結束通訊。
    * 若要取得更具體的資訊，請參閱下表中的 *\<位置\>. azmk8s.io*和 * *. 執行\>\<. azmk8s.io*位址。
* 網路時間通訊協定（NTP）時間同步處理（Linux 節點）的 UDP 埠*123* 。
* 如果您有 pod 直接存取 API 伺服器，則也需要適用于 DNS 的 UDP 埠*53* 。

需要下列 FQDN/應用程式規則：

> [!IMPORTANT]
> * **。 blob.core.windows.net 和 aksrepos.azurecr.io**不再需要用於輸出鎖定的 FQDN 規則。  針對現有的叢集，請使用 `az aks upgrade` 命令來[執行叢集升級][aks-upgrade]作業，以移除這些規則。

> [!IMPORTANT]
> *. cdn.mscr.io 已取代為 Azure 公用雲端區域的 *. data.mcr.microsoft.com。 請升級現有的防火牆規則，變更才會生效。

- Azure 全域

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *. hcp。\<位置\>. azmk8s.io | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| *. 執行。\<位置\>. azmk8s.io | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| *. cdn.mscr.io       | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| mcr.microsoft.com          | HTTPS：443 | 需要有此位址，才能存取 Microsoft Container Registry （MCR）中的映射。 此登錄包含在叢集的升級和調整期間，叢集運作所需的第一方映射/圖表（例如，moby 等） |
| *. data.mcr.microsoft.com             | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| management.azure.com       | HTTPS：443 | Kubernetes 取得/PUT 作業需要此位址。 |
| login.microsoftonline.com  | HTTPS：443 | Azure Active Directory 驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 在 Linux 節點上進行 NTP 時間同步處理時，需要此位址。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於*快取 apt-get*作業的 Microsoft 封裝存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此位址適用于安裝必要的二進位檔（例如 kubenet 和 Azure CNI）所需的存放庫。 |

- Azure China 21Vianet

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *. hcp。\<位置\>. cx.prod.service.azk8s.cn | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| *. 執行。\<位置\>. cx.prod.service.azk8s.cn | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| *. azk8s.cn        | HTTPS：443 | 需要此位址才能下載所需的二進位檔和映射|
| mcr.microsoft.com          | HTTPS：443 | 需要有此位址，才能存取 Microsoft Container Registry （MCR）中的映射。 此登錄包含在叢集的升級和調整期間，叢集運作所需的第一方映射/圖表（例如，moby 等） |
| *. cdn.mscr.io       | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| *. data.mcr.microsoft.com             | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| management.chinacloudapi.cn       | HTTPS：443 | Kubernetes 取得/PUT 作業需要此位址。 |
| login.chinacloudapi.cn  | HTTPS：443 | Azure Active Directory 驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 在 Linux 節點上進行 NTP 時間同步處理時，需要此位址。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於*快取 apt-get*作業的 Microsoft 封裝存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |

- Azure Government

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *. hcp。\<位置\>. cx.aks.containerservice.azure.us | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| *. 執行。\<位置\>. cx.aks.containerservice.azure.us | HTTPS：443、TCP：22、TCP：9000、UDP：1194 | 節點 < > API 伺服器通訊需要此位址。 以部署 AKS 叢集所在的區域取代 *\<位置\>* 。 |
| mcr.microsoft.com          | HTTPS：443 | 需要有此位址，才能存取 Microsoft Container Registry （MCR）中的映射。 此登錄包含在叢集的升級和調整期間，叢集運作所需的第一方映射/圖表（例如，moby 等） |
|*. cdn.mscr.io              | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| *. data.mcr.microsoft.com             | HTTPS：443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| management.usgovcloudapi.net       | HTTPS：443 | Kubernetes 取得/PUT 作業需要此位址。 |
| login.microsoftonline.us  | HTTPS：443 | Azure Active Directory 驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 在 Linux 節點上進行 NTP 時間同步處理時，需要此位址。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於*快取 apt-get*作業的 Microsoft 封裝存放庫。  範例套件包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此位址適用于安裝必要的二進位檔（例如 kubenet 和 Azure CNI）所需的存放庫。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>適用于 AKS 叢集的選擇性建議位址和埠

下列輸出埠/網路規則對於 AKS 叢集而言是選擇性的：

建議使用下列 FQDN/應用程式規則，讓 AKS 叢集正常運作：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com、azure.archive.ubuntu.com、changelogs.ubuntu.com | HTTP：80   | 此位址可讓 Linux 叢集節點下載所需的安全性修補程式和更新。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>已啟用 GPU 的 AKS 叢集所需的位址和埠

已啟用 GPU 的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS：443 | 此位址用於以 GPU 為基礎的節點上進行正確的驅動程式安裝和操作。 |
| us.download.nvidia.com | HTTPS：443 | 此位址用於以 GPU 為基礎的節點上進行正確的驅動程式安裝和操作。 |
| apt.dockerproject.org | HTTPS：443 | 此位址用於以 GPU 為基礎的節點上進行正確的驅動程式安裝和操作。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>已啟用容器 Azure 監視器的必要位址和埠

已啟用容器 Azure 監視器的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS：443    | 這適用于使用 Azure 監視器的正確計量和監視遙測。 |
| *.ods.opinsights.azure.com    | HTTPS：443    | Azure 監視器用於內嵌 log analytics 資料。 |
| *.oms.opinsights.azure.com | HTTPS：443 | Omsagent 會使用此位址來驗證 log analytics 服務。 |
|*.microsoftonline.com | HTTPS：443 | 這是用來驗證和傳送計量給 Azure 監視器。 |
|*. monitoring.azure.com | HTTPS：443 | 這是用來將計量資料傳送至 Azure 監視器。 |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>已啟用 Azure Dev Spaces 所需的位址和埠

啟用 Azure Dev Spaces 的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 此位址可用來提取 linux alpine 和其他 Azure Dev Spaces 映射 |
| gcr.io | HTTP：443 | 此位址可用來提取 helm/tiller 映射 |
| storage.googleapis.com | HTTP：443 | 此位址可用來提取 helm/tiller 映射 |
| azds-\<guid\>。\<位置\>. azds.io | HTTPS：443 | 與您的控制器 Azure Dev Spaces 後端服務進行通訊。 您可以在% USERPROFILE%\.azds\settings.json 的 "dataplaneFqdn" 中找到確切的 FQDN |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>已啟用 Azure 原則（處於公開預覽狀態）的 AKS 叢集所需的位址和埠

> [!CAUTION]
> 下列部分功能目前為預覽狀態。  本文中的建議可能會隨著功能移至公開預覽和未來的發行階段而改變。

已啟用 Azure 原則的 AKS 叢集需要下列 FQDN/應用程式規則。

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS：443 | 此位址會用來進行 Azure 原則的正確操作。 （目前在 AKS 中為預覽狀態） |
| raw.githubusercontent.com | HTTPS：443 | 此位址是用來從 GitHub 提取內建原則，以確保 Azure 原則的正確操作。 （目前在 AKS 中為預覽狀態） |
| *. gk。\<位置\>. azmk8s.io | HTTPS：443    | Azure 原則附加元件，會與在主伺服器中執行的閘道管理員 audit 端點交談，以取得審核結果。 |
| dc.services.visualstudio.com | HTTPS：443 | 將遙測資料傳送至 application insights 端點的 Azure 原則附加元件。 |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>已啟用 Windows Server 架構節點的必要項（公開預覽）

> [!CAUTION]
> 下列部分功能目前為預覽狀態。  本文中的建議可能會隨著功能移至公開預覽和未來的發行階段而改變。

以 Windows Server 為基礎的 AKS 叢集需要下列 FQDN/應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net、winlayers.blob.core.windows.net、winlayers.cdn.mscr.io、go.microsoft.com | HTTPS：443 | 安裝與 windows 相關的二進位檔 |
| mp.microsoft.com、www<span></span>. msftconnecttest.com、ctldl.windowsupdate.com | HTTP：80 | 安裝與 windows 相關的二進位檔 |
| kms.core.windows.net | TCP：1688 | 安裝與 windows 相關的二進位檔 |


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解在限制叢集的輸出流量時，要允許哪些埠和位址。 您也可以定義 pod 本身可以通訊的方式，以及它們在叢集內的限制。 如需詳細資訊，請參閱[在 AKS 中使用網路原則來保護 pod 之間的流量][network-policy]。

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
