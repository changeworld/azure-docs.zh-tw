---
title: 概念-監視和修復 Azure VMware 解決方案私人雲端
description: 瞭解 Azure VMware 解決方案如何監視和修復 Azure VMware 解決方案私人雲端上的 VMware ESXi 伺服器。
ms.topic: conceptual
ms.custom: contperfq2
ms.date: 11/20/2020
ms.openlocfilehash: 6c37ff6cc0715182453669f67306094af2f00cdf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024342"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>監視及修復 Azure VMware 解決方案私人雲端

Azure VMware 解決方案會持續監視 Azure VMware 解決方案私人雲端上的 VMware ESXi 伺服器。 

## <a name="what-azure-vmware-solution-monitors"></a>Azure VMware 解決方案的監視

Azure VMware 解決方案會監視主機上失敗狀況的下列各項：  

- 處理器狀態 
- 記憶體狀態 
- 連接和電源狀態 
- 硬體風扇狀態 
- 網路連接中斷 
- 硬體系統面板狀態 
- VSAN 主機的磁片 () 發生錯誤 
- 硬體電壓 
- 硬體溫度狀態 
- 硬體電源狀態 
- 儲存體狀態 
- 連線失敗 

> [!NOTE]
> Azure VMware 解決方案租使用者系統管理員不得編輯或刪除上述定義的 VMware vCenter 警示，因為這些警示是由 vCenter 上的 Azure VMware 解決方案控制平面所管理。 Azure VMware 解決方案監視會使用這些警示來觸發 Azure VMware 解決方案主機補救流程。

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware 解決方案主機補救  

當 Azure VMware 解決方案偵測到租使用者私用雲端上的 Azure VMware 解決方案節點效能降低或失敗時，它會觸發主機補救流程。 主機補救牽涉到以新的狀況良好節點來取代故障的節點。  

主機補救程式一開始會將新的狀況良好節點新增至叢集中。 如此一來，故障的主機可能會在 VMware vSphere 維護模式下放置。 VMware vMotion 可用來將 Vm 從故障的主機移至叢集中的其他可用伺服器，這可能會讓工作負載的即時移轉不會中斷。 在故障主機無法置於維護模式的情況下，主機會從叢集移除。

## <a name="next-steps"></a>下一步

以下是您可能想要深入瞭解的一些主題：

- [Azure VMware 解決方案私用雲端升級](concepts-upgrades.md)
- [Azure VMware 解決方案 Vm 的生命週期管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- [使用 Azure 資訊安全中心整合來保護您的 Azure VMware 解決方案 Vm](azure-security-integration.md)
- [使用 Azure 備份伺服器備份 Azure VMware 解決方案 Vm](backup-azure-vmware-solution-virtual-machines.md)
- [使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復](disaster-recovery-for-virtual-machines.md)
