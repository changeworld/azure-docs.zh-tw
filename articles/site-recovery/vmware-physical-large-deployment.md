---
title: 使用 Azure Site Recovery 調整 VMware/實體嚴重損壞修復
description: 瞭解如何使用 Azure Site Recovery 設定大量內部部署 VMware Vm 或實體伺服器的災難復原至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 101e42263e46c5a21f26b0fa9cdeed798525fee9
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047070"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>為 VMware Vm/實體伺服器設定大規模的嚴重損壞修復

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務，為您的生產環境中的大量 ( # A0 1000) 的內部部署 VMware vm 或實體伺服器設定嚴重損壞修復。


## <a name="define-your-bcdr-strategy"></a>定義您的 BCDR 策略

在您的商務持續性和嚴重損壞修復 (BCDR) 策略的過程中，您可以為您的商務應用程式和工作負載，定義復原點目標 (Rpo) 和復原時間目標 (Rto) 。 RTO 會測量時間和服務等級的持續時間，在此期間內必須還原或使用商務應用程式或進程，以避免發生持續性問題。
- Site Recovery 提供 VMware Vm 和實體伺服器的連續複寫，以及 RTO 的 [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 。
- 當您規劃 VMware Vm 的大規模嚴重損壞修復，並找出您需要的 Azure 資源時，您可以指定將用於容量計算的 RTO 值。


## <a name="best-practices"></a>最佳作法

大規模嚴重損壞修復的一些一般最佳作法。 本檔的下一節會更詳細地討論這些最佳做法。

- **識別目標需求**：在設定嚴重損壞修復之前，先估計 Azure 中的容量和資源需求。
- **規劃 Site Recovery 元件**：找出 (設定伺服器的 Site Recovery 元件，) 您需要符合預估容量的進程伺服器。
- **設定一或多個相應放大進程伺服器**：請勿使用預設在設定伺服器上執行的進程伺服器。 
- **執行最新的更新**： Site Recovery 團隊會定期發行新版 Site Recovery 元件，而您應確定您執行的是最新版本。 為了協助解決此情況，請追蹤更新的 [新功能](site-recovery-whats-new.md) ，並在發行時 [啟用和安裝更新](service-updates-how-to.md) 。
- **主動監視**：當您啟動並執行嚴重損壞修復時，您應該主動監視複寫機器的狀態和健全狀況，以及基礎結構資源。
- 嚴重損壞**修復演練**：您應該定期執行嚴重損壞修復演練。 這些不會影響您的生產環境，但可確保容錯移轉至 Azure 在需要時能如預期般運作。



## <a name="gather-capacity-planning-information"></a>收集容量規劃資訊

收集內部部署環境的相關資訊，以協助您評估和預估您的目標 (Azure) 容量需求。
- 若為 VMware，請執行 VMware Vm 的部署規劃工具來執行此作業。
- 若為實體伺服器，請手動收集資訊。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>執行 VMware Vm 的部署規劃工具

部署規劃工具可協助您收集 VMware 內部部署環境的相關資訊。

- 在代表 Vm 一般流失的期間內執行部署規劃工具。 這會產生更精確的估計和建議。
- 建議您在設定伺服器電腦上執行部署規劃工具，因為 Planner 會計算其執行所在伺服器的輸送量。 [深入瞭解](site-recovery-vmware-deployment-planner-run.md#get-throughput) 如何測量輸送量。
- 如果您尚未設定設定伺服器：
    - [取得](vmware-physical-azure-config-process-server-overview.md) Site Recovery 元件的總覽。
    - [設定伺服器](vmware-azure-deploy-configuration-server.md)，以便在其上執行部署規劃工具。

然後執行規劃工具，如下所示：

1. [瞭解](site-recovery-deployment-planner.md) 部署規劃工具。 您可以從入口網站下載最新版本，或 [直接下載](https://aka.ms/asr-deployment-planner)。
2. 請參閱部署規劃工具的 [必要條件](site-recovery-deployment-planner.md#prerequisites) 和 [最新更新](site-recovery-deployment-planner-history.md) ，並 [下載並解壓縮](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) 工具。
3. 在設定伺服器上[執行部署規劃工具](site-recovery-vmware-deployment-planner-run.md)。
4. [產生報告](site-recovery-vmware-deployment-planner-run.md#generate-report) 以摘要估計和建議。
5. 分析 [報表建議](site-recovery-vmware-deployment-planner-analyze-report.md) 和 [成本估計](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 根據預設，此工具設定為分析並產生最多1000個 Vm 的報告。 您可以藉由增加 ASRDeploymentPlanner.exe.config 檔案中的 MaxVMsSupported 索引鍵值來變更此限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>規劃目標 (Azure) 需求和容量

您可以使用所收集的估計和建議來規劃目標資源和容量。 如果您執行的是 VMware Vm 的部署規劃工具，您可以使用數個 [報告建議](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) 來協助您。

- **相容的 vm**：使用此數位來識別準備好在 Azure 上進行嚴重損壞修復的 vm 數目。 關於網路頻寬和 Azure 核心的建議是以這個數目為基礎。
- **必要的網路頻寬**：記下相容 vm 的差異複寫所需的頻寬。 
    - 當您執行規劃工具時，您會指定所需的 RPO （以分鐘為單位）。 這些建議會顯示符合 RPO 100% 和90% 的時間所需的頻寬。 
    - 網路頻寬建議會考慮規劃工具中建議的設定伺服器和進程伺服器總數所需的頻寬。
- **必要的 azure 核心**：請根據相容 vm 的數目，記下目標 Azure 區域中所需的核心數目。 如果您沒有足夠的核心，在容錯移轉 Site Recovery 將無法建立所需的 Azure Vm。
- **建議的 VM 批次大小**：建議的批次大小根據預設，在72小時內完成批次初始複寫的能力，同時符合100% 的 RPO。 可以修改小時的值。

您可以使用這些建議來規劃 Azure 資源、網路頻寬和 VM 批次處理。

## <a name="plan-azure-subscriptions-and-quotas"></a>規劃 Azure 訂用帳戶和配額

我們想要確保目標訂用帳戶中的可用配額足以處理容錯移轉。

**Task** | **詳細資料** | **動作**
--- | --- | ---
**檢查核心** | 如果可用配額中的核心不等於或超過容錯移轉時的總目標計數，容錯移轉將會失敗。 | 若為 VMware Vm，請檢查目標訂用帳戶中有足夠的核心，以符合部署規劃工具核心建議。<br/><br/> 針對實體伺服器，請確認 Azure 核心符合您的手動預估。<br/><br/> 若要檢查配額，請在 Azure 入口網站 > **訂**用帳戶中，按一下 [ **使用量 + 配額**]。<br/><br/> [深入瞭解](../azure-portal/supportability/resource-manager-core-quotas-request.md) 如何增加配額。
**檢查容錯移轉限制** | 容錯移轉不得數目超過 Site Recovery 容錯移轉限制。 |  如果容錯移轉超過限制，您可以新增訂用帳戶，並容錯移轉至多個訂用帳戶，或增加訂用帳戶的配額。 


### <a name="failover-limits"></a>容錯移轉限制

這些限制表示在一小時內受 Site Recovery 支援的容錯移轉次數（假設每部電腦有三個磁片）。

符合的意義為何？ 若要啟動 Azure VM，Azure 需要有一些驅動程式處於開機啟動狀態，而像 DHCP 這樣的服務會設定為自動啟動。
- 符合規範的電腦已有這些設定。
- 針對執行 Windows 的電腦，您可以主動檢查合規性，並視需要使其符合規範。 [深入了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux 機器只會在容錯移轉時進入合規性狀態。

**電腦符合 Azure？** | **Azure VM (受控磁片容錯移轉) 的限制 **
--- | --- 
是 | 2000
否 | 1000

- 限制會假設訂用帳戶的目的地區域中有最基本的其他作業正在進行中。
- 某些 Azure 區域較小，而且可能會有稍微低的限制。

## <a name="plan-infrastructure-and-vm-connectivity"></a>規劃基礎結構和 VM 連線能力

在容錯移轉至 Azure 之後，您需要您的工作負載在內部部署環境中運作，並讓使用者能夠存取在 Azure Vm 上執行的工作負載。

- [深入瞭解](site-recovery-active-directory.md#test-failover-considerations) 如何將您的 ACTIVE DIRECTORY 或 DNS 內部部署基礎結構容錯移轉至 Azure。
- [深入瞭解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) 如何準備在容錯移轉後連線至 Azure vm。



## <a name="plan-for-source-capacity-and-requirements"></a>規劃來源容量和需求

您必須擁有足夠的設定伺服器和向外延展進程伺服器，以符合容量需求。 當您開始進行大規模部署時，請從單一設定伺服器開始，以及單一相應放大進程伺服器。 當您達到指定的限制時，請新增額外的伺服器。

>[!NOTE]
> 針對 VMware Vm，部署規劃工具會對您所需的設定和進程伺服器提出一些建議。 建議您使用下列程式中包含的資料表，而不是遵循部署規劃工具建議。 


## <a name="set-up-a-configuration-server"></a>設定設定伺服器
 
設定伺服器容量會受到複寫的電腦數目所影響，而不會受到資料變換率的影響。 若要找出您是否需要額外的設定伺服器，請使用這些定義的 VM 限制。

**CPU** | **記憶體** | **快取磁片** | **已複寫的電腦限制**
 --- | --- | --- | ---
8 個 vCPU<br> 2個通訊端 * 4 核心 @ 2.5 Ghz | 16 GB | 600 GB | 最多550電腦<br> 假設每部電腦各有三個 100 GB 的磁片。

- 這些限制是以使用 OVF 範本設定的設定伺服器為基礎。
- 這些限制假設您不是使用預設在設定伺服器上執行的進程伺服器。

如果您需要新增設定伺服器，請遵循下列指示：

- 使用 OVF 範本設定 VMware VM 嚴重損壞修復的[設定伺服器](vmware-azure-deploy-configuration-server.md)。
- 手動設定實體伺服器的設定伺服器，或針對無法使用 OVF 範本的 VMware 部署[設定伺服器](physical-azure-set-up-source.md)。

當您設定伺服器時，請注意：

- 當您設定伺服器時，請務必考慮它所在的訂用帳戶和保存庫，因為在安裝之後不應變更這些帳戶和保存庫。 如果您需要變更保存庫，則必須解除佈建服務器與保存庫的關聯，然後重新註冊。 這會停止複寫保存庫中的 Vm。
- 如果您想要設定具有多張網路介面卡的設定伺服器，您應該在設定期間執行此動作。 在保存庫中註冊設定伺服器之後，您就無法這麼做。

## <a name="set-up-a-process-server"></a>設定進程伺服器

進程伺服器容量會受到資料變換率的影響，而不是由已啟用複寫的機器數目所影響。

- 針對大型部署，您應該一律至少有一個相應放大進程伺服器。
- 若要找出您是否需要額外的伺服器，請使用下表。
- 建議您新增具有最高規格的伺服器。 


**CPU** | **記憶體** | **快取磁片** | **流失率**
 --- | --- | --- | --- 
12 個 vCPU<br> 2個通訊端 * 6 核心 @ 2.5 Ghz | 24 GB | 1 GB | 一天最多 2 TB

設定進程伺服器，如下所示：

1. 檢查 [必要條件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 在 [入口網站](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)中或從 [命令列](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)安裝伺服器。
3. 將複寫的機器設定為使用新的伺服器。 如果您已經有機器複寫：
    - 您可以 [將](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) 整個進程伺服器工作負載移至新的進程伺服器。
    - 或者，您可以 [將](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) 特定 vm 移至新的進程伺服器。



## <a name="enable-large-scale-replication"></a>啟用大規模複寫

在規劃容量和部署所需的元件和基礎結構之後，請啟用大量 Vm 的複寫。

1. 將機器排序為批次。 您可以針對批次內的 Vm 啟用複寫，然後繼續進行下一個批次。

    - 針對 VMware Vm，您可以在部署規劃工具報表中使用 [建議的 VM 批次大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) 。
    - 針對實體機器，建議您根據具有類似資料大小和數量的機器，以及可用的網路輸送量來識別批次。 其目標是要批次處理可能在相同時間內完成初始複寫的機器。
    
2. 如果機器的磁片變換很高，或超過部署 thePlanner 中的限制，您可以將不 (需要複寫的非重要檔案（例如記錄傾印或暫存檔）移) 關閉電腦上。 針對 VMware Vm，您可以將這些檔案移至不同的磁片，然後將 [該磁片](vmware-azure-exclude-disk.md) 從複寫中排除。
3. 在您啟用複寫之前，請先檢查電腦是否符合複寫 [需求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 為 [VMware vm](vmware-azure-set-up-replication.md#create-a-policy) 或 [實體伺服器](physical-azure-disaster-recovery.md#create-a-replication-policy)設定複寫原則。
5. 啟用 [VMware vm](vmware-azure-enable-replication.md) 或 [實體伺服器](physical-azure-disaster-recovery.md#enable-replication)的複寫。 這會啟動所選機器的初始複寫。

## <a name="monitor-your-deployment"></a>監視您的部署

在您開始第一批 Vm 的複寫之後，請開始監視您的部署，如下所示：  

1. 指派嚴重損壞修復系統管理員來監視複寫機器的健全狀況狀態。
2. [監視](site-recovery-monitor-and-troubleshoot.md) 已複寫專案和基礎結構的事件。
3. 監視相應放大進程伺服器[的健康](vmware-physical-azure-monitor-process-server.md)情況。
4. 註冊以取得事件的 [電子郵件通知](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) ，以簡化監視。
5. 進行週期性嚴重損壞 [修復演練](site-recovery-test-failover-to-azure.md)，以確保一切都能如預期般運作。


## <a name="plan-for-large-scale-failovers"></a>規劃大規模容錯移轉

在發生嚴重損壞的情況下，您可能需要將大量的機器/工作負載容錯移轉至 Azure。 為這種類型的事件做好準備，如下所示。

您可以預先準備進行容錯移轉，如下所示：

- [準備您的基礎結構和 vm](#plan-infrastructure-and-vm-connectivity) ，以便在容錯移轉後可使用您的工作負載，讓使用者可以存取 Azure vm。
- 請注意本檔稍早的 [容錯移轉限制](#failover-limits) 。 請確定您的容錯移轉會落在這些限制範圍內。
- 執行週期性嚴重損壞 [修復演練](site-recovery-test-failover-to-azure.md)。 演練說明：
    - 在容錯移轉前找出部署中的差距。
    - 估計您應用程式的端對端 RTO。
    - 估計您工作負載的端對端 RPO。
    - 識別 IP 位址範圍衝突。
    - 當您執行演練時，建議您不要使用生產網路進行演練，並在每次演練之後清除測試容錯移轉。

若要執行大規模的容錯移轉，建議您執行下列動作：

1. 建立工作負載容錯移轉的復原方案。
    - 每個復原方案都可以觸發最多100部機器的容錯移轉。
    - [深入了解](recovery-plan-overview.md) 復原計劃。
2. 將 Azure 自動化 runbook 腳本新增至復原方案，以將 Azure 上的任何手動工作自動化。 一般工作包括設定負載平衡器、更新 DNS 等等。 [深入了解](site-recovery-runbook-automation.md)
2. 在容錯移轉之前，請先準備 Windows 機器，使其符合 Azure 環境。 符合的電腦的[容錯移轉限制](#plan-azure-subscriptions-and-quotas)較高。 [深入瞭解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) runbook。
4.  使用 [AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell Cmdlet 來觸發容錯移轉，以及復原方案。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
