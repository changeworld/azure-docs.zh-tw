---
title: 針對登錄的網路問題進行疑難排解
description: 存取虛擬網路或防火牆後方的 Azure container registry 時，常見問題的徵兆、原因和解決方式
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 95b32b839d1b3b804a2035b797e1146a09d5236a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351788"
---
# <a name="troubleshoot-network-issues-with-registry"></a>針對登錄的網路問題進行疑難排解

本文可協助您針對存取虛擬網路或防火牆後方的 Azure container registry 時可能遇到的問題進行疑難排解。 

## <a name="symptoms"></a>徵狀

可能包含下列一或多項：

* 無法推送或提取映射，因此您收到錯誤 `dial tcp: lookup myregistry.azurecr.io`
* 無法推送或提取映射，而且您收到 Azure CLI 錯誤 `Could not connect to the registry login server`
* 無法將映射從登錄提取到 Azure Kubernetes Service 或另一個 Azure 服務
* 無法存取 HTTPS proxy 後方的登錄，您收到錯誤 `Error response from daemon: login attempt failed with status: 403 Forbidden`
* 無法設定虛擬網路設定，而且您收到錯誤 `Failed to save firewall and virtual network settings for container registry`
* 無法存取或查看 Azure 入口網站中的登錄設定，或使用 Azure CLI 管理登錄
* 無法新增或修改虛擬網路設定或公用存取規則
* ACR 工作無法推送或提取映射
* Azure 資訊安全中心無法掃描登錄中的映射，或掃描結果不會出現在 Azure 資訊安全中心

## <a name="causes"></a>原因

* 用戶端防火牆或 proxy 會防止存取- [解決方案](#configure-client-firewall-access)
* 登錄上的公用網路存取規則阻礙存取- [解決方案](#configure-public-access-to-registry)
* 虛擬網路設定可防止存取- [解決方案](#configure-vnet-access)
* 您嘗試將 Azure 資訊安全中心或某些其他 Azure 服務與具有私人端點、服務端點或公用 IP 存取規則的登錄整合- [解決方案](#configure-service-access)

## <a name="further-diagnosis"></a>進一步診斷 

執行 [az acr check health](/cli/azure/acr#az-acr-check-health) 命令以取得登錄環境健康情況的詳細資訊，並選擇性地存取目標登錄。 例如，診斷特定的網路連線能力或設定問題。 

如需命令範例，請參閱 [檢查 Azure container registry 的健康情況](container-registry-check-health.md) 。 如果報告錯誤，請參閱 [錯誤參考](container-registry-health-error-reference.md) 和下列各節，以取得建議的解決方案。

> [!NOTE]
> 當登錄驗證或授權發生問題時，也可能會發生一些網路連線徵兆。 請參閱 [疑難排解登錄登](container-registry-troubleshoot-login.md)入。

## <a name="potential-solutions"></a>可能的解決方案

### <a name="configure-client-firewall-access"></a>設定用戶端防火牆存取

若要從用戶端防火牆或 proxy 伺服器後方存取登錄，請設定防火牆規則來存取登錄的公用 REST 和資料端點。 如果已啟用 [專用資料端點](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) ，您需要可存取的規則：

* REST 端點： `<registryname>.azurecr.io`
* Data endpoint (s) ： `<registry-name>.<region>.data.azurecr.io`

針對異地複寫的登錄，請針對每個區域複本設定資料端點的存取權。

在 HTTPS proxy 後方，確定您的 Docker 用戶端和 Docker daemon 都已針對 proxy 行為進行設定。

ContainerRegistryLoginEvents 資料表中的登錄資源記錄檔可協助診斷已封鎖的已嘗試連接。

相關連結：

* [設定可以從防火牆後方存取 Azure 容器登錄的規則](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS proxy 設定](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [異地 replicationin Azure Container Registry](container-registry-geo-replication.md)
* [用於診斷評估和審核的 Azure Container Registry 記錄](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>設定登錄的公用存取

如果透過網際網路存取登錄，請確認登錄允許來自您用戶端的公用網路存取。 根據預設，Azure container registry 可讓您從所有網路存取公用登錄端點。 登錄可以限制對選取的網路或選取的 IP 位址的存取。 

如果已針對具有服務端點的虛擬網路設定登錄，則停用公用網路存取也會停用對服務端點的存取。 如果您的登錄設定為具有 Private Link 的虛擬網路，則 IP 網路規則不會套用至登錄的私人端點。 

相關連結：

* [設定公用 IP 網路規則](container-registry-access-selected-networks.md)
* [使用 Azure Private Link 私下連接到 Azure container registry](container-registry-private-link.md)
* [使用 Azure 虛擬網路中的服務端點來限制對容器登錄的存取](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>設定 VNet 存取

確認虛擬網路已設定 Private Link 的私人端點，或 (preview) 的服務端點。 目前不支援 Azure 防禦端點。

請參閱用來將網路中其他資源的流量限制為登錄的 NSG 規則和服務標記。 

如果已設定登錄的服務端點，請確認已在登錄中新增網路規則，以允許從該網路子網進行存取。 服務端點僅支援從虛擬機器和網路中的 AKS 叢集進行存取。

如果您想要使用不同 Azure 訂用帳戶中的虛擬網路來限制登錄存取，請確定您已 `Microsoft.ContainerRegistry` 在該訂用帳戶中註冊資源提供者。 使用 Azure 入口網站、Azure CLI 或其他 Azure 工具，為 Azure Container Registry[註冊資源提供者](../azure-resource-manager/management/resource-providers-and-types.md)。

如果網路中已設定 Azure 防火牆或類似的解決方案，請檢查來自其他資源（例如 AKS 叢集）的輸出流量是否已啟用，以連接到登錄端點。

如果已設定私人端點，請確認 DNS 將登錄的公用 FQDN （例如 *myregistry.azurecr.io* ）解析為登錄的私人 IP 位址。 使用網路公用程式（例如 `dig` 或） `nslookup` 進行 DNS 查閱。

相關連結：

* [使用 Azure Private Link 私下連接到 Azure container registry](container-registry-private-link.md)
* [使用 Azure 虛擬網路中的服務端點來限制對容器登錄的存取](container-registry-vnet.md)
* [AKS 叢集所需的輸出網路規則和 Fqdn](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes：調試 DNS 解析](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [虛擬網路服務標籤](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>設定服務存取

目前，Azure 資訊安全中心無法在登錄中執行 [映射弱點掃描](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) ，以限制對私人端點、選取的子網或 IP 位址的存取。 此外，下列服務的資源無法存取具有網路限制的容器登錄：

* Azure DevOps Services 
* Azure Container Instances
* Azure Container Registry 工作

如果需要將這些 Azure 服務與您的容器登錄進行存取或整合，請移除網路限制。 例如，移除登錄的私人端點，或移除或修改登錄的公用存取規則。

相關連結：

* [Azure Container Registry 由安全中心掃描影像](../security-center/defender-for-container-registries-introduction.md)
* 提供[意見](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)反應
* [設定公用 IP 網路規則](container-registry-access-selected-networks.md)
* [使用 Azure Private Link 私下連接到 Azure container registry](container-registry-private-link.md)


## <a name="advanced-troubleshooting"></a>進階疑難排解

如果已在登錄中啟用 [資源記錄檔收集](container-registry-diagnostics-audit-logs.md) ，請檢查 ContainterRegistryLoginEvents 記錄檔。 此記錄儲存驗證事件和狀態，包括傳入的身分識別和 IP 位址。 查詢記錄檔以取得登錄 [驗證失敗](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)。 

相關連結：

* [診斷評估和審核的記錄](container-registry-diagnostics-audit-logs.md)
* [Container registry 常見問題](container-registry-faq.md)
* [適用于 Azure Container Registry 的 Azure 安全性基準](security-baseline.md)
* [Azure Container Registry 的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有解決問題，請參閱下列選項。

* 其他登錄疑難排解主題包括：
  * [針對登錄登入進行疑難排解](container-registry-troubleshoot-login.md) 
  * [針對登錄效能進行疑難排解](container-registry-troubleshoot-performance.md)
* [社區支援](https://azure.microsoft.com/support/community/) 選項
* [Microsoft Q&A](/answers/products/)
* [開啟支援票證](https://azure.microsoft.com/support/create-ticket/)