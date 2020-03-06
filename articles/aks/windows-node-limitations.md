---
title: Azure Kubernetes Service 中 Windows Server 節點集區的限制（AKS）
description: 瞭解在 Azure Kubernetes Service 中執行 Windows Server 節點集區和應用程式工作負載時的已知限制（AKS）
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 65c62324a27e8377a1cc9833595b15cf08c6c820
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298167"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 中 Windows Server 節點集區和應用程式工作負載目前的限制（AKS）

在 Azure Kubernetes Service （AKS）中，您可以建立執行 Windows Server 的節點集區，做為節點上的客體作業系統。 這些節點可以執行原生 Windows 容器應用程式，例如建立在 .NET Framework 上的。 因為 Linux 和 Windows OS 如何提供容器支援的主要差異，所以某些常見的 Kubernetes 和 pod 相關功能目前不適用於 Windows 節點集區。

本文概述 AKS 中 Windows Server 節點的一些限制和 OS 概念。 適用于 Windows Server 的節點集區目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>支援哪些 Windows 作業系統？

AKS 會使用 Windows Server 2019 做為主機作業系統版本，而且只支援進程隔離。 不支援使用其他 Windows Server 版本建立的容器映射。 [Windows 容器版本相容性][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows 和 Linux 上的 Kubernetes 不同嗎？

Windows Server 節點集區支援包含在 Kubernetes 專案中屬於上游 Windows Server 的一些限制。 這些限制並不是 AKS 特有的。 如需有關此 Kubernetes 中 Windows Server 之上游支援的詳細資訊，請參閱 Kubernetes 專案中的 < [Kubernetes 中的 windows 支援簡介][intro-windows]檔中的[支援的功能和限制][upstream-limitations]一節。

Kubernetes 在過去是以 Linux 為重點。 上游[Kubernetes.io][kubernetes]網站中使用的許多範例都是供 Linux 節點使用。 當您建立使用 Windows Server 容器的部署時，適用于 OS 層級的下列考慮事項：

- 身分**識別**-Linux 會依照整數使用者識別碼（UID）來識別使用者。 使用者也會有用來登入的英數位元使用者名稱，而 Linux 會將其轉譯為使用者的 UID。 同樣地，Linux 會以整數群組識別碼（GID）識別使用者群組，並將組名轉譯成其對應的 GID。
    - Windows Server 使用較大的二進位安全識別碼（SID），儲存在 Windows 安全性存取管理員（SAM）資料庫中。 此資料庫不會在主機與容器之間共用，或在容器之間共用。
- 檔案**許可權**-Windows Server 使用以 sid 為基礎的存取控制清單，而不是許可權和 UID + GID 的位元遮罩
- 檔案**路徑**-Windows Server 上的慣例是使用 \，而不是/。
    - 在 pod 規格中，載入磁片區，為 Windows Server 容器指定正確的路徑。 例如，不是 Linux 容器中 */mnt/volume*的掛接點，而是指定磁碟機號和位置（例如 */K/Volume* ）來掛接為*K：* 磁片磁碟機。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支援哪種磁片？

Azure 磁片和 Azure 檔案儲存體是支援的磁片區類型，以 Windows Server 容器中的 NTFS 磁片區來存取。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>我可以在 AKS 中執行僅限 Windows 的叢集嗎？

AKS 叢集中的主要節點（控制平面）是藉由 AKS 服務來裝載，而您不會公開至裝載主要元件之節點的作業系統。 所有 AKS 叢集都是使用預設的第一個節點集區（以 Linux 為基礎）來建立。 此節點集區包含叢集運作所需的系統服務。 建議您在第一個節點集區中至少執行兩個節點，以確保叢集的可靠性，以及執行叢集作業的能力。 除非 AKS 叢集本身已刪除，否則無法刪除第一個以 Linux 為基礎的節點集區。

## <a name="what-network-plug-ins-are-supported"></a>支援哪些網路外掛程式？

具有 Windows 節點集區的 AKS 叢集必須使用 Azure CNI （advanced）網路模型。 不支援 Kubenet （基本）網路功能。 如需網路模型差異的詳細資訊，請參閱[AKS 中應用程式的網路概念][azure-network-models]。 -Azure CNI 網路模型需要額外的 IP 位址管理規劃和考慮。 如需如何規劃和執行 Azure CNI 的詳細資訊，請參閱[在 AKS 中設定 AZURE CNI 網路][configure-azure-cni]。

## <a name="can-i-change-the-max--of-pods-per-node"></a>我可以變更最大值。每個節點的 pod 數目？

目前需要設定為最多30個 pod，以確保叢集的可靠性。

## <a name="how-do-patch-my-windows-nodes"></a>如何修補我的 Windows 節點？

AKS 中的 Windows Server 節點必須*升級*，才能取得最新的修補程式修正和更新。 AKS 中的節點上未啟用 Windows 更新。 一旦有可用的修補程式，AKS 就會發行新的節點集區映射，客戶必須負責升級節點集區，以保持最新的修補程式和修補程式。 這也適用于所使用的 Kubernetes 版本。 AKS 版本資訊會指出有新版本可用的時間。 如需有關升級 Windows Server 節點集區的詳細資訊，請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。

> [!NOTE]
> 只有在升級節點集區之前已執行叢集升級（控制平面升級）時，才會使用更新的 Windows Server 映射
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何? 輪替 Windows 節點集區的服務主體嗎？

在預覽期間，Windows 節點集區不支援服務主體輪替做為預覽限制。 若要更新服務主體，請建立新的 Windows 節點集區，並將您的 pod 從舊的集區遷移至新的集區。 完成之後，請刪除較舊的節點集區。

## <a name="how-many-node-pools-can-i-create"></a>我可以建立多少個節點集區？

AKS 叢集最多可以有10個節點集區。 在這些節點集區中，您最多可以有1000個節點。 [節點集區限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以將 Windows 節點集區命名為什麼？

您必須將名稱保留最多6（六個）個字元。 這是目前的 AKS 限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 節點支援所有功能嗎？

Windows 節點目前不支援網路原則和 kubenet。 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>我可以在 Windows 節點上執行輸入控制器嗎？

是，支援 Windows Server 容器的輸入控制器可以在 AKS 的 Windows 節點上執行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>我可以搭配 Windows 節點使用 Azure Dev Spaces 嗎？

Azure Dev Spaces 目前僅適用于以 Linux 為基礎的節點集區。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows Server 容器是否可以使用 gMSA？

AKS 目前無法使用群組受管理的服務帳戶（gMSA）支援。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>我可以將 Azure 監視器用於具有 Windows 節點和容器的容器嗎？

是的，不過 Azure 監視器不會從 Windows 容器收集記錄（stdout）。 您仍然可以從 Windows 容器附加到 stdout 記錄的即時串流。

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>如果我需要不支援的功能，該怎麼辦？

我們致力於將您需要的所有功能帶入 AKS，但如果您遇到了一些差距，開放原始碼的上游[AKS 引擎][aks-engine]專案提供了一種簡單且完全可自訂的方式，可在 Azure 中執行 Kubernetes，包括 Windows 支援。 請務必查看我們推出[AKS 藍圖][aks-roadmap]的功能藍圖。

## <a name="next-steps"></a>後續步驟

若要開始在 AKS 中使用 Windows Server 容器，請[在 AKS 中建立執行 Windows server 的節點集][windows-node-cli]區。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
