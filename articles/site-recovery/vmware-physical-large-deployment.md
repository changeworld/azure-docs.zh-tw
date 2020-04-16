---
title: 使用 Azure 網站恢復擴展 VMware/物理災難復原
description: 瞭解如何為具有 Azure 網站恢復的大量本地 VMware VM 或物理伺服器設置災難恢復。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409778"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>為 VMware VM/物理伺服器大規模設定災難復原

本文介紹如何使用[Azure 網站恢復](site-recovery-overview.md)服務為生產環境中的大量本地 Vm 或物理伺服器設置災難恢復到 Azure,以便進行大量(> 1000)。


## <a name="define-your-bcdr-strategy"></a>定義 BCDR 戰略

作為業務連續性和災難恢復 (BCDR) 策略的一部分,您可以為業務應用和工作負載定義復原點目標 (RPO) 和復原時間目標 (RPO)。 RTO 測量必須還原和可用的業務應用或流程的時間和服務級別,以避免連續性問題。
- 網站恢復為 VMware VM 和實體伺服器提供連續複製,並為 RTO 提供[SLA。](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)
- 在為 VMware VM 規劃大規模災難恢復並找出所需的 Azure 資源時,可以指定將用於容量計算的 RTO 值。


## <a name="best-practices"></a>最佳作法

大規模災難恢復的一些一般最佳實踐。 本文檔的下一節將更詳細地討論這些最佳實踐。

- **確定目標要求**:在設置災難恢復之前,估計 Azure 中的容量和資源需求。
- **規劃網站恢復元件**:找出需要滿足估計容量的網站恢復元件(配置伺服器、進程伺服器)。
- **設置一個或多個橫向擴展進程伺服器**:不要使用配置伺服器上預設運行的進程伺服器。 
- **運行最新更新**:網站恢復團隊定期發佈新版本的網站恢復元件,並且應確保運行最新版本。 為了協助實作此功能,請追蹤更新[的功能](site-recovery-whats-new.md),並在更新發佈時[開啟並安裝更新](service-updates-how-to.md)。
- **主動監視**:在啟動和運行災難恢復時,應主動監視複製計算機以及基礎結構資源的狀態和運行狀況。
- **災難恢復演練**:應定期進行災難恢復演練。 這些不會影響生產環境,但有助於確保故障轉移到 Azure 將在需要時按預期工作。



## <a name="gather-capacity-planning-information"></a>收集容量規劃資訊

收集有關本地環境的資訊,以幫助評估和估計目標 (Azure) 容量需求。
- 對於 VMware,運行 VMware VM 的部署規劃器來執行此操作。
- 對於物理伺服器,手動收集資訊。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>執行 VMware VM 部署規劃器

部署規劃器可説明您收集有關 VMware 本地環境的資訊。

- 在表示 VM 的典型改動的時間段內運行部署規劃器。 這將產生更準確的估計和建議。
- 我們建議您在設定伺服器計算機上運行部署計畫器,因為規劃器計算其運行的伺服器的輸送量。 [詳細了解](site-recovery-vmware-deployment-planner-run.md#get-throughput)測量輸送量。
- 如果尚未設定設定伺服器:
    - [獲取](vmware-physical-azure-config-process-server-overview.md)站點恢復元件的概述。
    - [設置設定伺服器](vmware-azure-deploy-configuration-server.md),以便運行部署規劃器。

然後運行規劃器,如下所示:

1. [瞭解](site-recovery-deployment-planner.md)部署規劃器。 你可以從門戶下載最新版本,或[直接下載它](https://aka.ms/asr-deployment-planner)。
2. 查看部署規劃器[的先決條件](site-recovery-deployment-planner.md#prerequisites)和[最新更新](site-recovery-deployment-planner-history.md),然後[下載並提取](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)該工具。
3. [在設定伺服器上執行部署規劃器](site-recovery-vmware-deployment-planner-run.md)。
4. [生成報告](site-recovery-vmware-deployment-planner-run.md#generate-report)以匯總估計和建議。
5. 分析[報告建議](site-recovery-vmware-deployment-planner-analyze-report.md)與[成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 默認情況下,該工具配置為對多達 1000 個 VM 進行配置檔和生成報告。 您可以通過在 ASRDeploymentPlanner.exe.config 檔中增加 MaxVM 支援鍵值來更改此限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>計畫目標(Azure)要求和容量

使用收集的估計和建議,您可以規劃目標資源和容量。 如果執行 VMware VM 的部署規劃器,則可以使用許多[報表建議](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)來説明您。

- **相容 VM**:使用此編號可以識別準備好向 Azure 進行災難恢復的 VM 數。 有關網路頻寬和 Azure 內核的建議基於此數位。
- **所需的網路頻寬**:注意相容 VM 的增量複製所需的頻寬。 
    - 運行規劃器時,可以在幾分鐘內指定所需的 RPO。 這些建議顯示了滿足該 RPO 100% 和 90% 時間所需的頻寬。 
    - 網路頻寬建議考慮了規劃器中推薦的配置伺服器和進程伺服器總數所需的頻寬。
- **必需的 Azure 內核**:根據相容 VM 的數量,記下目標 Azure 區域中所需的內核數。 如果沒有足夠的內核,在故障轉移網站恢復時將無法創建所需的 Azure VM。
- **建議的 VM 批次處理大小**:建議的批次處理大小基於在預設情況下在 72 小時內完成批次處理的初始複製的能力,同時滿足 100% 的 RPO。 可以修改小時值。

可以使用這些建議規劃 Azure 資源、網路頻寬和 VM 批處理。

## <a name="plan-azure-subscriptions-and-quotas"></a>計畫 Azure 訂閱和配額

我們希望確保目標訂閱中的可用配額足以處理故障轉移。

**Task** | **詳細資料** | **動作**
--- | --- | ---
**檢查核心** | 如果可用配額中的內核不等於或超過故障轉移時的總目標計數,故障轉移將失敗。 | 對於 VMware VM,請檢查目標訂閱中有足夠的內核來滿足部署規劃器核心建議。<br/><br/> 對於物理伺服器,請檢查 Azure 內核是否符合手動估計。<br/><br/> 要檢查配額,請在 Azure 門戶>**訂閱**中,按下 **「使用方式 + 配額**」。<br/><br/> [瞭解有關](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)增加配額的更多詳細資訊。
**檢查容錯移轉限制** | 故障轉移的數量不能超過網站恢復故障轉移限制。 |  如果故障轉移超過限制,則可以添加訂閱,並故障轉移到多個訂閱,或增加訂閱的配額。 


### <a name="failover-limits"></a>容錯移轉限制

這些限制指示網站恢復在一小時內支援的故障轉移數,假設每台計算機有三個磁碟。

遵守是什麼意思? 要啟動 Azure VM,Azure 要求某些驅動程式處於啟動啟動狀態,並且將 DHCP 等服務設置為自動啟動。
- 符合要求的計算機已經具備了這些設置。
- 對於運行 Windows 的電腦,您可以主動檢查合規性,並在需要時使其符合要求。 [深入了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux 計算機僅在故障轉移時才符合要求。

**計算機符合 Azure?** | **Azure VM 限制(託管磁碟故障轉移)**
--- | --- 
是 | 2000
否 | 1000

- 限制假定訂閱的目標區域中正在進行最少的其他作業。
- 某些 Azure 區域較小,並且限制可能稍低。

## <a name="plan-infrastructure-and-vm-connectivity"></a>規劃基礎架構和 VM 連線

故障轉移到 Azure 後,需要工作負荷像在本地操作一樣運行,並使用戶能夠訪問在 Azure VM 上運行的工作負載。

- [瞭解有關](site-recovery-active-directory.md#test-failover-considerations)將活動目錄或 DNS 本地基礎結構故障到 Azure 的詳細資訊。
- [詳細瞭解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)在故障轉移後準備連接到 Azure VM。



## <a name="plan-for-source-capacity-and-requirements"></a>規劃來源容量和需求

請務必擁有足夠的配置伺服器和橫向擴展進程伺服器,以滿足容量要求。 開始大規模部署時,請從單個配置伺服器和單個橫向擴展進程伺服器開始。 達到規定限制時,添加其他伺服器。

>[!NOTE]
> 對於 VMware VM,部署規劃器會針對所需的配置和處理伺服器提出一些建議。 我們建議您使用以下過程中包括的表,而不是遵循部署規劃器的建議。 


## <a name="set-up-a-configuration-server"></a>設定設定伺服器
 
配置伺服器容量受複製的計算機數(而不是數據改動率)的影響。 要確定是否需要其他配置伺服器,請使用這些定義的 VM 限制。

**Cpu** | **記憶體** | **快取磁碟** | **複製的電腦限制**
 --- | --- | --- | ---
8 個 vCPU<br> 2 個插座 = 4 個內核 = 2.5 Ghz | 16 GB | 600 GB | 多達 550 台機器<br> 假定每台電腦有三個磁碟,每個磁碟為 100 GB。

- 這些限制基於使用 OVF 範本設定的設定伺服器。
- 限制假定您不使用配置伺服器上預設運行的進程伺服器。

如果需要新增新的設定伺服器,請按照以下說明操作:

- 使用 OVF 樣本為 VMware VM 災難復[原設定設定伺服器](vmware-azure-deploy-configuration-server.md)。
- 為物理伺服器或無法使用 OVF 樣本的 VMware 部署手動[設定設定伺服器](physical-azure-set-up-source.md)。

設定設定伺服器時,請注意:

- 設置配置伺服器時,必須考慮它所在的訂閱和保管庫,因為設置后不應更改這些訂閱和保管庫。 如果需要更改保管庫,必須取消配置伺服器與保管庫的關聯,然後重新註冊它。 這將停止在保管庫中複製 VM。
- 如果要設置具有多個網路適配器的配置伺服器,則應在設置期間執行此操作。 在保管庫中註冊配置伺服器后,無法執行此操作。

## <a name="set-up-a-process-server"></a>設定行程伺服器

進程伺服器容量受數據改動率的影響,而不是受啟用複製的計算機數的影響。

- 對於大型部署,應始終至少有一個橫向擴展進程伺服器。
- 要確定是否需要其他伺服器,請使用下表。
- 我們建議您添加具有最高規格的伺服器。 


**Cpu** | **記憶體** | **快取磁碟** | **流失率**
 --- | --- | --- | --- 
12 個 vCPU<br> 2 個插座*6 個內核 = 2.5 Ghz | 24 GB | 1 GB | 每天高達 2 TB

設定程序伺服器,如下所示:

1. 請檢閱[必要條件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 在[門戶](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)或命令[行](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)中安裝伺服器。
3. 將複製的計算機配置為使用新伺服器。 如果您已經有電腦複製:
    - 您可以將整個程序伺服器工作負載[移動到](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)新的程序伺服器。
    - 或者,您可以將特定的 VM[移至](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)新的程序伺服器。



## <a name="enable-large-scale-replication"></a>開啟大規模複製

規劃容量並部署所需的元件和基礎結構後,為大量 VM 啟用複製。

1. 將計算機分批分類。 為批次處理中的 VM 啟用複製,然後轉到下一個批次處理。

    - 對 VMware VM,您可以在部署規劃器報告中[使用建議的 VM 批次的大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)。
    - 對於物理計算機,我們建議您根據具有類似大小和數量的計算機以及可用網路輸送量來標識批處理。 目標是對可能在相同時間內完成初始複製的計算機進行批處理。
    
2. 如果電腦的磁碟改動率很高,或者在部署規劃器中超過限制,則可以將不需要複製的非關鍵檔(如日誌轉儲或臨時檔)移離計算機。 對於 VMware VM,您可以將這些檔案移至所有磁碟,然後[從複製中排除該磁碟](vmware-azure-exclude-disk.md)。
3. 在開啟複製之前,請檢查電腦是否符合[複製要求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 為[VMware VM](vmware-azure-set-up-replication.md#create-a-policy)或[實體伺服器](physical-azure-disaster-recovery.md#create-a-replication-policy)配置複製策略。
5. 啟用[VMware VM](vmware-azure-enable-replication.md)或[物理伺服器](physical-azure-disaster-recovery.md#enable-replication)的複製。 這將啟動所選計算機的初始複製。

## <a name="monitor-your-deployment"></a>監視您的部署

啟動第一批 VM 的複製後,開始監視部署,如下所示:  

1. 分配災難恢復管理員以監視複製計算機的運行狀況。
2. [監視](site-recovery-monitor-and-troubleshoot.md)複製項和基礎結構的事件。
3. [監視](vmware-physical-azure-monitor-process-server.md)橫向擴展進程伺服器的運行狀況。
4. 註冊以獲取事件[的電子郵件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications),以便更輕鬆地進行監視。
5. 定期進行[災後恢復演練](site-recovery-test-failover-to-azure.md),確保一切按預期工作。


## <a name="plan-for-large-scale-failovers"></a>排程大規模故障移轉

如果發生災難,可能需要將大量計算機/工作負荷故障轉移到 Azure。 準備這種類型的事件,如下所示。

您可以提前準備故障轉移,如下所示:

- [準備基礎結構和 VM,](#plan-infrastructure-and-vm-connectivity)以便工作負荷在故障轉移後可用,以便用戶可以存取 Azure VM。
- 請注意本文檔前面的限制故障[轉移](#failover-limits)限制。 確保故障轉移將屬於這些限制。
- 定期執行[災難復原演習](site-recovery-test-failover-to-azure.md)。 鑽頭有助於:
    - 在故障轉移之前查找部署中的漏洞。
    - 估計應用的端到端RTO。
    - 估計工作負載的端到端 RPO。
    - 識別 IP 位址範圍衝突。
    - 在運行鑽頭時,我們建議您不要將生產網路用於鑽頭,避免在生產和測試網路中使用相同的子網名稱,並在每次鑽頭後清理測試故障轉移。

要運行大規模故障轉移,我們建議執行以下操作:

1. 為工作負載故障轉移創建恢復計劃。
    - 每個恢復計劃可以觸發多達 100 台電腦的故障轉移。
    - [深入了解](recovery-plan-overview.md) 復原計劃。
2. 將 Azure 自動化執行簿文本添加到恢復計畫,以自動執行 Azure 上的任何手動任務。 典型的任務包括配置負載均衡器、更新 DNS 等。 [深入了解](site-recovery-runbook-automation.md)
2. 在故障轉移之前,請準備 Windows 計算機,以便它們符合 Azure 環境。 對於符合要求的電腦,[故障轉移限制](#plan-azure-subscriptions-and-quotas)更高。 [瞭解有關](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)Runbook 的更多詳細資訊。
4.  使用[啟動-Az 恢復服務觸發](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0)故障轉移,並結合恢復計劃進行計劃故障轉移。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
