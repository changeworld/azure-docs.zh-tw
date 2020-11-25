---
title: 簡介
description: 了解 Azure VMware 解決方案在 Azure 中部署和管理 VMware 工作負載的功能和優點。
ms.topic: overview
ms.date: 11/11/2020
ms.openlocfilehash: 57edfc5786dfc95070b66eb9c8e2e038bafdcd35
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534648"
---
# <a name="what-is-azure-vmware-solution"></a>什麼是 Azure VMware 解決方案？

Azure VMware 解決方案會為您提供私人雲端，其中包含從專用裸機 Azure 基礎結構建置的 vSphere 叢集。 最基本的初始部署為三部主機，但您可以逐次新增一部額外的主機，每個叢集最多 16 部主機。  所有佈建的私人雲端都具有 vCenter Server、vSAN、vSphere 和 NSX-T。 您可以從內部部署環境遷移工作負載、部署新的虛擬機器，以及從您的私人雲端使用 Azure 服務。

Azure VMware 解決方案是一項 VMware 驗證解決方案，其中包含進行中的增強功能與升級驗證和測試。 Microsoft 會管理和維護私人雲端基礎結構和軟體。 如此，您即可全心在私人雲端中開發及執行工作負載。 

下圖顯示 Azure、Azure 服務和內部部署環境中私人雲端與 VNet 之間的相鄰關係。 從私人雲端到 Azure 服務或 VNet 的網路存取，會提供 Azure 服務端點的 SLA 驅動整合。 ExpressRoute Global Reach 可將您的內部部署環境連線至 Azure VMware 解決方案私人雲端。 

![Azure VMware 解決方案私人雲端與 Azure 和內部部署相鄰的影像](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>主機、叢集和私人雲端

Azure VMware 解決方案私人雲端和叢集是從裸機、超融合的 Azure 基礎結構主機所建立。 高階主機具有 576 GB 的 RAM，以及兩顆 Intel 18 核心和 2.3 GHz 的處理器。 HE 主機具有兩個 vSAN 磁碟群組，其中包含 15.36 TB (SSD) 的原始 vSAN 容量層，以及 3.2 TB (NVMe) 的 vSAN 快取層。

新的私人雲端會透過 Azure 入口網站或 Azure CLI 部署。

## <a name="networking"></a>網路功能

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

如需詳細資訊，請參閱[網路概念](concepts-networking.md)。

## <a name="access-and-security"></a>存取和安全性

Azure VMware 解決方案私人雲端會使用 vSphere 角色型存取控制，以加強安全性。 您可以將 vSphere SSO LDAP 功能與 Azure Active Directory 整合。 如需詳細資訊，請參閱[存取和身分識別概念](concepts-identity.md)。  

vSAN 待用資料加密預設為啟用，可用來提供 vSAN 資料存放區的安全性。 如需詳細資訊，請參閱[儲存體概念](concepts-storage.md)。

## <a name="host-and-software-lifecycle-maintenance"></a>主機和軟體生命週期維護

以一般方式升級 Azure VMware 解決方案私人雲端和 VMware 軟體，可確保在您的私人雲端中執行最新的安全性、穩定性和功能集。 如需詳細資訊，請參閱[私人雲端更新和升級](concepts-upgrades.md)。

## <a name="monitoring-your-private-cloud"></a>監視為您的私人雲端

將 Azure VMware 解決方案部署至您的訂用帳戶後，就會自動產生 [Azure 監視器記錄](../azure-monitor/overview.md)。 

在您的私人雲端中，您可以：
- 收集每個 VM 上的記錄。
- 在 Linux 和 Windows VM 上[下載並安裝 MMA 代理程式](../azure-monitor/platform/log-analytics-agent.md#installation-options)。
- 啟用 [Azure 診斷擴充功能](../azure-monitor/platform/diagnostics-extension-overview.md)。
- [建立並執行新的查詢](../azure-monitor/platform/data-platform-logs.md#log-queries)。
- 執行您通常在 VM 上執行的相同查詢。

Azure VMware 解決方案內的監視模式類似於 IaaS 平台內的 Azure VM。 如需詳細資訊和操作說明，請參閱[使用 Azure 監視器來監視 Azure VM](../azure-monitor/insights/monitor-vm-azure.md)。

## <a name="next-steps"></a>後續步驟

下一步是了解主要的 [私人雲端和叢集概念](concepts-private-clouds-clusters.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
