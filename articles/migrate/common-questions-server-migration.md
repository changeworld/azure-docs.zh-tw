---
title: Azure Migrate Server 遷移的常見問題
description: 取得 Azure Migrate Server 遷移的常見問題解答
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067379"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 遷移：常見的問題

本文會回答有關 Azure Migrate 的常見問題：伺服器遷移。 如果您在閱讀本文後有進一步的查詢，請將其張貼在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。 如果您有其他問題，請參閱下列文章：

- 有關 Azure Migrate 的[一般問題](resources-faq.md)。
- Azure Migrate 設備的相關[問題](common-questions-appliance.md)。
- 探索、評估和相依性視覺效果的相關[問題](common-questions-discovery-assessment.md)。


## <a name="how-does-agentless-vmware-replication-work"></a>無代理程式 VMware 複寫如何運作？

VMware 的無代理程式複寫方法會使用 VMware 快照集，以及 VMware 變更封鎖追蹤（CBT）。 初始複寫週期會在使用者開始複寫時排程。 在初始複寫週期中，會採用 VM 的快照集，而此快照集會用來複寫 Vm Vmdk （磁片）。 初始複寫週期完成後，會定期排程差異複寫週期。 在差異複寫週期中，會建立快照集，並複寫自先前複寫週期以來變更的資料區塊。 VMware 變更的區塊追蹤是用來判斷自上一個週期以來已變更的區塊。
定期複寫週期的頻率是由服務自動管理，這取決於有多少其他 Vm/磁片同時從相同的資料存放區進行複寫，而且在理想的情況中，每個 VM 的每小時最終都會收斂到1個迴圈。

當您遷移時，會排程 VM 的隨選複寫週期來捕捉任何剩餘的資料。 您可以選擇在遷移過程中關閉 VM，以確保零資料遺失和應用程式一致性。

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>為什麼不會在無代理程式堆疊中公開重新同步處理選項？

在無代理程式堆疊中，我們會在每個差異迴圈中，傳輸目前快照集與我們所建立之先前快照集之間的差異。 因為快照集之間一律有差異，所以這會提供折迭資料的優點（也就是，如果特定的磁區在快照集之間寫入 ' n ' 次，我們只需要轉移最後的寫入，因為我們只對上一個同步處理有興趣）。 這不同于以代理程式為基礎的堆疊，我們會追蹤每個寫入並加以套用。 這表示每個差異週期都是重新同步處理。 因此，不會公開任何重新同步處理選項。 

如果磁片因為失敗而不同步，則會在下一個迴圈中修正。 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>如果我使用無代理程式複寫，變換率的影響為何？

因為堆疊相依于要折迭的資料，而不是變換率，所以變換模式就是此堆疊中的重要事項。 再次寫入檔案的模式，並不會有太大的影響。 不過，寫入其他每個磁區的模式會導致下一個週期的高變換率。 因為我們會將所傳輸的資料量降到最低，所以我們會盡可能讓資料折迭，然後再排程下一個週期。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>複寫週期排程的頻率為何？

排定下一個複寫週期的公式如下：（上一個週期時間/2）或1小時（取其較高者）。 例如，如果 VM 花了四個小時進行差異迴圈，我們會在2小時內排程其下一個週期，而不是在下一小時。 當此週期緊接在 IR 之後，我們會立即排程第一個差異週期，這是不同的。

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>使用無代理程式複寫時，對 vCenter Server 或 ESXi 主機的效能有何影響？

由於無代理程式複寫使用快照集，儲存體上將會有 IOPs 耗用量，而且客戶在儲存體上需要一些 IOPs 空間。 我們不建議在儲存體/IOPs 限制環境上使用此堆疊。

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>無代理程式遷移堆疊支援將 UEFI Vm 遷移至 Azure Gen 2 Vm 嗎？

否，您必須使用 Azure Site Recovery 將這些 Vm 遷移至 Gen 2 Azure Vm。 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>我可以在遷移時將 Vm 釘選到 Azure 可用性區域嗎？

否，Azure 可用性區域不支援。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Azure Migrate 在複寫期間使用哪種傳輸通訊協定？

Azure Migrate 使用網路封鎖裝置（NBD）通訊協定搭配 SSL 加密。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>遷移所需的最小 vCenter Server 版本為何？

您必須至少有 vCenter Server 5.5 和 VMware vSphere ESXi host 5.5 版。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客戶可以將其 Vm 遷移至非受控磁片嗎？

No。 Azure Migrate 僅支援將遷移至受控磁片（標準 HDD、premium SSD）。

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>有多少 Vm 可以同時使用無代理程式的 VMware 堆疊進行複寫？

目前，客戶可以同時遷移每個 vCenter Server 的 100 Vm。 這可在10個 Vm 的批次中完成。




