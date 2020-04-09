---
title: Windows 伺服器節點池限制
titleSuffix: Azure Kubernetes Service
description: 在 Azure Kubernetes 服務 (AKS) 中執行 Windows Server 節點池和應用程式工作負載時,瞭解已知的限制
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 934acf06a779c1c3b0b13e74b196b174dd944e66
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886665"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure 庫伯奈斯服務 (AKS) 中 Windows 伺服器節點池和應用程式工作負載的目前限制

在 Azure 庫伯奈斯服務 (AKS) 中,您可以創建一個節點池,該節點池在節點上作為來賓作業系統運行 Windows 伺服器。 這些節點可以運行本機 Windows 容器應用程式,例如在 .NET 框架上構建的應用程式。 由於 Linux 和 Windows 作業系統在提供容器支援的方式上存在重大差異,因此一些常見的 Kubernetes 和 pod 相關功能目前不適用於 Windows 節點池。

本文概述了 AKS 中 Windows 伺服器節點的一些限制和作業系統概念。 Windows 伺服器的節點池當前處於預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助服務加入宣告。 預覽版提供「根據」和「可用」,不在服務層級協定和有限保修中。 在盡力的基礎上,客戶支援部分覆蓋了 AKS 預覽。 因此,這些功能不適合生產用途。 有關詳細資訊,請參閱以下支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>支援哪些 Windows 作業系統?

AKS 使用 Windows Server 2019 作為主機作業系統版本,僅支援進程隔離。 不支援使用其他 Windows 伺服器版本生成的容器映射。 [Windows 容器版本相容性][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>庫伯內特在Windows和Linux上有什麼不同嗎?

視窗伺服器節點池支援包括一些限制,這些限制是 Kubernetes 專案中上游 Windows Server 的一部分。 這些限制並非特定於 AKS。 有關庫伯內斯中 Windows 伺服器的此上游支援的詳細資訊,請參閱[Kubernetes 專案中 Kubernetes][intro-windows]文檔中支援 Windows 支援簡介的功能[和限制][upstream-limitations]部分。

庫伯內斯在歷史上以Linux為中心。 上游[Kubernetes.io][kubernetes]網站中使用的許多範例都用於 Linux 節點。 建立使用 Windows Server 容器的部署時,作業系統等級的以下注意事項適用:

- **標識**- Linux 透過整數用戶識別碼 (UID) 識別使用者。 用戶還有用於登錄的字母數位使用者名,Linux 將該使用者名轉換為使用者的 UID。 同樣,Linux 通過整數組識別碼 (GID) 識別使用者組,並將組名稱轉換為相應的 GID。
    - Windows 伺服器使用儲存在 Windows 安全存取管理員 (SAM) 資料庫中的較大二進位安全識別子 (SID)。 此資料庫不會在主機和容器之間或容器之間共用。
- **檔案權限**- Windows 伺服器使用基於 S ID 的存取控制列表,而不是權限和 UID_GID 的位罩
- **檔路徑**- Windows 伺服器上的約定是使用 * 而不是 /。
    - 在裝載卷的窗格規範中,正確指定 Windows Server 容器的路徑。 例如,在 Linux 容器中指定驅動器號和位置(如 */K/Volume)* 以載入為*K:* 驅動器,而不是 Linux 容器中的 */mnt/卷*的裝載點。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支援哪種磁碟?

Azure 磁碟和 Azure 檔是受支援的卷類型,在 Windows Server 容器中作為 NTFS 卷訪問。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>是否可以僅在 AKS 中運行 Windows 群集?

AKS 群集中的主節點(控制平面)由 AKS 服務託管,您不會接觸到託管主元件的節點的作業系統。 所有 AKS 群集都使用預設的第一個節點池創建,該節點池基於 Linux。 此節點池包含群集正常運行所需的系統服務。 建議在第一個節點池中至少運行兩個節點,以確保群集的可靠性和進行群集操作的能力。 除非 AKS 群集本身被刪除,否則無法刪除第一個基於 Linux 的節點池。

## <a name="what-network-plug-ins-are-supported"></a>支援哪些網路外掛程式?

具有 Windows 節點池的 AKS 群集必須使用 Azure CNI(高級)網路模型。 不支援 Kubenet(基本)網路。 有關網路模型差異的詳細資訊,請參閱[AKS 中應用程式的網路概念][azure-network-models]。 - Azure CNI 網路模型需要額外的 IP 位址管理規劃和注意事項。 有關如何規劃和實現 Azure CNI 的詳細資訊,請參閱在[AKS 中設定 Azure CNI 網路][configure-azure-cni]。

## <a name="can-i-change-the-max--of-pods-per-node"></a>我可以更改最大值嗎?每個節點的窗格數?

是。 有關可用的含義和選項,請參閱[最大窗格數][maximum-number-of-pods]。

## <a name="how-do-patch-my-windows-nodes"></a>如何修補我的 Windows 節點?

必須*升級*AKS 中的 Windows 伺服器節點以獲取最新的修補程式和更新。 在 AKS 中的節點上未啟用 Windows 更新。 AKS 在修補程式可用後立即發佈新的節點池映像,客戶有責任升級節點池以在修補程式和修補程式上保持最新狀態。 對於正在使用的庫伯內斯版本也是如此。 AKS 發行說明將指示新版本何時可用。 有關升級 Windows 伺服器節點池的詳細資訊,請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

> [!NOTE]
> 僅當在升級節點池之前執行叢集升級(控制平面升級)時,才會使用更新的 Windows Server 映射
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何旋轉 Windows 節點池的服務主體?

在預覽期間,Windows 節點池不支援服務主體旋轉作為預覽限制。 為了更新服務主體,請創建新的 Windows 節點池,並將您的窗格從較舊的池遷移到新池。 完成此任務後,刪除較舊的節點池。

## <a name="how-many-node-pools-can-i-create"></a>我可以創建多少個節點池?

AKS 群集最多可以有 10 個節點池。 在這些節點池中最多可以有 1000 個節點。 [節點池限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以為 Windows 節點池命名什麼?

您必須將名稱保留為最多 6 (6) 個字元。 這是 AKS 的當前限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 節點支援所有功能嗎?

Windows 節點當前不支援網路策略和 kubenet。 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>是否可以在 Windows 節點上運行入口控制器?

是的,支援 Windows Server 容器的入口控制器可以在 AKS 中的 Windows 節點上運行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>是否可以將 Azure 開發空間與 Windows 節點一起使用?

Azure 開發人員空間目前僅適用於基於 Linux 的節點池。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows 伺服器容器可以使用 gMSA 嗎?

組託管服務帳戶 (gMSA) 支援目前不在 AKS 中。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>是否可以將 Azure 監視器用於具有 Windows 節點和容器的容器?

可以,但是 Azure 監視器不會從 Windows 容器收集日誌(stdout)。 您仍然可以從 Windows 容器附加到固定日誌的即時流。

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>如果我需要不支援的功能,該怎麼辦?

我們努力將您需要的所有功能帶到 AKS 中的 Windows,但如果您確實遇到差距,開源的上游[aks 引擎][aks-engine]專案提供了一種簡單且完全可自定義的方式在 Azure 中運行 Kubernetes,包括 Windows 支援。 請務必檢視我們的[AKS 路線圖功能路線圖][aks-roadmap]。

## <a name="next-steps"></a>後續步驟

要開始在 AKS 中的 Windows 伺服器容器,[請建立在 AKS 執行 Windows 伺服器的節點池][windows-node-cli]。

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
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
