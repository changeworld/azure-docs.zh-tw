---
title: 將 Windows Server/System Center VMM 2012 R2 升級至 Windows Server 2016-Azure Site Recovery
description: 瞭解如何將 Windows Server 2012 R2 主機 & 使用 Azure Site Recovery 設定的 SCVMM 2012 R2 升級至 Windows Server 2016 & SCVMM 2016。
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b9869ae7dfbf5afd6b8d3b870a2ad4e56fd54c1a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250060"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>將 Windows Server Server/System Center 2012 R2 VMM 升級至 Windows Server/VMM 2016 

本文介紹如何將配有 Azure Site Recovery 的 Windows Server 2012 R2 主機和 SCVMM 2012 R2 升級到 Windows Server 2016 及 SCVMM 2016

Site Recovery 有助於商務持續性和災害復原 (BCDR) 策略的進行。 此服務可確保當停電時，無論是計劃性或突發性，您的 VM 工作負載皆維持可供使用。

> [!IMPORTANT]
> 要將已設定由 Azure Site Recovery 進行複寫的 Windows Server 2012 R2 主機升級時，請務必按照本文所述的步驟進行操作。 改選其他升級路徑均可能發生不受支援的狀態，也可能導致複寫中斷或容錯移轉的執行能力出問題。


在本文中，可了解到如何在您的環境中升級以下設定：

> [!div class="checklist"]
> * **不受 SCVMM 管理的 Windows Server 2012 R2 主機** 
> * **受獨立 SCVMM 2012 R2 伺服器管理的 Windows Server 2012 R2 主機** 
> * **受高可用性的 SCVMM 2012 R2 伺服器管理的 Windows Server 2012 R2 主機**


## <a name="prerequisites--factors-to-consider"></a>必要條件和考量因素

升級之前，請注意下列事項：

- 如果您擁有不受 SCVMM 管理的 Windows Server 2012 R2 主機，且其有獨立的環境設定，如果您嘗試執行升級，將會中斷複寫。
- 如果您在當初安裝 SCVMM 2012 R2 時，選取了 [not store my Keys in Active Directory under Distributed Key Management]\(不要將我的金鑰儲存在分散式金鑰管理下的 Active Directory\)**，則會無法順利完成升級。

- 如果您使用的是 System Center 2012 R2 VMM， 

    - 檢查 vmm 上的資料庫資訊： **vmm 主控台**  ->  **設定**  ->  **一般**  ->  **資料庫連接**
    - 檢查用於 System Center Virtual Machine Manager 代理程式服務的服務帳戶
    - 請確定您有 VMM 資料庫的備份。
    - 請記下與 SCVMM 伺服器相關的資料庫名稱。 這可以藉由流覽至**VMM 主控台**  ->  **設定**  ->  **一般**  ->  **資料庫**連線來完成
    - 請記下 2012R2 主要 VMM 伺服器和復原 VMM 伺服器的 VMM ID。 可以從「HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup」此登錄找到 VMM ID。
    - 請確認您要新增至叢集的新 SCVMM 的名稱與之前的相同。 

- 如果要在由 SCVMM 管理的兩個站台之間進行雙邊複寫，請確保在升級主要一方之前，先升級復原站台。
  > [!WARNING]
  > 升級 SCVMM 2012 R2 時，請在 [分散式金鑰管理] 下，選取 [store encryption keys in Active Directory] \(將加密金鑰儲存在 Active Directory 中\)****。 請仔細選擇服務帳戶和分散式金鑰管理的設定。 根據您的選擇，升級後可能無法使用加密資料 (例如範本中的密碼)，且可能會影響 Azure Site Recovery 的複寫

> [!IMPORTANT]
> 請參閱[先決條件](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)的詳細 SCVMM 文件

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>不受 SCVMM 管理的 Windows Server 2012 R2 主機 
下列步驟清單適用於依此[教學課程](./hyper-v-prepare-on-premises-tutorial.md)從 [Hyper-V 主機到 Azure](./hyper-v-azure-architecture.md) 執行的使用者設定

> [!WARNING]
> 如必要條件中所述，這些步驟僅適用於叢集環境的情形，而不適用於獨立的 Hyper-V 主機設定。

1. 請依照下列步驟來進行[叢集升級](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)， 以執行叢集的輪流升級程序。
2. 對於叢集中引入的每個新 Windows Server 2016 主機，請按照 [此處] 提到的步驟，從 Azure Site Recovery 中移除 Windows Server 2012 R2 主機的參考。 這應是您選擇從叢集中清空和收回的主機。
3. 為所有虛擬機器執行 *Update-VMVersion* 命令之後，升級即完成。 
4. 請用[此處](./hyper-v-azure-tutorial.md#set-up-the-source-environment)所述的步驟，將新的 Windows Server 2016 主機註冊到 Azure Site Recovery。 請注意，Hyper-V 站台已處於作用中狀態，您只需要在叢集中註冊新主機即可。 
5.  前往 Azure 入口網站確認復原服務中的複寫健康情況的狀態

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>升級受獨立 SCVMM 2012 R2 伺服器管理的 Windows Server 2012 R2 主機
升級 Windows Sever 2012 R2 主機之前，需先將 SCVMM 2012 R2 升級至 SCVMM 2016。 請按照下列步驟進行：

**將獨立的 SCVMM 2012 R2 升級至 SCVMM 2016**

1.  瀏覽至 [控制台] -> [程式] -> [程式和功能] -> [Microsoft Azure Site Recovery]，然後按一下 [解除安裝]，即可將 ASR 提供者解除安裝。
2. [保留 SVMM 資料庫，並升級作業系統](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. 在 [新增/移除程式]**** 中，選取 [VMM]**** > [解除安裝]****。 b. 選取 [移除功能]****，然後選取 [VMM 管理伺服器] 和 [VMM 主控台]****。 c. 在 [資料庫選項]**** 中，選取 [保留資料庫]****。 d. 檢閱摘要，並按一下 [解除安裝]****。

4. [安裝 VMM 2016](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. **在 [** 網狀架構] 索引標籤上啟動 SCVMM 並檢查每個主機的狀態。按一下 **[** 重新整理] 以取得最新狀態。 您應該會看到狀態為 [需要注意]。 
17. 在 SCVMM 上安裝最新的 [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra)。
16. 在叢集的每個主機上安裝最新的 [Microsoft Azure Recovery Service (MARS) 代理程式](https://aka.ms/latestmarsagent)。 重新整理以確保 SCVMM 可以順利查詢主機。

**將 Windows Server 2012 R2 主機升級為 Windows Server 2016**

1. 請依照[此處](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)所述的步驟，執行下列叢集輪流升級程序。 
2. 將新主機新增至叢集之後，請在 SCVMM 主控台重新整理主機，以在更新後的主機上安裝 VMM 代理程式。
3. 執行 [更新-VMVersion]**，以更新虛擬機器的 VM 版本。 
4.  前往 Azure 入口網站，確認復原服務保存庫中虛擬機器複寫健康情況的狀態。 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>升級受高可用性的 SCVMM 2012 R2 伺服器管理的 Windows Server 2012 R2 主機
升級 Windows Sever 2012 R2 主機之前，需先將 SCVMM 2012 R2 升級至 SCVMM 2016。 升級配有 Azure Site Recovery 的 SCVMM 2012 R2 伺服器時，可支援以下升級模式：不含其他 VMM 伺服器的混合模式，以及包含其他 VMM 伺服器的混合模式。

**將 SCVMM 2012 R2 升級至 SCVMM 2016**

1.  瀏覽至 [控制台] -> [程式] -> [程式和功能] -> [Microsoft Azure Site Recovery]，然後按一下 [解除安裝]，即可將 ASR 提供者解除安裝。
2. 根據您希望執行的升級模式，依照[此處](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server)所述的步驟進行操作。
3. 啟動 SCVMM 主控台， **並在 [** 網狀架構] 索引標籤下檢查每個主機的狀態。按一下 **[** 重新整理] 以取得最新狀態。 您應該會看到狀態為 [需要注意]。
4. 在 SCVMM 上安裝最新的 [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra)。
5. 在叢集的每個主機上升級最新的 [Microsoft Azure Recovery Service (MARS) 代理程式](https://aka.ms/latestmarsagent)。 重新整理以確保 SCVMM 可以順利查詢主機。


**將 Windows Server 2012 R2 主機升級為 Windows Server 2016**

1. 請依照[此處](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)所述的步驟，執行下列叢集輪流升級程序。
2. 將新主機新增至叢集之後，請在 SCVMM 主控台重新整理主機，以在更新後的主機上安裝 VMM 代理程式。
3. 執行 [更新-VMVersion]**，以更新虛擬機器的 VM 版本。 
4.  前往 Azure 入口網站，確認復原服務保存庫中虛擬機器複寫健康情況的狀態。 

## <a name="next-steps"></a>後續步驟
執行主機升級後，您可以執行[測試容錯移轉](tutorial-dr-drill-azure.md)，以測試複寫和災害恢復狀態的健康情況。

