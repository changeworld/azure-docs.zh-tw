---
title: Windows Server node pool 常見問題
titleSuffix: Azure Kubernetes Service
description: 當您在 Azure Kubernetes Service (AKS) 中執行 Windows Server 節點集區和應用程式工作負載時，請參閱常見問題。
services: container-service
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: df9a4dd546ddc5944d9a282e74c2444a5161b862
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927546"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>AKS 中 Windows Server 節點集區的常見問題

在 Azure Kubernetes Service (AKS) 中，您可以建立執行 Windows Server 的節點集區，做為節點上的客體作業系統。 這些節點可以執行原生 Windows 容器應用程式，例如 .NET Framework 上建的應用程式。 Linux 和 Windows 作業系統提供容器支援的方式有所差異。 Windows 節點集區目前無法使用一些常見的 Linux Kubernetes 和 pod 相關功能。

本文概述 AKS 中 Windows Server 節點的一些常見問題和 OS 概念。

## <a name="which-windows-operating-systems-are-supported"></a>支援哪些 Windows 作業系統？

AKS 使用 Windows Server 2019 作為主機 OS 版本，且僅支援進程隔離。 不支援使用其他 Windows Server 版本所建立的容器映射。 如需詳細資訊，請參閱 [Windows 容器版本相容性][windows-container-compat]。

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows 和 Linux 上的 Kubernetes 是否不同？

視窗伺服器節點集區支援包含一些屬於 Kubernetes project 中上游 Windows Server 一部分的限制。 這些限制並不是 AKS 特有的。 如需有關此 Kubernetes 中 Windows Server 上游支援的詳細資訊，請參閱 Kubernetes 專案中有關 Kubernetes 檔[中的 windows 支援簡介][intro-windows]的[支援功能和限制][upstream-limitations]一節。

Kubernetes 在過去是以 Linux 為焦點。 上游 [Kubernetes.io][kubernetes] 網站中使用的許多範例都適用于 Linux 節點。 當您建立使用 Windows Server 容器的部署時，適用下列作業系統層級的考慮：

- 身分**識別-Linux**會依 (UID) 的整數使用者識別碼來識別使用者。 使用者也會有用於登入的英數位元使用者名稱，而 Linux 會將其轉譯為使用者的 UID。 同樣地，Linux 會依整數群組識別碼 (GID) 來識別使用者群組，並將組名轉譯為其對應的 GID。
    - Windows Server 會使用較大的二進位安全識別碼 (SID) ，此識別碼儲存在 Windows 安全性存取管理員 (SAM) 資料庫中。 此資料庫不會在主機與容器之間共用，或在容器之間共用。
- 檔案**許可權**-Windows Server 使用以 sid 為基礎的存取控制清單，而不是許可權的位元遮罩和 UID + GID
- 檔案**路徑**-Windows Server 上的慣例是使用 \ 而非/。
    - 在掛接磁片區的 pod 規格中，為 Windows Server 容器指定正確的路徑。 例如，您可以指定磁碟機號和位置（例如 */K/Volume* ）作為*K：* 磁片磁碟機，而不是 Linux 容器中的 */mnt/volume*掛接點。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支援何種磁片？

Azure 磁片和 Azure 檔案儲存體是支援的磁片區類型。 這些是在 Windows Server 容器中以 NTFS 磁片區的方式存取。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>我可以只在 AKS 中執行 Windows 叢集嗎？

AKS 叢集中 (控制平面) 的主要節點是由 AKS 服務所裝載，您將不會被公開至裝載主要元件之節點的作業系統。 所有 AKS 叢集都是使用預設的第一個節點集區（以 Linux 為基礎）建立的。 此節點集區包含叢集運作所需的系統服務。 建議您在第一個節點集區中執行至少兩個節點，以確保叢集的可靠性和執行叢集作業的能力。 除非 AKS 叢集本身已刪除，否則無法刪除第一個以 Linux 為基礎的節點集區。

## <a name="how-do-i-patch-my-windows-nodes"></a>如何? 修補我的 Windows 節點？

若要取得 Windows 節點的最新修補程式，您可以 [升級節點集][nodepool-upgrade] 區或 [升級節點映射][upgrade-node-image]。 AKS 中的節點上未啟用 Windows Update。 AKS 會在修補程式可用時立即發行新的節點集區映射，而使用者必須負責升級節點集區，以保持最新的修補程式和修補程式。 這也適用于所使用的 Kubernetes 版本。 [AKS 版本][aks-release-notes] 資訊指出有新版本可供使用。 如需有關升級整個 Windows Server 節點集區的詳細資訊，請參閱 [在 AKS 中升級節點集][nodepool-upgrade]區。 如果您只想要更新節點映射，請參閱 [AKS 節點映射升級][upgrade-node-image]。

> [!NOTE]
> 只有在升級節點集區之前，已執行 (控制平面升級) 的叢集升級時，才會使用更新的 Windows Server 映射。
>

## <a name="what-network-plug-ins-are-supported"></a>支援哪些網路外掛程式？

具有 Windows 節點集區的 AKS 叢集必須使用 Azure CNI (advanced) 網路模型。 不支援 Kubenet (基本) 網路。 如需網路模型差異的詳細資訊，請參閱 [AKS 中的應用程式網路概念][azure-network-models]。 Azure CNI 網路模型需要針對 IP 位址管理進行額外的規劃和考慮。 如需有關如何規劃和執行 Azure CNI 的詳細資訊，請參閱 [在 AKS 中設定 AZURE CNI 網路][configure-azure-cni]。

## <a name="is-preserving-the-client-source-ip-supported"></a>是否要保留用戶端來源 IP 的支援？

在此期間，Windows 節點不支援 [用戶端來源 IP 保留][client-source-ip] 。

## <a name="can-i-change-the-max--of-pods-per-node"></a>我可以變更最大值每個節點的 pod 數目為何？

是。 如需可用的含意和選項，請參閱 pod [數目上限][maximum-number-of-pods]。

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>當我嘗試建立新的 Windows 代理程式組件區時，為什麼會看到錯誤？

如果您在2020年2月之前建立叢集，而且從未完成任何叢集升級作業，叢集仍會使用舊的 Windows 映像。 您可能會看到類似下列的錯誤：

「找不到下列從部署範本參考的映射清單：發行者： MicrosoftWindowsServer、供應專案： WindowsServer、Sku： 2019-datacenter-core-smalldisk-2004、Version：最新版本。 https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage如需尋找可用影像的指示，請參閱。

若要修正此錯誤：

1. 升級叢集 [控制平面][upgrade-cluster-cp] ，以更新映射供應專案和發行者。
1. 建立新的 Windows 代理程式組件區。
1. 將 Windows pod 從現有的 Windows 代理程式組件區移至新的 Windows 代理程式組件區。
1. 刪除舊的 Windows 代理程式組件區。

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何? 輪替 Windows 節點集區的服務主體？

Windows 節點集區不支援服務主體輪替。 若要更新服務主體，請建立新的 Windows 節點集區，並將您的 pod 從較舊的集區遷移至新的集區。 完成之後，請刪除較舊的節點集區。

相反地，請使用受控識別，基本上是服務主體周圍的包裝函式。 如需詳細資訊，請參閱[在 Azure Kubernetes Service 中使用受控識別][managed-identity] \(部分機器翻譯\)。

## <a name="how-many-node-pools-can-i-create"></a>我可以建立多少個節點集區？

AKS 叢集最多可以有 10 個節點集區。 在這些節點集區中，您最多可以有1000個節點。 [節點集區限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以將 Windows 節點集區命名為什麼？

您必須將名稱最多保留 6 (6) 個字元。 這是目前的 AKS 限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 節點是否支援所有功能？

Windows 節點目前不支援網路原則和 kubenet。

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>我可以在 Windows 節點上執行輸入控制器嗎？

是，支援 Windows Server 容器的輸入控制器可以在 AKS 的 Windows 節點上執行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>我可以搭配 Windows 節點使用 Azure Dev Spaces 嗎？

Azure Dev Spaces 目前僅適用于以 Linux 為基礎的節點集區。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows Server 容器可以使用 gMSA 嗎？

GMSA 目前無法在 AKS 中使用群組受管理的服務帳戶 () 支援。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>可以針對具有 Windows 節點和容器的容器使用 Azure 監視器嗎？

是的，但 Azure 監視器是公開預覽，可從 Windows 容器 (stdout、stderr) 和計量收集記錄。 您也可以從 Windows 容器附加至 stdout 記錄的即時串流。

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>具有 Windows 節點之叢集上的服務數目有任何限制嗎？

具有 Windows 節點的叢集在遇到埠耗盡之前，可以有大約500的服務。

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>我可以搭配使用 Kubernetes Web 儀表板與 Windows 容器嗎？

是，您可以使用 [Kubernetes Web 儀表板][kubernetes-dashboard] 來存取 Windows 容器的相關資訊，但此時您無法直接從 Kubernetes Web 儀表板將 *kubectl exec* 執行至執行中的 Windows 容器。 如需有關連接到執行中 Windows 容器的詳細資訊，請參閱 [使用 RDP 連線至 Azure Kubernetes Service (AKS) 叢集 Windows Server 節點以進行維護或疑難排解][windows-rdp]。

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>如果我需要不支援的功能，該怎麼辦？

我們致力於在 AKS 中提供 Windows 所需的所有功能，但如果您遇到間距，開放原始碼的上游 [AKS 引擎][aks-engine] 專案可提供在 Azure 中執行 Kubernetes 的簡單、完全自訂的方式，包括 Windows 支援。 請務必查看我們即將推出的 [AKS 藍圖][aks-roadmap]功能藍圖。

## <a name="next-steps"></a>後續步驟

若要在 AKS 中開始使用 Windows Server 容器，請 [在 AKS 中建立執行 Windows server 的節點集][windows-node-cli]區。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
