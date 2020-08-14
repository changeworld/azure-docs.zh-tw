---
title: 針對登錄的網路問題進行疑難排解
description: 存取虛擬網路或防火牆後方的 Azure container registry 時的徵兆、原因和解決常見問題
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 227eeeadb2aef4b4d3feb7923a198b129a6267d3
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227104"
---
# <a name="troubleshoot-network-issues-with-registry"></a>針對登錄的網路問題進行疑難排解

本文可協助您針對在虛擬網路或防火牆後方存取 Azure container registry 時可能遇到的問題進行疑難排解。 

## <a name="symptoms"></a>徵狀

可能包含下列一或多項：

* 無法推送或提取映射，而且您收到錯誤 `dial tcp: lookup myregistry.azurecr.io`
* 無法推送或提取映射，而且您收到 Azure CLI 錯誤 `Could not connect to the registry login server`
* 無法將映射從登錄提取到 Azure Kubernetes Service 或另一個 Azure 服務
* 無法存取 HTTPS proxy 後方的登錄，且您收到錯誤 `Error response from daemon: login attempt failed with status: 403 Forbidden`
* 無法存取或查看 Azure 入口網站中的登錄設定，或使用 Azure CLI 管理登錄
* 無法新增或修改虛擬網路設定或公用存取規則
* ACR 工作無法推送或提取映射
* Azure 資訊安全中心無法掃描登錄中的影像，或掃描結果不會出現在 Azure 資訊安全中心

## <a name="causes"></a>原因

* 用戶端防火牆或 proxy 會阻止存取 [解決方案](#configure-client-firewall-access)
* 登錄上的公用網路存取規則會阻止存取- [解決方案](#configure-public-access-to-registry)
* 虛擬網路設定可防止存取- [解決方案](#configure-vnet-access)
* 您嘗試整合 Azure 資訊安全中心與具有私人端點或服務端點的登錄- [解決方案](#configure-image-scanning-solution)

## <a name="further-diagnosis"></a>進一步診斷 

執行 [az acr check-health](/cli/azure/acr#az-acr-check-health) 命令，以取得登錄環境健全狀況的詳細資訊，並選擇性地存取目標登錄。 例如，診斷特定的網路連線或設定問題。 

如需命令範例，請參閱 [檢查 Azure container registry 的健全狀況](container-registry-check-health.md) 。 如果報告錯誤，請參閱 [錯誤參考](container-registry-health-error-reference.md) 和下列各節，以取得建議的解決方案。

> [!NOTE]
> 當登錄驗證或授權有問題時，也可能會發生一些網路連線徵兆。 請參閱針對 [登錄登入進行疑難排解](container-registry-troubleshoot-login.md)。

## <a name="potential-solutions"></a>可能的解決方案

### <a name="configure-client-firewall-access"></a>設定用戶端防火牆存取

若要從用戶端防火牆或 proxy 伺服器後方存取登錄，請設定防火牆規則來存取登錄的 REST 和資料端點。 如果已啟用 [專用的資料端點](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) ，您需要存取下列規則：

* REST 端點： `<registryname>.azurecr.io`
* 資料端點 (s) ： `<registry-name>.<region>.data.azurecr.io`

針對異地複寫的登錄，設定每個區域複本的資料端點存取權。

在 HTTPS proxy 後方，請確定您的 Docker 用戶端和 Docker daemon 都已設定 proxy 行為。

ContainerRegistryLoginEvents 資料表中的登錄資源記錄可能有助於診斷已封鎖的嘗試連接。

相關連結：

* [設定可以從防火牆後方存取 Azure 容器登錄的規則](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS proxy 設定](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [異地 replicationin Azure Container Registry](container-registry-geo-replication.md)
* [用於診斷評估和審核的 Azure Container Registry 記錄](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>設定對登錄的公用存取

如果透過網際網路存取登錄，請確認登錄允許來自您用戶端的公用網路存取。 根據預設，Azure container registry 可讓您從所有網路存取公用登錄端點。 登錄可以限制對選取的網路或選取的 IP 位址的存取。 

如果已針對具有服務端點的虛擬網路設定登錄，則停用公用網路存取也會停用透過服務端點的存取。 如果您的登錄是針對具有私人連結的虛擬網路所設定，則 IP 網路規則不會套用至登錄的私人端點。 

相關連結：

* [設定公用 IP 網路規則](container-registry-access-selected-networks.md)
* [使用 Azure 私人連結私下連接到 Azure container registry](container-registry-private-link.md)
* [使用 Azure 虛擬網路中的服務端點來限制對容器登錄的存取](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>設定 VNet 存取

確認虛擬網路已設定私用端點私人連結或服務端點 (預覽) 。 目前不支援 Azure 防禦端點。

請參閱用來將網路中其他資源的流量限制為登錄的 NSG 規則和服務標記。 

如果已設定登錄的服務端點，請確認已將網路規則新增至允許從該網路子網存取的登錄。 服務端點僅支援從網路中的虛擬機器和 AKS 叢集存取。

如果在網路中設定了 Azure 防火牆或類似的解決方案，請檢查來自其他資源（例如 AKS 叢集）的輸出流量是否已啟用以連線到登錄端點。

相關連結：

* [使用 Azure 私人連結私下連接到 Azure container registry](container-registry-private-link.md)
* [使用 Azure 虛擬網路中的服務端點來限制對容器登錄的存取](container-registry-vnet.md)
* [AKS 叢集所需的輸出網路規則和 Fqdn](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes：調試 DNS 解析](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [虛擬網路服務標籤](../virtual-network/service-tags-overview.md)

### <a name="configure-image-scanning-solution"></a>設定影像掃描解決方案

如果您的登錄是以私人端點或服務端點來設定，您目前無法與 Azure 資訊安全中心進行映射掃描的整合。 （選擇性）設定可在 Azure Marketplace 中使用的其他影像掃描解決方案，包括：

* [淺綠色雲端原生安全性平臺](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security)
* [Twistlock Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock)

相關連結：

* [資訊安全中心 Azure Container Registry 影像掃描](../security-center/azure-container-registry-integration.md)
* 提供[意見](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)反應


## <a name="advanced-troubleshooting"></a>進階疑難排解

如果已在登錄中啟用 [資源記錄的收集](container-registry-diagnostics-audit-logs.md) ，請檢查 ContainterRegistryLoginEvents 記錄。 此記錄會儲存驗證事件和狀態，包括傳入身分識別和 IP 位址。 查詢記錄檔中的登錄 [驗證失敗](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)。 

相關連結：

* [診斷評估與審核的記錄](container-registry-diagnostics-audit-logs.md)
* [Container registry 常見問題](container-registry-faq.md)
* [適用于 Azure Container Registry 的 Azure 安全性基準](security-baseline.md)
* [Azure Container Registry 的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>後續步驟

如果您沒有在這裡解決問題，請參閱下列選項。

* 其他登錄疑難排解主題包括：
  * [針對登錄登入進行疑難排解](container-registry-troubleshoot-login.md) 
  * [針對登錄效能進行疑難排解](container-registry-troubleshoot-performance.md)
* [社區支援](https://azure.microsoft.com/support/community/) 選項
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [開啟支援票證](https://azure.microsoft.com/support/create-ticket/)


