---
title: Azure 遷移伺服器遷移常見問題解答
description: 獲取有關使用 Azure 遷移伺服器遷移遷移電腦的常見問題的解答。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 507cc8088bf54b1a4f4483673ec5332efcdd36c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127810"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure 遷移伺服器遷移：常見問題

本文回答了有關 Azure 遷移：伺服器遷移工具的常見問題。 如果您有其他問題，請檢查以下資源：

- 有關 Azure 遷移的[一般問題](resources-faq.md)
- 有關[Azure 遷移設備的問題](common-questions-appliance.md)
- 有關[發現、評估和依賴項視覺化](common-questions-discovery-assessment.md)的問題
- 在[Azure 遷移論壇](https://aka.ms/AzureMigrateForum)中回答問題

## <a name="how-does-agentless-vmware-replication-work"></a>無代理 VMware 複製如何工作？

VMware 的無代理複製方法使用 VMware 快照和 VMware 更改塊跟蹤 （CBT）。

其程序如下：

1. 開始複製時，將計畫初始複製週期。 在初始週期中，將獲取 VM 的快照。 快照用於複製 VMVMDK（磁片）。 
2. 初始複製週期完成後，定期計畫增量複製週期。
    - 在增量複製期間，將拍攝快照，並複製自上次複製週期以來已更改的資料塊。
    - VMware CBT 用於確定自上次週期以來已更改的塊。
    - 定期複製週期的頻率由 Azure 遷移自動管理，具體取決於有多少其他 VM 和磁片同時從同一資料存儲複製。 在理想情況下，複製最終為每個 VM 每小時收斂一個週期。

遷移時，電腦將安排一個按需複製週期來捕獲任何剩餘資料。 為了確保零資料丟失和應用程式一致性，您可以選擇在遷移期間關閉電腦。

## <a name="why-isnt-resynchronization-exposed"></a>為什麼不公開重新同步？

在無代理遷移期間，在每個增量週期中，將寫入當前快照和以前拍攝的快照之間的差異。 它總是快照、折疊資料之間的差異。 如果特定磁區在快照之間寫入*N*次，則只需要傳輸最後一個寫入，因為我們只對上次同步感興趣。該過程不同于基於代理的複製，在此期間，我們跟蹤和應用每個寫入。 在此過程中，每個增量週期都是重新同步。 因此，不會公開重新同步選項。 如果磁片因故障而未同步，則在下一個週期中修復。 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>改性率如何影響無代理複製？

由於無代理複製在資料中折疊，*因此改動模式*比*改動率*更重要。 當一次又一次地寫入檔時，速率沒有太大的影響。 但是，寫入其他每個磁區的模式會導致下一個週期的高流失。 由於我們最大限度地減少了傳輸的資料量，因此在計畫下一個週期之前，我們允許資料盡可能折疊。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>複製週期的調度頻率如何？

計畫下一個複製週期的公式是（以前的週期時間 / 2）或一小時，以較高者為准。

例如，如果 VM 的增量週期需要四個小時，則下一個週期將在兩小時內安排，而不是在下一小時內。 初始複製後立即進行該過程不同，此時會立即計畫第一個增量週期。

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>無代理複製如何影響 VMware 伺服器？

無代理複製會對 VMware vCenter 伺服器和 VMware ESXi 主機產生一些性能影響。 由於無代理複製使用快照，因此它在存儲時使用 IOPS，因此需要一些 IOPS 存儲頻寬。 如果您的環境中對存儲或 I普具有限制，我們不建議使用無代理複製。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>是否可以對 UEFI VM 進行無代理遷移，以遷移到 Azure 第 2 代？

否。 使用 Azure 網站恢復將這些 VM 遷移到第 2 代 Azure VM。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>遷移時，是否可以將 VM 固定到 Azure 可用性區域？

否。 Azure 遷移不支援 Azure 可用性區域。

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Azure 遷移在複製期間使用什麼傳輸協議？

Azure 遷移使用帶有 SSL 加密的網路塊設備 （NBD） 協定。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>遷移所需的最低 vCenter 伺服器版本是什麼？

您必須至少具有 vCenter 伺服器 5.5 和 vSphere ESXi 主機版本 5.5。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客戶是否可以將其 VM 遷移到非託管磁片？

否。 Azure 遷移僅支援遷移到託管磁片（標準硬碟、高級 SSD）。

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>使用無代理遷移可以同時複製多少 VM？

目前，您可以同時遷移每個 vCenter Server 實例的 100 個 VM。 分批遷移 10 個 VM。

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>如何使用 Azure 遷移設備進行無代理 VMware 複製，如何限制複製？  

您可以使用 NetQos 策略進行節流。 例如：

NetQos 策略中要使用的 AppNamePrefix 是"閘道Windows服務.exe"。 您可以在 Azure 遷移設備上創建策略，通過創建如下策略來限制來自設備的複製流量：
 
新網調策略 - 名稱"節流複製" - AppPathName 匹配條件"閘道Windows服務.exe" - 節流操作位秒 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>何時將電腦遷移到物理伺服器？

在許多情況下，將機器視為實體伺服器進行遷移，都有其效用：

- 遷移本地物理伺服器時。
- 如果要遷移由 Xen、KVM 等平臺虛擬化的 VM。
- 要遷移 Hyper-V 或 VMware VM，如果由於某種原因無法將標準遷移過程用於[Hyper-V](tutorial-migrate-hyper-v.md)，或[VMware](server-migrate-overview.md)遷移。 例如，如果您未運行 VMware vCenter，並且僅使用 ESXi 主機。
- 將當前在私有雲中運行的 VM 遷移到 Azure
- 如果要將在公共雲（如 Amazon Web 服務 （AWS） 或 Google 雲平臺 （GCP））中運行的 VM 遷移到 Azure。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>我是否需要 VMware vCenter 來遷移 VMware VM？
要使用基於 VMware 代理或無代理的遷移[遷移 VMware VM，VM](server-migrate-overview.md)所在的 ESXi 主機必須由 vCenter Server 管理。 如果沒有 vCenter 伺服器，則可以通過將 VMware VM 遷移到物理伺服器來遷移 VMware VM。 [深入了解](migrate-support-matrix-physical-migration.md)。
 
## <a name="next-steps"></a>後續步驟

閱讀[Azure 遷移概述](migrate-services-overview.md)。
