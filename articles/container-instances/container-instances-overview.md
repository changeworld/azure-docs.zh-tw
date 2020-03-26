---
title: Azure 中的無伺服器容器
description: Azure Container Instances 服務提供最簡單快速的方法，讓您無需管理虛擬機器或採用更高層級的協調器，就能在 Azure 中執行隔離的容器。
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215046"
---
# <a name="what-is-azure-container-instances"></a>什麼是 Azure Container Instances？

容器即將成為封裝、部署及管理雲端應用程式的慣用方法。 Azure Container Instances 提供更簡單快速的方法，讓您無需管理任何虛擬機器或採用更高層級的服務，就能在 Azure 中執行容器。

對於可在隔離容器中運作的任何情節，包括簡單的應用程式、工作自動化及建置工作，Azure Container Instances 是很棒的解決方案。 對於需要完整容器協調流程的情節，包括跨多個容器的服務探索、自動縮放及協調性應用程式升級，我們建議使用 [Azure Kubernetes Service (AKS)](../aks/index.yml)。

## <a name="fast-startup-times"></a>快速啟動時間

容器提供比虛擬機器 (VM) 更多的啟動優點。 Azure Container Instances 可在幾秒內啟動 Azure 中的容器，而不需要佈建和管理 VM。

## <a name="container-access"></a>容器存取

Azure Container Instances 可讓您利用 IP 位址和完整網域名稱 (FQDN)，直接向網際網路公開您的容器群組。 當您建立容器執行個體時，您可以指定自訂的 DNS 名稱標籤，讓系統可以在 *customlabel*.*azureregion*.azurecontainer.io 找到您的應用程式。

Azure Container Instances 也會提供互動式殼層，讓您可以在執行中的容器內執行命令，進而協助應用程式的開發和疑難排解。 存取會透過 HTTPS 進行，並使用 TLS 來保護用戶端連線。

> [!IMPORTANT]
> 從 2020 年 1 月 13 日開始，Azure Container Instances 將要求所有來自伺服器和應用程式的安全連線都使用 TLS 1.2。 將會淘汰 TLS 1.0 和1.1 的支援。

## <a name="hypervisor-level-security"></a>Hypervisor 等級安全性

在過去，雖然容器提供應用程式相依性隔離和資源控管，但並沒有足夠的能力防範惡意的多租用戶使用方式。 Azure Container Instances 保證會將您的應用程式隔離在容器中，就像在 VM 中一樣。


## <a name="custom-sizes"></a>自訂大小

容器通常已最適合只執行單一應用程式，但這些應用程式的確切需求可能明顯不同。 Azure Container Instances 允許使用明確的 CPU 核心和記憶體規格，以提供最佳的使用率。 您可根據所需的數量付費並採用以秒計費方式，因此能夠根據實際的需求來微調您的費用。

對於計算密集型作業 (例如機器學習)，Azure Container Instances 可排程 Linux 容器以使用 NVIDIA Tesla [GPU 資源](container-instances-gpu.md) (預覽)。

## <a name="persistent-storage"></a>永續性儲存體

為了擷取和保存 Azure Container Instances 的狀態，我們支援直接[裝載 Azure 檔案儲存體共用](container-instances-mounting-azure-files-volume.md) (由 Azure 儲存體提供支援)。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

Azure Container Instances 可讓您使用相同的 API 來排程 Windows 和 Linux 容器。 當您建立[容器群組](container-instances-container-groups.md)時，只須指定 OS 類型。

某些功能目前僅限於 Linux 容器：

* 每個容器群組的多個容器
* 磁碟區掛接 ([Azure 檔案儲存體](container-instances-volume-azure-files.md)、[emptyDir](container-instances-volume-emptydir.md)、[GitRepo](container-instances-volume-gitrepo.md)、[祕密](container-instances-volume-secret.md))
* Azure 監視器的[資源使用量計量](container-instances-monitor.md)
* [虛擬網路部署](container-instances-vnet.md)
* [GPU 資源](container-instances-gpu.md) (預覽)

進行 Windows 容器部署時，請使用以常用的 [Windows 基礎映像](container-instances-faq.md#what-windows-base-os-images-are-supported)為基礎的映像。

> [!NOTE]
> 在 Azure 容器執行個體中使用以 Windows Server 2019 為基礎的映像是預覽功能。

## <a name="co-scheduled-groups"></a>共同排程的群組

Azure Container Instances 支援排程共用主機、區域網路、儲存體和生命週期的[多個容器群組](container-instances-container-groups.md)。 這可讓您合併主要應用程式容器與其他支援角色容器，例如記錄 Sidecar。

## <a name="virtual-network-deployment"></a>虛擬網路部署

Azure 容器執行個體的這項功能目前可在部分 Azure 區域中用於生產工作負載，可以[將容器執行個體部署至 Azure 虛擬網路](container-instances-vnet.md)。 藉由將容器執行個體部署至您虛擬網路內的子網路，這些執行個體可以安全地與虛擬網路中的其他資源通訊，包括內部部署的資源 (透過 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute](../expressroute/expressroute-introduction.md))。

## <a name="next-steps"></a>後續步驟

使用我們的快速入門指南，嘗試使用單一命令將容器部署至 Azure。

> [!div class="nextstepaction"]
> [Azure Container Instances 快速入門](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
