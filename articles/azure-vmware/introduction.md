---
title: 簡介
description: 了解 Azure VMware 解決方案在 Azure 中部署和管理 VMware 工作負載的功能和優點。
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 1875f535a5ca3e346362409c143da82cb8c07a12
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488582"
---
# <a name="what-is-azure-vmware-solution"></a>什麼是 Azure VMware 解決方案？

Azure VMware 解決方案可在 Azure 為您提供私人雲端。 私人雲端包含 vSphere 叢集，從專用的裸機 Azure 基礎結構加以建置。 由於單一私人雲端中可擁有多個叢集，您可以將私人雲端叢集從 3 個主機調整為 16 個。 所有私人雲端都會以 vCenter Server、vSAN、vSphere 和 NSX-T 佈建。 您可以從內部部署環境遷移工作負載、建立或部署新的虛擬機器，以及從您的私人雲端使用 Azure 服務。

Azure VMware 解決方案是一項 VMware 驗證解決方案，其中包含進行中的增強功能與升級驗證和測試。 私人雲端基礎結構和軟體由 Microsoft 所管理和維護，可讓您專注於在私人雲端中開發及執行工作負載。

下圖顯示 Azure、Azure 服務和內部部署環境中私人雲端和 VNet 之間的相鄰關係。 從私人雲端到 Azure 服務或 VNet 的網路存取，會提供 Azure 服務端點的 SLA 驅動整合。 來自內部部署環境的私人雲端存取會使用 ExpressRoute Global Reach 來進行私人且安全的連線。

![Azure VMware 解決方案私人雲端與 Azure 和內部部署相鄰的影像](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>主機、叢集和私人雲端

Azure VMware 解決方案私人雲端和叢集是從裸機、超融合的 Azure 基礎結構主機所建立。 高階主機具有 576 GB 的 RAM，以及兩顆 Intel 18 核心和 2.3 GHz 的處理器。 HE 主機具有兩個 vSAN 磁碟群組，其中包含總共 15.36 TB (SSD) 的原始 vSAN 容量層級，以及 3.2 TB (NVMe) 的 vSAN 快取層。

新的私人雲端會透過 Azure 入口網站或 Azure CLI 部署。

## <a name="networking"></a>網路功能

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

如需網路和互連能力的詳細資訊，請參閱[網路概念](concepts-networking.md)一文。

## <a name="access-and-security"></a>存取和安全性

為了加強安全性，Azure VMware 解決方案私人雲端會使用 vSphere 角色型存取控制。 vSphere SSO LDAP 功能可以與 Azure Active Directory 整合。 如需有關身分識別和使用權限的詳細資訊，請參閱[存取和身分識別概念](concepts-identity.md)一文。

vSAN 待用資料加密預設為啟用，並用來提供 vSAN 資料存放區的安全性。 [儲存體概念](concepts-storage.md)一文中會更詳細地說明。

## <a name="host-and-software-lifecycle-maintenance"></a>主機和軟體生命週期維護

以一般方式升級 Azure VMware 解決方案私人雲端和 VMware 軟體，可確保在您的私人雲端中執行最新的安全性、穩定性和功能集。 如需有關平台維護和升級的詳細資訊，請參閱[升級概念](concepts-upgrades.md)一文。

## <a name="monitoring-your-private-cloud"></a>監視為您的私人雲端

將 Azure VMware 解決方案部署至您的訂用帳戶後，就會自動產生 [Azure 監視器記錄](../azure-monitor/overview.md)。 此外，您可以收集私人雲端中每個虛擬機器的記錄。 您可以在執行於 Azure VMware 解決方案私人雲端中的 Linux 和 Windows 虛擬機器上[下載並安裝 MMA 代理程式](../azure-monitor/platform/log-analytics-agent.md#installation-options)，以及啟用 [Azure 診斷擴充功能](../azure-monitor/platform/diagnostics-extension-overview.md)。 您甚至可以執行通常會在虛擬機器上執行的相同查詢。 若要深入了解如何建立查詢，請參閱 [Azure 監視器記錄概觀](../azure-monitor/platform/data-platform-logs.md)。 Azure VMware 解決方案內的監視模式類似於 IaaS 平台內的 Azure 虛擬機器。 如需詳細資訊和操作說明，請參閱[使用 Azure 監視器監視 Azure 虛擬機器](../azure-monitor/insights/monitor-vm-azure.md)。

## <a name="next-steps"></a>後續步驟

下一步是了解主要的 [私人雲端和叢集概念](concepts-private-clouds-clusters.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
