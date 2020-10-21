---
title: Azure Migrate Server 遷移的常見問題
description: 取得有關使用 Azure Migrate Server 遷移來遷移機器的常見問題的解答。
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: b62110e6b2ce97cdd80ed91ee4b1e75d119c7c7d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315241"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 遷移：常見問題

本文將回答 Azure Migrate：伺服器遷移工具的常見問題。 如果您有其他問題，請查看下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [探索、評量和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中取得問題的解答

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate 是否將 UEFI 型機器轉換成 BIOS 型機器，並將其遷移至 Azure 作為 Azure 第1代 Vm？
Azure Migrate：伺服器遷移工具會將所有 UEFI 型機器遷移至 Azure 作為 Azure 第2代 Vm。 我們不再支援將 UEFI 型 Vm 轉換成 BIOS 型 Vm。 請注意，所有 BIOS 電腦都只會遷移至 Azure 作為 Azure 第1代 Vm。

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>如何將 UEFI 型機器遷移至 Azure 作為 Azure 第1代 Vm？
Azure Migrate：伺服器遷移工具會將 UEFI 型機器遷移至 Azure 作為 Azure 第2代 Vm。 如果您想要將它們遷移至 Azure 第1代 Vm，請在啟動複寫之前將開機類型轉換成 BIOS，然後使用 Azure Migrate：伺服器遷移工具來遷移至 Azure。
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>哪些作業系統支援將 UEFI 型機器遷移至 Azure？

| **以 UEFI 為基礎的電腦支援的作業系統** | **無代理程式 VMware 至 Azure**                                                                                                             | **無代理程式 Hyper-v 到 Azure** | **以代理程式為基礎的 VMware、實體和其他雲端到 Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019、2016、2012 R2、201                 | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 專業版、Windows 10 企業版                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04、18.04、19.04、19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1、8.0、7.8、7.7、7.6、7.5、7.4、7.0、6。x        | Y<br>                 _RHEL 8.x 需要 [手動準備](./prepare-for-migration.md#linux-machines)_   | Y                              | Y                                                          |
| 分幣 OS 8.1、8.0、7.7、7.6、7.5、7.4、6。x               | Y<br>_美分 OS 8.x 需要 [手動準備](./prepare-for-migration.md#linux-machines)_ | Y                              | Y                                                          |
| Oracle Linux 7.7、7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>是否可以使用 Azure Migrate 針對嚴重損壞修復案例所建立的復原服務保存庫？
我們不建議使用 Azure Migrate 所建立的復原服務保存庫來進行嚴重損壞修復案例。 這樣做可能會導致 Azure Migrate 中的開始複寫失敗。 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>要在哪裡安裝以代理程式為基礎之遷移的複寫設備？

複寫設備應該安裝在專用電腦上。 複寫設備不應該安裝在您想要複寫的來源機器上，也不應該安裝在您先前已安裝的 Azure Migrate 探索和評估設備上。 遵循 [教學](./tutorial-migrate-physical-virtual-machines.md) 課程以取得詳細資料。

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>如何將我的 AWS EC2 實例遷移至 Azure？

請參閱這 [篇文章](./tutorial-migrate-aws-virtual-machines.md) ，以探索、評估您的 AWS EC2 實例，並將其遷移至 Azure。

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>是否可以遷移執行 Amazon Linux 作業系統的 AWS Vm？

執行 Amazon Linux 的 Vm 無法依原樣遷移，因為只有 AWS 才支援 Amazon Linux OS。
若要遷移在 Amazon Linux 上執行的工作負載，您可以在 Azure 中啟動 CentOS/RHEL VM，並使用相關的工作負載移轉方法，遷移在 AWS Linux 電腦上執行的工作負載。 例如，視工作負載而定，可能有工作負載專屬的工具可協助移轉，例如適用於 Web 伺服器的資料庫或部署工具。

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>支援哪些地理位置以 Azure Migrate 進行遷移？

請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>是否可以使用相同的 Azure Migrate 專案來遷移至多個區域？

雖然您可以在 Azure Migrate 專案中建立多個區域的評量，但只能使用一個 Azure Migrate 專案將伺服器遷移至一個 Azure 區域。 您可以針對需要遷移至的每個區域，建立額外的 Azure Migrate 專案。

- 若為無代理程式 VMware 遷移，當您啟用第一次複寫時，目的地區域會遭到鎖定。
- 針對以代理程式為基礎的遷移 (VMware、實體伺服器和其他雲端) 的伺服器，當您設定複寫設備時，在入口網站上按一下 [建立資源] 按鈕，就會鎖定目的地區域。
- 針對無代理程式 Hyper-v 遷移，當您在設定 Hyper-v 複寫提供者時，在入口網站上按一下 [建立資源] 按鈕時，會鎖定目的地區域。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>我們可以使用相同的 Azure Migrate 專案來遷移至多個訂用帳戶嗎？ 

是，您可以在相同的目的地區域中，遷移至 Azure Migrate 專案的多個訂用帳戶。 您可以選取目標訂用帳戶，同時啟用電腦或一組機器的複寫。 目的地區域針對無代理程式 VMware 遷移，以及複寫設備和 Hyper-v 提供者安裝，分別用於代理程式的遷移和無代理程式 Hyper-v 遷移，而鎖定了首次複寫。

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure Migrate：伺服器遷移有哪些遷移選項？

Azure Migrate：伺服器遷移工具提供兩個選項，可讓您將來源伺服器/Vm 遷移至 Azure –無代理程式遷移和代理程式型的遷移。

不論選擇哪一種遷移選項，使用 Azure 遷移來遷移伺服器的第一個步驟：伺服器遷移是啟用伺服器的複寫。 這會對 Azure 執行虛擬機器/伺服器資料的初始複寫。 初始複寫完成之後，就會建立進行中的複寫 (進行中的差異同步) ，以將增量資料移轉至 Azure。 一旦作業到達差異同步階段，您就可以隨時選擇遷移至 Azure。  

以下是在決定遷移選項時要牢記在心的一些考慮。

**無代理程式遷移** 不需要將任何軟體 (代理程式) 部署到所要遷移的來源 vm/伺服器上。 無代理程式選項會整合虛擬化提供者所提供的功能，以協調複寫。
適用于 [VMware vm](./tutorial-migrate-vmware.md) 和 [Hyper-v Vm](./tutorial-migrate-hyper-v.md)的無代理程式複寫選項。

以代理程式為**基礎的遷移**需要在要遷移的來源 vm/機器上安裝 Azure Migrate 軟體 (代理程式) 。 以代理程式為基礎的選項並不依賴虛擬化平臺的複寫功能，因此可以搭配執行 x86/x64 架構的任何伺服器和代理程式型複寫方法所支援的作業系統版本使用。

以代理程式為基礎的遷移選項可用於 [VMware vm](./tutorial-migrate-vmware-agent.md)、 [hyper-v vm](./tutorial-migrate-physical-virtual-machines.md)、 [實體伺服器](./tutorial-migrate-physical-virtual-machines.md)、 [在 AWS](./tutorial-migrate-aws-virtual-machines.md)上執行的 vm、在 GCP 上執行的 vm，或在不同的虛擬化提供者上執行的 vm。 以代理程式為基礎的遷移會將您的電腦視為實體伺服器，以進行遷移。

雖然無代理程式遷移可針對 (VMWare 和 Hyper-v) 所支援案例的代理程式型複寫選項提供額外的便利性和簡單性，但您可能會想要考慮使用以代理程式為基礎的案例來進行下列使用案例：

- IOPS 受限的環境：無代理程式複寫使用快照集，並耗用儲存體 IOPS/頻寬。 如果您的環境中有儲存體/IOPS 的限制，我們建議以代理程式為基礎的遷移方法。
- 如果您沒有 vCenter Server，您可以將 VMware Vm 視為實體伺服器，並使用以代理程式為基礎的遷移工作流程。

若要深入瞭解，請參閱這 [篇文章](./server-migrate-overview.md) ，以比較 VMware 遷移的遷移選項。

## <a name="how-does-agentless-migration-work"></a>無代理程式遷移的運作方式為何？

Azure Migrate：伺服器遷移提供無代理程式複寫選項，可讓您遷移 VMware 虛擬機器和執行 Windows 或 Linux 的 Hyper-v 虛擬機器。 此工具也會為 Windows 和 Linux 伺服器提供另一個以代理程式為基礎的複寫選項，可用來在 VMware、Hyper-v、AWS、GCP 等上遷移實體伺服器，以及 x86/x64 虛擬機器。以代理程式為基礎的複寫選項需要在要遷移的伺服器/虛擬機器上安裝代理程式軟體，而在無代理程式選項中，不需要在虛擬機器上安裝任何軟體，因此可在代理程式複寫選項之外提供額外的便利性和簡單性。

無代理程式複寫選項的運作方式是使用虛擬化提供者提供的機制 (VMware、Hyper-v) 。 在 VMware 虛擬機器的情況下，無代理程式複寫機制會使用 VMware 快照集和 VMware 變更區塊追蹤技術，從虛擬機器磁片複寫資料。 這項機制類似于許多備份產品所使用的機制。 在 Hyper-v 虛擬機器的情況下，無代理程式複寫機制會使用 Hyper-v 複本的 VM 快照集和變更追蹤功能，從虛擬機器磁片複寫資料。

針對虛擬機器設定複寫時，它會先經歷初始複寫階段。 在初始複寫期間，會建立 VM 快照集，並將快照集磁片的完整資料複本複寫到您訂用帳戶中的受控磁片。 完成 VM 的初始複寫之後，複寫程式會轉換為累加式複寫， (delta replication) 階段。 在增量複寫階段中，自從上一個完成的複寫週期以來發生的資料變更會定期複寫並套用至複本受控磁片，因此會讓複寫與 VM 上發生的變更保持同步。 在 VMware 虛擬機器的情況下，VMware 變更區塊追蹤技術是用來追蹤複寫週期之間的變更。 在複寫週期開始時，會建立 VM 快照集，並使用變更的區塊追蹤來取得目前快照集與最後一個成功複寫快照之間的變更。 如此一來，就只需要複寫自上一次完成複寫週期之後變更的資料，才能讓 VM 的複寫保持同步。在每個複寫週期結束時，快照集會釋出，並針對虛擬機器執行快照集匯總。 同樣地，在 Hyper-v 虛擬機器的情況下，也會使用 Hyper-v 複本變更追蹤引擎來追蹤連續複寫週期之間的變更。
當您在複寫的虛擬機器上執行遷移作業時，您可以選擇關閉內部部署虛擬機器，並執行最後一次的累加式複寫，以確保零的資料遺失。 執行遷移選項時，會使用對應至虛擬機器的複本受控磁片，在 Azure 中建立虛擬機器。

若要開始使用，請參閱 [VMware 無代理程式遷移](./tutorial-migrate-vmware.md) 和 [hyper-v 無代理程式遷移](./tutorial-migrate-hyper-v.md) 教學課程。

## <a name="how-does-agent-based-migration-work"></a>以代理程式為基礎的遷移如何運作？

除了適用于 VMware 虛擬機器和 Hyper-v 虛擬機器的無代理程式遷移選項之外，伺服器遷移工具還提供以代理程式為基礎的遷移選項，可遷移在實體伺服器上執行的 Windows 和 Linux 伺服器，或在 VMware、Hyper-v、AWS、Google Cloud Platform 等上以 x86/x64 虛擬機器的形式執行。

以代理程式為基礎的遷移方法會使用在要遷移的伺服器上所安裝的代理程式軟體，將伺服器資料複寫至 Azure。 複寫程式會使用卸載架構，在此架構中，代理程式會將複寫資料轉送至稱為複寫設備或設定伺服器 (或向外延展處理伺服器) 的專用複寫伺服器。 [深入瞭解](./agent-based-migration-architecture.md) 以代理程式為基礎的遷移選項如何運作。 

注意：複寫設備與 Azure Migrate 探索設備不同，而且必須安裝在不同的/專用電腦上。

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>如何? 測量我的遷移的頻寬需求嗎？

將資料複寫至 Azure 的頻寬取決於各種因素，而且是內部部署 Azure Migrate 設備可讀取資料並將資料複寫至 Azure 的速度的功能。 複寫有兩個階段：初始複寫和差異複寫。

當 VM 的複寫開始時，會在複寫磁片的完整複本時，進行初始複寫週期。 初始複寫完成後，累加式複寫迴圈 (差異週期) 會定期排程，以傳送先前複寫週期以來發生的任何變更。

### <a name="agentless-vmware-vm-migration"></a>無代理程式 VMware VM 遷移

您可以根據需要在 wave 中移動的資料量，以及您希望初始複寫完成的時間來滿足頻寬需求 (您最好是先讓初始複寫在實際的遷移視窗之前完成至少3-4 天的時間，並讓您有足夠的時間在時間範圍內執行測試遷移，並在視窗) 期間維持最少停機時間。

您可以使用下列公式來估計無代理程式 VMware VM 遷移所需的頻寬或時間：

完成初始複寫的時間 = {磁片大小 (或使用大小（如果有的話）) * 0.7 (假設有30% 壓縮平均–保守估計) }/bandwidth 可用於複寫。

### <a name="agent-based-vmware-vm-migration"></a>以代理程式為基礎的 VMware VM 遷移

針對以代理程式為基礎的複寫方法，部署規劃工具可協助分析環境中的資料變換，並協助預測必要的頻寬需求。 若要深入瞭解，請參閱這 [篇文章](./agent-based-migration-architecture.md#plan-vmware-deployment)。 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>使用 Azure Migrate 設備進行無代理程式 VMware 複寫時，如何? 節流閥複寫？  

您可以使用 >new-netqospolicy 進行節流。 例如：

要在 >new-netqospolicy 中使用的 AppNamePrefix 是 "GatewayWindowsService.exe"。 您可以建立 Azure Migrate 設備的原則，藉由建立如下的原則來節流設備的複寫流量：

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>如何將資料從內部內部部署環境傳輸到 Azure？ 它會在傳輸前加密嗎？

無代理程式複寫案例中的 Azure Migrate 設備會在上傳之前壓縮資料並進行加密。 資料會透過 HTTPs 透過安全通道傳輸，並使用 TLS 1.2 或更新版本。 此外，當您將資料保存到雲端 (待用加密) 時，Azure 儲存體會自動將您的資料加密。  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>流失率如何影響無代理程式複寫？

因為無代理程式複寫會在資料中折迭，所以變換 *模式* 比變換 *率*更重要。 再次寫入檔案時，速率不會有很大的影響。 不過，寫入其他每個磁區的模式會導致下一個週期的高變換率。 由於我們會將傳輸的資料量降到最低，因此我們可以在排程下一個週期之前，讓資料盡可能折迭。

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>複寫週期排程的頻率為何？

排程下一個複寫週期的公式是 (先前的週期時間/2) 或一小時（以較高者為准）。

例如，如果 VM 需要四個小時進行差異迴圈，則會在兩個小時內排程下一個週期，而不是在下一個小時。 此程式會在初始複寫之後，立即排程第一個差異週期時的不同。

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>如何? 將在 VMware/Hyper-v 上執行的 Windows Server 2003 遷移至 Azure 嗎？

[Windows Server 2003 延伸支援](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 已于2015年7月14日結束。  Azure 支援團隊會持續協助您針對在 Azure 上執行 Windows Server 2003 的問題進行疑難排解。 不過，這項支援僅限於不需要 OS 層級疑難排解或修補程式的問題。
建議您將應用程式遷移至執行較新版本 Windows Server 的 Azure 實例，以確保您有效地運用 Azure 雲端的彈性和可靠性。

但是，如果您仍然選擇將您的 Windows Server 2003 遷移至 Azure，您可以使用 Azure Migrate：伺服器遷移工具（如果您的 Windows Server 是在 VMware 或 Hyper-v 上執行的 VM），請參閱這篇文章，以 [準備要進行遷移的 Windows server 2003 電腦](./prepare-windows-server-2003-migration.md)。

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>測試遷移和遷移作業之間有何差異？

測試遷移提供一種方法，可在實際遷移之前測試及驗證遷移。 測試遷移的運作方式是讓您在 Azure 中的沙箱環境中建立複寫 Vm 的測試複本。 沙箱環境是由您指定的測試虛擬網路所劃定。 測試遷移作業不會造成干擾，而應用程式會繼續在來源執行，同時讓您在隔離的沙箱環境中對複製的複本執行測試。 您可以視需要執行多個測試，以驗證遷移、執行應用程式測試，並解決實際遷移之前的任何問題。

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>在遷移之後，Azure 是否支援 Windows Server 2008 和 2008 R2？

您可以將內部部署的 Windows Server 2008 和 2008 R2 伺服器遷移至 Azure 虛擬機器，並在終止支援日期後的三年內取得延長的安全性更新，不會超過執行虛擬機器的費用。 您可以使用 Azure Migrate：伺服器遷移工具來遷移您的 Windows Server 2008 和 2008 R2 工作負載。

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>是否有 Azure Migrate 的復原選項？

您可以使用 [測試遷移] 選項來驗證您在 Azure 中的應用程式功能和效能。 您可以執行任意數目的測試遷移，並可在透過測試遷移作業建立信心之後，執行最終的遷移。 測試遷移並不會影響內部部署電腦，其仍可運作並繼續複寫，直到您執行實際的遷移為止。 如果在測試遷移 UAT 期間發生任何錯誤，您可以選擇延後最後的遷移，並讓來源 VM/伺服器執行並複寫至 Azure。 解決錯誤之後，您可以重新嘗試進行最後的遷移。  
注意：當您執行最後一次遷移至 Azure 且內部部署來源電腦已關閉之後，您就無法從 Azure 復原到您的內部部署環境。

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>我可以選取要用於測試遷移的虛擬網路和子網嗎？

您可以選取用於測試遷移的虛擬網路。 子網會根據下列邏輯自動選取：

- 如果在啟用複寫時，將) 預設的子網 (指定為輸入，則 Azure Migrate 會優先使用為測試遷移選取的虛擬網路中具有相同名稱的子網。
- 如果找不到具有相同名稱的子網，則 Azure Migrate 會依字母順序選取不是閘道/應用程式閘道/防火牆/防禦子網的第一個子網。

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>為什麼我的伺服器已停用 [測試遷移] 按鈕？

在下列案例中，[測試遷移] 按鈕可能處於停用狀態：

- 在 VM 的初始複寫 (IR) 完成之前，您無法開始進行測試遷移。 [測試遷移] 按鈕將會停用，直到 IR 進程完成為止。 一旦您的 VM 處於 delta 同步階段，您就可以執行測試遷移。
- 如果測試遷移已完成，但未針對該 VM 執行測試遷移清除，則可以停用該按鈕。 請執行測試遷移清除，然後再次嘗試操作。

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>如果我未清除測試遷移，會發生什麼事？

測試遷移藉由使用複寫的資料建立測試 Azure VM，來模擬實際的遷移。 將複寫的資料複製到目標資源群組時，將會將伺服器部署至目標資源群組 (選取的伺服器，並使用 "-test" 尾碼來啟用複寫) 。 測試遷移適用于驗證服務器功能，因此會將遷移後問題降至最低。 如果測試遷移未在測試後進行清理，則測試虛擬機器將繼續在 Azure 中執行，而且會產生費用。 若要清除測試後的測試，請移至伺服器遷移工具中的 [複寫機器]，並使用電腦上的 [清除測試遷移] 動作。

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>我可以使用 Azure Migrate 來遷移 Active Directory 網域控制站嗎？

伺服器遷移工具是應用程式中立的，適用于大部分的應用程式。 當您使用伺服器遷移工具來遷移伺服器時，安裝在伺服器上的所有應用程式都會與其一起遷移。 不過，在某些應用程式中，伺服器遷移以外的替代遷移方法可能更適合進行遷移。  針對 Active Directory，在內部部署網站連線至 Azure 環境的混合式環境中，您可以在 Azure 中新增額外的網域控制站，並設定 Active Directory 複寫，將目錄延伸至 Azure。 如果您要遷移至 Azure 中的隔離環境，需要有自己的網域控制站 (或在沙箱環境中測試應用程式) ，您可以使用伺服器遷移工具來遷移伺服器。

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>如果我未在遷移後停止複寫，會發生什麼事？

當您停止複寫時，Azure Migrate：伺服器遷移工具會清除針對複寫所建立之訂用帳戶中的受控磁片。 如果您未在遷移後停止複寫，您將會繼續產生這些磁片的費用。 停止複寫並不會影響已遷移的電腦所連接的磁片。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>是否需要 VMware vCenter 才能遷移 VMware Vm？

若要使用 VMware 代理程式型或無代理程式遷移來 [遷移 Vmware vm](server-migrate-overview.md) ，vm 所在位置的 ESXi 主機必須由 vCenter Server 管理。 如果您沒有 vCenter Server，您可以將 VMware Vm 作為實體伺服器進行遷移，以進行遷移。 [深入了解](migrate-support-matrix-physical-migration.md)。

## <a name="can-i-upgrade-my-os-while-migrating"></a>是否可以在遷移時升級 OS？

Azure Migrate：伺服器遷移工具目前僅支援類似類似的遷移。 此工具不支援在遷移期間升級作業系統版本。 已遷移的電腦將會與來源機器具有相同的作業系統。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>我部署了兩個 (或多個) 設備，以探索 vCenter Server 中的 Vm。 不過，當我嘗試遷移 Vm 時，只會看到對應到其中一個設備的 Vm。

如果已設定多個設備，則在提供的 vCenter 帳戶中的多個 VM 之間不可以有重疊的情況。 若有這類重疊，則不支援探索。

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>如何? 知道是否已成功遷移我的 VM？

成功遷移您的 VM/伺服器之後，您可以從 [虛擬機器] 頁面來查看和管理 VM。 連線到已遷移的 VM 來進行驗證。
或者，您可以檢查作業的「作業狀態」，以檢查是否已順利完成遷移。 如果您看到任何錯誤，請加以解決，然後重試遷移作業。

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>在遷移時，是否可以將多個來源 Vm 合併到一個 VM？

Azure Migrate server 遷移功能支援的方式，就像是目前的遷移。 我們不支援在遷移過程中合併伺服器或升級作業系統。 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>無代理程式複寫如何影響 VMware 伺服器？

無代理程式複寫會產生對 VMware vCenter Server 和 VMware ESXi 主機的一些效能影響。 由於無代理程式複寫使用快照集，因此會耗用儲存體上的 IOPS，因此需要一些 IOPS 儲存體頻寬。 如果您的環境中有儲存體或 IOPs 的限制，則不建議使用無代理程式複寫。


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Migrate 總覽](migrate-services-overview.md)。