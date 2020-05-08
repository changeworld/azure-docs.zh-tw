---
title: Azure Migrate Server 遷移的常見問題
description: 取得有關使用 Azure Migrate 伺服器遷移來遷移電腦的常見問題的解答。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0cfe23b4e544040fc3ab69796988ca34b1bdcdbf
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744333"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 遷移：常見的問題

本文會回答有關 Azure Migrate：伺服器遷移工具的常見問題。 如果您有其他問題，請檢查下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [探索、評估和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中獲得解答

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>哪些地理位置支援以 Azure Migrate 進行遷移？

審查適用于[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府](migrate-support-matrix.md#supported-geographies-azure-government)雲端的支援地理位置。

## <a name="how-does-agentless-vmware-replication-work"></a>無代理程式 VMware 複寫如何運作？

適用于 VMware 的無代理程式複寫方法會使用 VMware 快照集和 VMware 變更封鎖追蹤（CBT）。

其程序如下：

1. 當您啟動複寫時，會排定初始複寫迴圈。 在初始迴圈中，會採用 VM 的快照集。 快照集會用來複寫 Vm Vmdk （磁片）。 
2. 在初始複寫週期完成後，會定期排程差異複寫週期。
    - 在差異複寫期間，會建立快照集，並複寫自先前複寫週期之後變更的資料區塊。
    - VMware CBT 是用來判斷自上一個週期以來已變更的區塊。
    - 定期複寫週期的頻率會由 Azure Migrate 自動管理，取決於多個 Vm 和磁片同時從相同的資料存放區進行複寫。 在理想的情況下，複寫最終會針對每個 VM 每小時聚合到一個迴圈。

當您遷移時，會針對機器排定隨選複寫週期來捕捉任何剩餘的資料。 為確保零資料遺失和應用程式一致性，您可以選擇在遷移期間關閉機器。

## <a name="why-isnt-resynchronization-exposed"></a>為什麼不會公開重新同步處理？

在無代理程式遷移期間，在每個差異迴圈中，會寫入目前快照與先前拍攝的快照之間的差異。 這一律是快照集之間的差異，折迭中的資料。 如果指定的磁區在快照集之間寫入*N*次，則只需要傳送最後寫入，因為我們只對上次同步處理感興趣。此程式不同于代理程式式複寫，在此期間，我們會追蹤並套用每個寫入。 在此程式中，每個差異週期都是重新同步處理。 因此，不會公開任何重新同步處理選項。 如果磁片因為失敗而未同步處理，則會在下一個迴圈中修正。 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>變換率如何影響無代理程式複寫？

因為無代理程式複寫會在資料中折迭，所以變換*模式*比變換*率*更重要。 再次寫入檔案時，速率不會有太大的影響。 不過，寫入其他每個磁區的模式會導致下一個週期的高變換。 因為我們會將所傳輸的資料量降到最低，所以我們會在排程下一個週期之前，盡可能折迭資料。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>複寫週期排程的頻率為何？

排定下一個複寫週期的公式為（上一個週期時間/2）或一小時（以較高者為准）。

例如，如果 VM 需要四個小時來進行差異迴圈，則下一個週期會排程為兩個小時，而不是在下一個小時。 當第一個差異週期立即排程時，此程式會在初始複寫之後立即有所不同。

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>無代理程式複寫會如何影響 VMware 伺服器？

無代理程式複寫會在 VMware vCenter Server 和 VMware ESXi 主機上產生一些效能影響。 因為無代理程式複寫會使用快照集，所以它會在儲存體上使用 IOPS，因此需要一些 IOPS 儲存頻寬。 如果您的環境中有儲存體或 IOPs 的條件約束，則不建議使用無代理程式複寫。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>我可以對 Azure Gen 2 進行無代理程式的 UEFI Vm 遷移嗎？

不需要。 使用 Azure Site Recovery 將這些 Vm 遷移至 Gen 2 Azure Vm。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>我可以在遷移時將 Vm 釘選到 Azure 可用性區域嗎？

不需要。 Azure 可用性區域不支援 Azure Migrate 遷移。

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Azure Migrate 在複寫期間使用哪種傳輸通訊協定？

Azure Migrate 使用網路封鎖裝置（NBD）通訊協定搭配 TLS 加密。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>遷移所需的最小 vCenter Server 版本為何？

您必須至少有 vCenter Server 5.5 和 vSphere ESXi host 5.5 版。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客戶可以將其 Vm 遷移至非受控磁片嗎？

不需要。 Azure Migrate 僅支援將遷移至受控磁片（標準 HDD、進階 SSD）。

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>我可以使用無代理程式遷移一次複寫多少部 Vm？

目前，您可以同時遷移每個 vCenter Server 實例的 100 Vm。 以10個 Vm 的批次進行遷移。

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>使用 Azure Migrate 設備來進行無代理程式 VMware 複寫的如何? 節流複寫？  

您可以使用 New-netqospolicy 進行節流。 例如：

要在 New-netqospolicy 中使用的 AppNamePrefix 是 "GatewayWindowsService"。 您可以在 Azure Migrate 設備上建立原則，藉由建立如下所示的原則來節流設備的複寫流量：
 
New-netqospolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService"-ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>我可以將已複寫的 Vm 遷移至 Azure 嗎？ 

如果 Vm 已透過其他方式複寫至 Azure，您就無法使用 Azure Migrate Server 遷移，將這些機器遷移為 Vm。 因應措施是將 Vm 視為實體伺服器，並根據[支援的實體伺服器遷移](migrate-support-matrix-physical-migration.md)來進行遷移。

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>何時將機器遷移為實體伺服器？

在許多情況下，將機器視為實體伺服器進行遷移，都有其效用：

- 當您要遷移內部部署實體伺服器時。
- 如果您要遷移由像是 Xen、KVM 等平臺虛擬化的 Vm。
- 若要遷移 Hyper-v 或 VMware Vm，如果基於某些原因而無法使用[hyper-v](tutorial-migrate-hyper-v.md)或[vmware](server-migrate-overview.md)遷移的標準遷移程式。 例如，如果您不是執行 VMware vCenter，而且只使用 ESXi 主機。
- 將目前正在私人雲端中執行的 Vm 遷移至 Azure
- 如果您想要將在公用雲端中執行的 Vm （例如 Amazon Web Services （AWS）或 Google Cloud Platform （GCP））遷移至 Azure。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>我部署了兩個（或更多）設備，以探索我的 vCenter Server 中的 Vm。 不過，當我嘗試遷移 Vm 時，只會看到對應到其中一個設備的 Vm。

雖然這可能是很好的使用案例，但我們目前不支援。 部署兩個（或更多）設備以探索相同的 Vm 集合會導致服務問題，其中 VM 擁有權會在兩個設備之間保持切換。 這是您看到 Vm 出現並消失的原因。 在這種情況下，若要解決此問題，您必須刪除一個設備，然後進行硬重新整理。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>我需要 VMware vCenter 才能遷移 VMware Vm 嗎？
若要使用 VMware 代理程式型或無代理程式遷移來[遷移 Vmware vm](server-migrate-overview.md) ，ESXi vm 所在的主機必須由 vCenter Server 管理。 如果您沒有 vCenter Server，可以藉由將 VMware Vm 作為實體伺服器遷移來進行遷移。 [深入了解](migrate-support-matrix-physical-migration.md)。
 
## <a name="next-steps"></a>後續步驟

閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
