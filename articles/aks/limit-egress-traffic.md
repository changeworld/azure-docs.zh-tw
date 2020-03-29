---
title: 限制 Azure 庫伯內斯服務 （AKS） 中的出口流量
description: 瞭解在 Azure 庫伯奈斯服務 （AKS） 中控制出口流量需要哪些埠和位址
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080184"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure 庫伯內斯服務 （AKS） 中叢集節點的傳出流量

預設情況下，AKS 群集具有不受限制的出站（出口）互聯網接入。 此級別的網路訪問允許運行的節點和服務根據需要訪問外部資源。 如果要限制出口流量，則必須訪問數量有限的埠和位址，以保持健康的群集維護任務。 預設情況下，群集配置為僅使用來自 Microsoft 容器註冊表 （MCR） 或 Azure 容器註冊表 （ACR） 的基本系統容器映射。 配置首選的防火牆和安全規則以允許這些所需的埠和位址。

本文詳細介紹了在 AKS 群集中限制出口流量時，需要哪些網路埠和完全限定的功能變數名稱 （FQDN） 是必需的，可選。

> [!IMPORTANT]
> 本文檔僅介紹如何鎖定離開 AKS 子網的流量。 AKS 沒有入口要求。  不支援使用網路安全性群組 （NSG） 和防火牆阻止內部子網流量。 要控制和阻止群集中的流量，請使用[網路原則][network-policy]。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 版本 2.0.66 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>輸出流量概觀

出於管理和操作目的，AKS 群集中的節點需要訪問某些埠和完全限定的功能變數名稱 （FQDN）。 這些操作可以是與 API 伺服器通信，或者下載並安裝核心 Kubernetes 群集元件和節點安全更新。 預設情況下，AKS 群集中的節點的出入口（出站）Internet 流量不受限制。 群集可以從外部存儲庫中提取基本系統容器映射。

為了提高 AKS 群集的安全性，您可能希望限制出口流量。 群集配置為從 MCR 或 ACR 中提取基本系統容器映射。 如果以這種方式鎖定出口流量，請定義特定埠和 FQDN，以允許 AKS 節點與所需的外部服務正確通信。 如果沒有這些授權埠和 FQDN，您的 AKS 節點無法與 API 伺服器通信或安裝核心元件。

您可以使用[Azure 防火牆][azure-firewall]或協力廠商防火牆設備來保護出口流量並定義這些所需的埠和位址。 AKS 不會為您自動創建這些規則。 在網路防火牆中創建適當的規則時，以下埠和位址可供參考。

> [!IMPORTANT]
> 當您使用 Azure 防火牆限制出口流量並創建使用者定義的路由 （UDR） 以強制所有出口流量時，請確保在防火牆中創建適當的 DNAT 規則以正確允許入口流量。 將 Azure 防火牆與 UDR 一起使用會破壞由於非對稱路由而導致的入口設置。 （如果 AKS 子網具有通往防火牆私人 IP 位址的預設路由，但您使用的是公共負載等化器 - 入口或 Kubernetes 類型的服務：LoadBalancer），則會出現此問題。 在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 由於防火牆是有狀態的，因此它會丟棄返回的資料包，因為防火牆不知道已建立的會話。 要瞭解如何將 Azure 防火牆與入口或服務負載等化器集成，請參閱將[Azure 防火牆與 Azure 標準負載等化器集成](https://docs.microsoft.com/azure/firewall/integrate-lb)。
> 您可以使用出口輔助節點 IP 和 API 伺服器的 IP 之間的網路規則鎖定 TCP 埠 9000、TCP 埠 22 和 UDP 埠 1194 的流量。

在 AKS 中，有兩組埠和位址：

* [AKS 群集所需的埠和位址](#required-ports-and-addresses-for-aks-clusters)詳細說明了授權出口流量的最低要求。
* [AKS 群集的可選建議位址和埠](#optional-recommended-addresses-and-ports-for-aks-clusters)並非所有方案都是必需的，但與其他服務（如 Azure 監視器）的集成將無法正常工作。 查看可選埠和 FQDN 的清單，並授權 AKS 群集中使用的任何服務和元件。

> [!NOTE]
> 限制出口流量僅適用于新的 AKS 群集。 對於現有群集，在限制出口流量之前，請使用`az aks upgrade`命令[執行群集升級操作][aks-upgrade]。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 叢集所需的連接埠和位址

AKS 群集需要以下出站埠/網路規則：

* TCP 埠*443*
* 如果您有一個需要與 API 伺服器對話的應用，則需要 TCP [IPAddrofapiServer]：443。  可以在創建群集後設置此更改。
* TCP 埠*9000、TCP*埠*22*和 UDP 埠*1194，* 用於隧道前艙與 API 伺服器上的隧道端通信。
    * 要獲得更具體的瞭解，請參閱 @*.hcp。\<位置\>.azmk8s.io*和 **.tun。\<下表\>中的位置 .azmk8s.io*位址。
* 用於網路時間協定 （NTP） 時間同步（Linux 節點）的 UDP 埠*123。*
* 如果您有直接存取 API 伺服器的 pod，則還需要用於 DNS 的 UDP 埠*53。*

需要以下 FQDN / 應用程式規則：

> [!IMPORTANT]
> ***.blob.core.windows.net和aksrepos.azurecr.io**不再需要 FQDN 規則即可進行出口鎖定。  對於現有群集，使用`az aks upgrade`命令[執行群集升級操作][aks-upgrade]以刪除這些規則。

> [!IMPORTANT]
> *.cdn.mscr.io已替換為 Azure 公共雲區域的 *.data.mcr.microsoft.com。 請升級現有的防火牆規則，以便更改生效。

- Azure 全域

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.azmk8s.io | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| \.tun.\<位置\>.azmk8s.io | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| *.cdn.mscr.io       | HTTPS：443 | 此位址對於 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| mcr.microsoft.com          | HTTPS：443 | 訪問 Microsoft 容器註冊表 （MCR） 中的圖像需要此位址。 此註冊表包含群集在升級和擴展期間運行群集所需的第一方圖像/圖表（例如，moby 等） |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此位址對於由 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| management.azure.com       | HTTPS：443 | 庫伯內斯 GET/PUT 操作需要此位址。 |
| login.microsoftonline.com  | HTTPS：443 | Azure 活動目錄身份驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 此位址是 Linux 節點上的 NTP 時間同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於緩存*的 apt-get*操作的 Microsoft 包存儲庫。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此位址用於安裝所需的二進位檔案（如 kubenet 和 Azure CNI）所需的存儲庫。 |

- Azure China 21Vianet

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.cx.prod.service.azk8s.cn | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| \.tun.\<位置\>.cx.prod.service.azk8s.cn | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| *.azk8s.cn        | HTTPS：443 | 下載所需的二進位檔案和圖像需要此位址|
| mcr.microsoft.com          | HTTPS：443 | 訪問 Microsoft 容器註冊表 （MCR） 中的圖像需要此位址。 此註冊表包含群集在升級和擴展期間運行群集所需的第一方圖像/圖表（例如，moby 等） |
| *.cdn.mscr.io       | HTTPS：443 | 此位址對於 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此位址對於由 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| management.chinacloudapi.cn       | HTTPS：443 | 庫伯內斯 GET/PUT 操作需要此位址。 |
| login.chinacloudapi.cn  | HTTPS：443 | Azure 活動目錄身份驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 此位址是 Linux 節點上的 NTP 時間同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於緩存*的 apt-get*操作的 Microsoft 包存儲庫。  示例包包括 Moby、PowerShell 和 Azure CLI。 |

- Azure Government

| FQDN                       | 連接埠      | 使用      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.cx.aks.containerservice.azure.us | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| \.tun.\<位置\>.cx.aks.containerservice.azure.us | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 節點<> API 伺服器通信需要此位址。 將*\<位置\>* 替換為部署 AKS 群集的區域。 |
| mcr.microsoft.com          | HTTPS：443 | 訪問 Microsoft 容器註冊表 （MCR） 中的圖像需要此位址。 此註冊表包含群集在升級和擴展期間運行群集所需的第一方圖像/圖表（例如，moby 等） |
|*.cdn.mscr.io              | HTTPS：443 | 此位址對於 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此位址對於由 Azure 內容傳遞網路 （CDN） 支援的 MCR 存儲是必需的。 |
| management.usgovcloudapi.net       | HTTPS：443 | 庫伯內斯 GET/PUT 操作需要此位址。 |
| login.microsoftonline.us  | HTTPS：443 | Azure 活動目錄身份驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 此位址是 Linux 節點上的 NTP 時間同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此位址是用於緩存*的 apt-get*操作的 Microsoft 包存儲庫。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此位址用於安裝所需的二進位檔案（如 kubenet 和 Azure CNI）所需的存儲庫。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 群集的可選推薦位址和埠

以下出站埠/網路規則對於 AKS 群集是可選的：

建議以下 FQDN / 應用程式規則使 AKS 群集正常運行：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com，azure.archive.ubuntu.com，changelogs.ubuntu.com | HTTP：80   | 此位址允許 Linux 叢集節點下載所需的安全修補程式和更新。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>啟用 GPU 的 AKS 群集所需的位址和埠

啟用 GPU 的 AKS 群集需要以下 FQDN / 應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS：443 | 此位址用於在基於 GPU 的節點上正確安裝和操作驅動程式。 |
| us.download.nvidia.com | HTTPS：443 | 此位址用於在基於 GPU 的節點上正確安裝和操作驅動程式。 |
| apt.dockerproject.org | HTTPS：443 | 此位址用於在基於 GPU 的節點上正確安裝和操作驅動程式。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>啟用了啟用容器的 Azure 監視器所需的位址和埠

啟用容器的 Azure 監視器的 AKS 群集需要以下 FQDN / 應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS：443    | 這是用於使用 Azure 監視器進行正確的指標和監視遙測。 |
| *.ods.opinsights.azure.com    | HTTPS：443    | Azure 監視器使用此選項來引入日誌分析資料。 |
| *.oms.opinsights.azure.com | HTTPS：443 | 此位址由 omsagent 使用，用於對日誌分析服務進行身份驗證。 |
|*.microsoftonline.com | HTTPS：443 | 這用於驗證指標並將其發送到 Azure 監視器。 |
|*.monitoring.azure.com | HTTPS：443 | 這用於向 Azure 監視器發送指標資料。 |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>啟用 Azure 開發空間所需的位址和埠

啟用 Azure 開發空間的 AKS 群集需要以下 FQDN / 應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 此位址用於拉 linux 高山和其他 Azure 開發人員空間圖像 |
| gcr.io | HTTP：443 | 此位址用於拉舵/牽引器圖像 |
| storage.googleapis.com | HTTP：443 | 此位址用於拉舵/牽引器圖像 |
| 阿茲茲-\<吉德\>.\<位置\>.azds.io | HTTPS：443 | 與控制器的 Azure 開發空間後端服務進行通信。 確切的 FQDN 可在 %USERPROFILE%\.azds_s_json 中的"資料平面Fqdn"中找到。 |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>啟用 Azure 策略（在公共預覽版中）的 AKS 群集所需的位址和埠

> [!CAUTION]
> 以下部分功能處於預覽狀態。  隨著功能移動到公共預覽和將來發佈階段，本文中的建議可能會發生變化。

對於啟用 Azure 策略的 AKS 群集，需要以下 FQDN / 應用程式規則。

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS：443 | 此位址用於正確操作 Azure 策略。 （當前在 AKS 預覽版中） |
| raw.githubusercontent.com | HTTPS：443 | 此位址用於從 GitHub 中提取內置策略，以確保 Azure 策略的正確操作。 （當前在 AKS 預覽版中） |
| \.gk.\<位置\>.azmk8s.io | HTTPS：443    | Azure 策略載入項，該載入項與在主伺服器中運行的網守審核終結點對話，以獲得審核結果。 |
| dc.services.visualstudio.com | HTTPS：443 | 向應用程式見解終結點發送遙測資料的 Azure 策略載入項。 |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>啟用基於 Windows 伺服器的節點（在公共預覽中）所需的

> [!CAUTION]
> 以下部分功能處於預覽狀態。  隨著功能移動到公共預覽和將來發佈階段，本文中的建議可能會發生變化。

基於 Windows 伺服器 AKS 群集需要以下 FQDN / 應用程式規則：

| FQDN                                    | 連接埠      | 使用      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net、winlayers.blob.core.windows.net、winlayers.cdn.mscr.io、go.microsoft.com | HTTPS：443 | 安裝與視窗相關的二進位檔案 |
| mp.microsoft.com，www.msftconnecttest.com，ctldl.windowsupdate.com<span> </span> | HTTP：80 | 安裝與視窗相關的二進位檔案 |
| kms.core.windows.net | TCP：1688 | 安裝與視窗相關的二進位檔案 |


## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了如果限制群集的出口流量，允許哪些埠和位址。 您還可以定義窗格本身如何進行通信，以及它們在群集中有哪些限制。 有關詳細資訊，請參閱使用[AKS 中的網路原則在 pod 之間安全流量][network-policy]。

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
