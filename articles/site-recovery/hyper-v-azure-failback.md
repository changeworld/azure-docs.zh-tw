---
title: 通過 Azure 網站恢復從 Azure 故障回超級 VM
description: 如何通過 Azure 網站恢復將 Hyper-VM 故障台故障回 Azure 的本地網站。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281778"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>執行 Hyper-V VM 的容錯回復

本文介紹如何在 Hyper-V VM 容錯移轉後從本地網站容錯移轉到 Azure 後，使用[Azure 網站恢復來](site-recovery-overview.md)故障返回 Azure VM。

- 通過運行從 Azure 到本地網站的計畫容錯移轉，從 Azure 故障回退 Hyper-VM。 如果容錯移轉方向從 Azure 到本地，則視為故障倒轉。
- 由於 Azure 是一個高可用性環境，並且 VM 始終可用，因此 Azure 的故障倒退是計畫的活動。 您可以計畫一小次停機時間，以便工作負載可以再次開始在本地運行。 
- 計畫故障回退將關閉 Azure 中的 VM，並下載最新的更改。 預計不會丟失任何資料。

## <a name="before-you-start"></a>開始之前

1. [查看可以使用的故障恢復類型](failover-failback-overview.md#hyper-v-reprotectionfailback)- 原始位置恢復和備用位置恢復。
2. 確保 Azure VM 正在使用存儲帳戶而不是託管磁片。 不支援使用託管磁碟複製的超 VM 故障倒回。
3. 檢查本地 Hyper-V 主機（或系統中心 VMM 伺服器，如果您正在使用網站恢復）是否正在運行並連接到 Azure。 
4. 確保 VM 的容錯移轉和提交已完成。 無需為 Azure 中超 VM 的故障恢復設置任何特定的網站恢復元件。
5. 完成資料同步和啟動本地 VM 所需的時間將取決於許多因素。 要加快資料下載速度，可以配置 Microsoft 恢復服務代理以使用更多執行緒並行化下載。 [深入了解](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)。


## <a name="fail-back-to-the-original-location"></a>容錯回復至原始位置

要將 Azure 中的 Hyper-V VM 故障台故障回原始本地 VM，應運行從 Azure 到本地網站的計畫容錯移轉，如下所示：

1. 在保存庫>**複製的專案**中，選擇 VM。 按右鍵 VM >**計畫容錯移轉**。 如果恢復計畫失敗，請選擇計畫名稱，然後按一下**容錯移轉** > **計畫容錯移轉**。
2. 在 **"確認計畫容錯移轉**"中，選擇源和目標位置。 記下容錯移轉方向。 如果主容錯移轉按預期工作，並且所有虛擬機器都位於輔助位置，則僅供參考。
3. 在 **"資料同步**"中，選擇一個選項：
    - **在容錯移轉之前同步資料（僅同步增量更改）—** 此選項可在 VM 同步時最大限度地減少停機時間，而不會關閉它們。
        - **階段 1：** 拍攝 Azure VM 的快照並將其複製到本地 Hyper-V 主機。 機器會繼續在 Azure 中執行。
        - **第 2 階段**：關閉 Azure VM，以便那裡不發生新的更改。 最後一組增量更改將傳輸到本機伺服器，並啟動本地 VM。
    - **僅在容錯移轉期間同步資料（完全下載）**- 此選項更快，因為我們假定大多數磁片已更改，並且不想花時間計算校驗和。 這個選項不會執行任何總和檢查碼計算。
        - 它會下載磁碟。 
        - 如果您運行 Azure 一段時間（一個月或更長時間），或者已刪除本地 VM，我們建議您使用此選項。

4. 僅對於 VMM，如果為雲啟用了資料加密，請在**加密金鑰**中選擇在 VMM 伺服器上安裝提供程式期間啟用資料加密時頒發的證書。
5. 起始容錯移轉。 您可以在 [工作] **** 索引標籤上追蹤容錯移轉進度。
6. 如果選擇在容錯移轉之前同步資料的選項，則在初始資料同步完成並準備好關閉 Azure 中的虛擬機器後，請按一下**作業**>作業名稱>**完成容錯移轉**。 這會執行以下動作：
    - 關閉 Azure 電腦。
    - 將最新更改傳輸到本地 VM。
    - 啟動本地 VM。
7. 現在，您可以登錄到本地 VM 電腦，以檢查其是否可以按預期使用。
8. 虛擬機器目前處於認可擱置中的狀態。 請按一下 **[認可]** 認可容錯移轉。
9. 要完成故障倒轉，請按一下 **"反向複製**"以再次將本地 VM 複製到 Azure。



## <a name="fail-back-to-an-alternate-location"></a>容錯回復至其他位置 

故障返回備用位置，如下所示：

1. 如果要設置新硬體，請在電腦上安裝[受支援的 Windows](hyper-v-azure-support-matrix.md#replicated-vms)版本 和 Hyper-V 角色。
2. 使用您在原始伺服器上所擁有的相同名稱來建立虛擬網路交換器。
3. 在**受保護專案** > **保護組保護組** > \<保護組名稱> \<->虛擬機器名稱>中，選擇要故障恢復的 VM，然後選擇 **"計畫容錯移轉**"。
4. 在 **"確認計畫容錯移轉**"中，選擇 **"如果本地虛擬機器不存在"，則選擇"創建本地虛擬機器**"。
5. 在**主機名稱**中，選擇要將 VM 放在其上的新的 Hyper-V 主機伺服器。
6. 在 **"資料同步**"中，我們建議您選擇在容錯移轉之前同步資料的選項。 這樣可以最大限度地減少 VM 同步時的停機時間，而不會關閉它們。 它會執行下列工作：
    - **階段 1：** 拍攝 Azure VM 的快照並將其複製到本地 Hyper-V 主機。 機器會繼續在 Azure 中執行。
    - **第 2 階段**：關閉 Azure VM，以便那裡不發生新的更改。 最後一組變更會傳送到內部部署伺服器，並啟動內部部署虛擬機器。
    
7. 按一下勾號以開始容錯移轉 (容錯回復)。
8. 初始同步完成後，您已準備好關閉 Azure VM，請按一下**作業** > \<計畫容錯移轉作業> > **完成容錯移轉**。 這將關閉 Azure 電腦，將最新更改傳輸到本地 VM 並啟動它。
9. 您可以登錄到本地 VM 以驗證一切是否按預期工作。
10. 按一下 **"提交"** 以完成容錯移轉。 提交將刪除 Azure VM 及其磁片，並準備再次保護本地 VM。
10. 按一下 **"反向複製**"以開始將本地 VM 複製到 Azure。 只有在 Azure 中關閉 VM 後增量更改才會複製。

    > [!NOTE]
    > 如果在資料同步期間取消故障恢復作業，則本地 VM 將處於損壞狀態。 這是因為資料同步將 Azure VM 磁片的最新資料複製到本地資料磁片，並且在同步完成之前，磁片資料可能未處於一致狀態。 如果本地 VM 在取消資料同步後啟動，則可能無法啟動。 在這種情況下，重新運行容錯移轉以完成資料同步。


## <a name="next-steps"></a>後續步驟
本地 VM 複製到 Azure 後，可以根據需要[運行另一個容錯移轉](site-recovery-failover.md)到 Azure。
