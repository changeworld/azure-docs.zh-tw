---
title: Azure Migrate Server 遷移的常見問題
description: 取得有關使用 Azure Migrate Server 遷移來遷移電腦的常見問題解答
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425828"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 遷移：常見的問題

本文會回答有關 Azure Migrate：伺服器遷移工具的常見問題。 如果您在閱讀本文後有其他問題，請參閱下列文章：

- 有關 Azure Migrate 的[一般問題](resources-faq.md)。
- Azure Migrate 設備的相關[問題](common-questions-appliance.md)。
- 探索、評估和相依性視覺效果的相關[問題](common-questions-discovery-assessment.md)。
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)上張貼問題。


## <a name="how-does-agentless-vmware-replication-work"></a>無代理程式 VMware 複寫如何運作？

VMware 的無代理程式複寫方法會使用 VMware 快照集，以及 VMware 變更封鎖追蹤（CBT）。

1. 當您啟動複寫時，會排定初始複寫迴圈。 在初始迴圈中，會採用 VM 的快照集。 此快照集會用來複寫 Vm Vmdk （磁片）。 
2. 在初始複寫週期完成後，會定期排程差異複寫週期。
    - 在差異複寫期間，會建立快照集，並複寫自先前複寫週期之後變更的資料區塊。
    - VMware CBT 是用來判斷自上一個週期以來已變更的區塊。
    - 定期複寫週期的頻率會由 Azure Migrate 自動管理，視從相同資料存放區同時複寫的其他 Vm/磁片數目而定。 在理想的情況下，複寫最終會針對每個 VM 每小時聚合到一個迴圈。

當您遷移時，系統會針對機器排定隨選複寫週期，以捕捉任何剩餘的資料。 您可以選擇在遷移期間關閉機器，以確保零資料遺失和應用程式一致性。

## <a name="why-isnt-resynchronization-exposed"></a>為什麼不會公開重新同步處理？

在無代理程式遷移期間，在每個差異迴圈中，會寫入目前快照與先前拍攝的快照之間的差異。 因為快照集之間一律會有差異，所以折迭中的資料。 因此，如果特定磁區在快照集之間寫入 N 次，則只需要傳送最後寫入，因為我們只對上次同步處理感興趣。這與以代理程式為基礎的複寫不同，我們會追蹤並套用每個寫入。 這表示每個差異週期都是重新同步處理。 因此，不會公開任何重新同步處理選項。 如果磁片因為失敗而未同步處理，則會在下一個迴圈中修正。 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>變換率如何影響無代理程式複寫？

由於無代理程式複寫會折迭日期，變換模式對於變換率而言比較重要。 再次寫入檔案時，速率不會有太大的影響。 不過，寫入其他每個磁區的模式會導致下一個週期的高變換。 因為我們會將所傳輸的資料量降到最低，所以我們會盡可能讓資料折迭，然後再排程下一個週期。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>複寫週期排程的頻率為何？

排程下一個複寫週期的公式：（上一個週期時間/2）或1小時（以較高者為准）。

例如，如果 VM 需要四個小時來進行差異迴圈，則下一個週期會排程為兩個小時，而不是在下一個小時。 這在初始複寫之後會立即有所不同，其中第一個差異週期會立即排程。

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>無代理程式複寫會如何影響 VMware 伺服器？

VCenter Server/ESXi 主機上會有一些效能上的影響。 由於無代理程式複寫會使用快照集，因此會耗用儲存體上的 IOPs，而且需要一些 IOPS 儲存頻寬。 如果您的環境中有儲存體/IOPs 的限制，則不建議使用無代理程式複寫。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>我可以對 Azure Gen 2 進行無代理程式的 UEFI Vm 遷移嗎？

否。 使用 Azure Site Recovery 將這些 Vm 遷移至 Gen 2 Azure Vm。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>我可以在遷移時將 Vm 釘選到 Azure 可用性區域嗎？

否，不支援 Azure 可用性區域。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Azure Migrate 在複寫期間使用哪種傳輸通訊協定？

Azure Migrate 使用網路封鎖裝置（NBD）通訊協定搭配 SSL 加密。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>遷移所需的最小 vCenter Server 版本為何？

您必須至少有 vCenter Server 5.5 和 VMware vSphere ESXi host 5.5 版。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客戶可以將其 Vm 遷移至非受控磁片嗎？

否。 Azure Migrate 僅支援將遷移至受控磁片（標準 HDD、premium SSD）。

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>有多少 Vm 可以與無代理程式的遷移一起複寫？

目前，您可以同時遷移每個 vCenter Server 的 100 Vm。 以10個 Vm 的批次進行遷移。
 



