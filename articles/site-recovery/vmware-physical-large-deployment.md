---
title: 使用 Azure Site Recovery 調整 VMware/實體嚴重損壞修復
description: 瞭解如何使用 Azure Site Recovery 將大量內部部署 VMware Vm 或實體伺服器的嚴重損壞修復設定到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409778"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>針對 VMware Vm/實體伺服器設定大規模的嚴重損壞修復

本文說明如何使用[Azure Site Recovery](site-recovery-overview.md)服務，針對生產環境中的內部部署 VMware vm 或實體伺服器，將大量（> 1000）損毀修復設定為 Azure。


## <a name="define-your-bcdr-strategy"></a>定義您的 BCDR 策略

作為商務持續性和嚴重損壞修復（BCDR）策略的一部分，您可以定義商務應用程式和工作負載的復原點目標（Rpo）和復原時間目標（Rto）。 RTO 會測量商務應用程式或進程必須還原並可供使用的時間和服務層級，以避免持續性問題。
- Site Recovery 為 VMware Vm 和實體伺服器提供連續複寫，以及 RTO 的[SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 。
- 當您針對 VMware Vm 規劃大規模的嚴重損壞修復，並找出所需的 Azure 資源時，您可以指定將用於容量計算的 RTO 值。


## <a name="best-practices"></a>最佳作法

大規模嚴重損壞修復的一些一般最佳作法。 這些最佳作法會在檔的下一節中更詳細地討論。

- **識別目標需求**：在您設定嚴重損壞修復之前，預估 Azure 中的容量和資源需求。
- **規劃 Site Recovery 元件**：找出您所需的 Site Recovery 元件（設定伺服器、進程伺服器），以符合您的估計容量。
- **設定一或多個相應放大進程伺服器**：請勿使用設定伺服器上預設執行的進程伺服器。 
- **執行最新的更新**： Site Recovery 小組會定期發行 Site Recovery 元件的新版本，您應該確定您執行的是最新版本。 為協助達成此目標，請追蹤更新的[新功能](site-recovery-whats-new.md)，並在發行時[啟用和安裝更新](service-updates-how-to.md)。
- **主動監視**：當您啟動並執行嚴重損壞修復時，應該主動監視複寫機器和基礎結構資源的狀態和健康情況。
- 嚴重損壞**修復演練**：您應該定期執行嚴重損壞修復演練。 這些不會影響您的生產環境，但有助於確保容錯移轉至 Azure 會在需要時如預期般運作。



## <a name="gather-capacity-planning-information"></a>收集容量規劃資訊

收集您內部部署環境的相關資訊，以協助評估和預估目標（Azure）容量需求。
- 若為 VMware，請執行 VMware Vm 的部署規劃工具。
- 若為實體伺服器，請手動搜集資訊。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>執行 VMware Vm 的部署規劃工具

此部署規劃工具可協助您收集 VMware 內部部署環境的相關資訊。

- 在代表您 Vm 的一般變換期間執行部署規劃工具。 這會產生更精確的評估和建議。
- 我們建議您在設定伺服器電腦上執行部署規劃工具，因為 Planner 會計算它正在執行之伺服器的輸送量。 [深入瞭解](site-recovery-vmware-deployment-planner-run.md#get-throughput)如何測量輸送量。
- 如果您還沒有設定伺服器：
    - [取得](vmware-physical-azure-config-process-server-overview.md)Site Recovery 元件的總覽。
    - [設定伺服器](vmware-azure-deploy-configuration-server.md)，以便在其上執行部署規劃工具。

然後執行規劃工具，如下所示：

1. [深入瞭解](site-recovery-deployment-planner.md)部署規劃工具。 您可以從入口網站下載最新版本，或[直接下載](https://aka.ms/asr-deployment-planner)。
2. 請參閱部署規劃工具的[必要條件](site-recovery-deployment-planner.md#prerequisites)和[最新更新](site-recovery-deployment-planner-history.md)，並[下載並解壓縮](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)工具。
3. 在設定伺服器上[執行部署規劃工具](site-recovery-vmware-deployment-planner-run.md)。
4. [產生報告](site-recovery-vmware-deployment-planner-run.md#generate-report)以摘要預估和建議。
5. 分析[報告建議](site-recovery-vmware-deployment-planner-analyze-report.md)和[成本估計](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 根據預設，此工具設定為分析並產生最多1000個 Vm 的報告。 您可以藉由增加 Asrdeploymentplanner.exe.config 中的 Maxvmssupported 為機碼值來變更此限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>規劃目標（Azure）需求和容量

您可以使用所收集的評估和建議，規劃目標資源和容量。 如果您已執行 VMware Vm 的部署規劃工具，您可以使用一些[報告建議](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)來協助您。

- **相容的 vm**.. 使用此號碼來識別已準備好要損毀修復至 Azure 的 vm 數目。 有關網路頻寬和 Azure 核心的建議是以此數目為依據。
- **所需的網路頻寬**：請記下您需要的頻寬，以進行相容 vm 的差異複寫。 
    - 當您執行 Planner 時，您會指定所需的 RPO （以分鐘為單位）。 建議會顯示符合當時 RPO 100% 和90% 所需的頻寬。 
    - 網路頻寬建議會考慮規劃工具中建議的設定伺服器和進程伺服器總數所需的頻寬。
- **必要的 azure 核心**：根據相容的 vm 數目，記下目標 Azure 區域中所需的核心數目。 如果您沒有足夠的核心，在容錯移轉時 Site Recovery 將無法建立所需的 Azure Vm。
- **建議的 VM 批次大小**：建議的批次大小是根據預設在72小時內完成批次初始複寫的能力，同時符合100% 的 RPO。 可以修改小時值。

您可以使用這些建議來規劃 Azure 資源、網路頻寬和 VM 批次處理。

## <a name="plan-azure-subscriptions-and-quotas"></a>規劃 Azure 訂用帳戶和配額

我們想要確保目標訂用帳戶中的可用配額足以處理容錯移轉。

**Task** | **詳細資料** | **動作**
--- | --- | ---
**檢查核心** | 如果可用配額中的核心不等於或超過容錯移轉時的總目標數，容錯移轉將會失敗。 | 若為 VMware Vm，請檢查目標訂用帳戶中有足夠的核心，以符合部署規劃工具核心建議。<br/><br/> 針對實體伺服器，請檢查 Azure 核心是否符合您的手動估計。<br/><br/> 若要檢查配額，請在 [Azure 入口網站 >**訂**用帳戶中，按一下 [**使用量 + 配額**]。<br/><br/> [深入瞭解](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)增加配額。
**檢查容錯移轉限制** | 容錯移轉不得數目超過 Site Recovery 的容錯移轉限制。 |  如果容錯移轉超過限制，您可以新增訂閱、容錯移轉至多個訂用帳戶，或增加訂用帳戶的配額。 


### <a name="failover-limits"></a>容錯移轉限制

這些限制指出 Site Recovery 在一小時內支援的容錯移轉次數（假設每部機器有三個磁片）。

什麼是相符的意思？ 若要啟動 Azure VM，Azure 需要某些驅動程式處於開機啟動狀態，而 DHCP 等服務會設定為自動啟動。
- 符合規範的電腦就已準備好這些設定。
- 對於執行 Windows 的機器，您可以主動檢查合規性，並視需要使其符合規範。 [深入了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux 機器只會在容錯移轉時進入合規性狀態。

**機器符合 Azure 的規定嗎？** | **Azure VM 限制（受控磁片容錯移轉）**
--- | --- 
是 | 2000
否 | 1000

- 限制會假設訂用帳戶的目的地區域中有最少的其他作業正在進行中。
- 某些 Azure 區域較小，而且可能會有稍微較低的限制。

## <a name="plan-infrastructure-and-vm-connectivity"></a>規劃基礎結構和 VM 連線能力

在容錯移轉至 Azure 之後，您需要工作負載如同在內部部署環境運作，並可讓使用者存取在 Azure Vm 上執行的工作負載。

- [深入瞭解](site-recovery-active-directory.md#test-failover-considerations)如何將您的 ACTIVE DIRECTORY 或 DNS 內部部署基礎結構容錯移轉至 Azure。
- [深入瞭解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)如何準備在容錯移轉後連線至 Azure vm。



## <a name="plan-for-source-capacity-and-requirements"></a>規劃來源容量和需求

請務必擁有足夠的設定伺服器和相應放大進程伺服器，以符合容量需求。 當您開始進行大規模部署時，請從單一設定伺服器開始，然後使用單一相應放大進程伺服器。 當您達到指定的限制時，請新增額外的伺服器。

>[!NOTE]
> 針對 VMware Vm，部署規劃工具會對您所需的設定和進程伺服器做出一些建議。 建議您使用下列程式中所包含的資料表，而不是遵循部署規劃工具建議。 


## <a name="set-up-a-configuration-server"></a>設定設定伺服器
 
設定伺服器容量會受到複寫的機器數目影響，而不是依資料變換率。 若要找出您是否需要額外的設定伺服器，請使用這些定義的 VM 限制。

**使用率** | **記憶體** | **快取磁片** | **已複寫電腦限制**
 --- | --- | --- | ---
8 個 vCPU<br> 2個通訊端 * 4 核心 @ 2.5 Ghz | 16 GB | 600 GB | 最多550部機器<br> 假設每部機器各有三個 100 GB 的磁片。

- 這些限制是以使用 OVF 範本設定的設定伺服器為基礎。
- 這些限制假設您未使用設定伺服器上預設執行的進程伺服器。

如果您需要加入新的設定伺服器，請遵循下列指示：

- 使用 OVF 範本來設定 VMware VM 嚴重損壞修復的[設定伺服器](vmware-azure-deploy-configuration-server.md)。
- 針對實體伺服器或無法使用 OVF 範本的 VMware 部署，手動[設定設定伺服器](physical-azure-set-up-source.md)。

設定伺服器時，請注意：

- 設定伺服器時，請務必考慮其所在的訂用帳戶和保存庫，因為這些不應在安裝後變更。 如果您需要變更保存庫，則必須將設定伺服器與保存庫解除關聯，然後重新註冊。 這會停止複寫保存庫中的 Vm。
- 如果您想要設定具有多個網路介面卡的設定伺服器，您應該在設定期間執行此動作。 在保存庫中註冊設定伺服器之後，就無法執行此動作。

## <a name="set-up-a-process-server"></a>設定進程伺服器

進程伺服器容量會受到資料變換率的影響，而不是由已啟用複寫的機器數目所影響。

- 針對大型部署，您應該一律至少有一個相應放大進程伺服器。
- 若要找出您是否需要額外的伺服器，請使用下表。
- 我們建議您新增具有最高規格的伺服器。 


**使用率** | **記憶體** | **快取磁片** | **變換率**
 --- | --- | --- | --- 
12 個 vCPU<br> 2個通訊端 * 6 核心 @ 2.5 Ghz | 24 GB | 1 GB | 一天最多 2 TB

設定進程伺服器，如下所示：

1. 請參閱[必要條件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 在[入口網站](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)中或從[命令列](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)安裝伺服器。
3. 將複寫的機器設定為使用新的伺服器。 如果您已經有機器複寫：
    - 您可以[將](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)整個進程伺服器工作負載移到新的進程伺服器。
    - 或者，您可以[將](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)特定 vm 移至新的進程伺服器。



## <a name="enable-large-scale-replication"></a>啟用大規模複寫

在規劃容量並部署必要的元件和基礎結構之後，請為大量的 Vm 啟用複寫。

1. 將機器排序成批次。 您可以在批次中啟用 Vm 的複寫，然後繼續進行下一個批次。

    - 針對 VMware Vm，您可以在部署規劃工具報告中使用[建議的 VM 批次大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)。
    - 針對實體機器，建議您根據具有類似資料大小和數量的機器，以及可用的網路輸送量來識別批次。 其目的是要批次處理可能在同一時間內完成初始複寫的機器。
    
2. 如果電腦的磁片變換很高，或超過部署 thePlanner 的限制，您就可以將不需要複寫的非關鍵檔案（例如，記錄傾印或暫存檔案）移出電腦。 針對 VMware Vm，您可以將這些檔案移至不同的磁片，然後將[該磁片](vmware-azure-exclude-disk.md)從複寫中排除。
3. 啟用複寫之前，請檢查機器是否符合複寫[需求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 設定[VMware vm](vmware-azure-set-up-replication.md#create-a-policy)或[實體伺服器](physical-azure-disaster-recovery.md#create-a-replication-policy)的複寫原則。
5. 啟用[VMware vm](vmware-azure-enable-replication.md)或[實體伺服器](physical-azure-disaster-recovery.md#enable-replication)的複寫。 這會啟動所選電腦的初始複寫。

## <a name="monitor-your-deployment"></a>監視您的部署

開始執行第一批 Vm 的複寫之後，請依照下列方式開始監視您的部署：  

1. 指派嚴重損壞修復系統管理員，以監視複寫機器的健全狀況狀態。
2. [監視](site-recovery-monitor-and-troubleshoot.md)已複寫專案和基礎結構的事件。
3. [監視相應放大進程伺服器的健全狀況](vmware-physical-azure-monitor-process-server.md)。
4. 註冊以取得事件的[電子郵件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications)，以方便監視。
5. 進行週期性嚴重損壞[修復演練](site-recovery-test-failover-to-azure.md)，以確保一切都如預期般運作。


## <a name="plan-for-large-scale-failovers"></a>為大規模容錯移轉做規劃

萬一發生嚴重損壞，您可能需要將大量的機器/工作負載損毀修復到 Azure。 準備這種類型的事件，如下所示。

您可以事先準備進行容錯移轉，如下所示：

- [準備您的基礎結構和 vm](#plan-infrastructure-and-vm-connectivity) ，讓您的工作負載在容錯移轉之後可供使用，讓使用者可以存取 Azure vm。
- 請注意本檔稍早的[容錯移轉限制](#failover-limits)。 請確定您的容錯移轉會落在這些限制內。
- 執行一般嚴重損壞[修復演練](site-recovery-test-failover-to-azure.md)。 演練說明：
    - 在容錯移轉之前，尋找部署中的間隙。
    - 估計應用程式的端對端 RTO。
    - 估計工作負載的端對端 RPO。
    - 識別 IP 位址範圍衝突。
    - 當您執行演練時，建議您不要使用生產網路進行演練、避免在生產和測試網路中使用相同的子網名稱，以及在每次演練之後清除測試容錯移轉。

若要執行大規模的容錯移轉，我們建議下列各項：

1. 建立工作負載容錯移轉的復原計畫。
    - 每個復原方案都可以觸發最多100部機器的容錯移轉。
    - [深入了解](recovery-plan-overview.md) 復原計劃。
2. 將 Azure 自動化 runbook 腳本新增至復原方案，以將 Azure 上的任何手動工作自動化。 一般工作包括設定負載平衡器、更新 DNS 等。 [深入了解](site-recovery-runbook-automation.md)
2. 在容錯移轉之前，請準備 Windows 機器，使其符合 Azure 環境。 符合的電腦會有較高的[容錯移轉限制](#plan-azure-subscriptions-and-quotas)。 [深入瞭解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)runbook。
4.  使用[AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell Cmdlet 來觸發容錯移轉，以及復原方案。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
