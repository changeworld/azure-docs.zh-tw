---
title: 在 Azure Site Recovery 中設定 Azure Vm 的複寫
description: 瞭解如何使用 Site Recovery 設定 Azure Vm 的其他區域複寫。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: fe5feed4bb6f9b84a3f161692310922f7a6d2f00
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424787"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>將 Azure VM 複寫至另一個 Azure 區域


本文說明如何將 Azure VM 從一個 Azure 區域複寫到另一個區域。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

本文假設您已備妥 Site Recovery 部署，如 [azure 至 azure](azure-to-azure-tutorial-enable-replication.md)的嚴重損壞修復教學課程中所述。

必要條件應該已準備就緒，您應該已建立復原服務保存庫。


## <a name="enable-replication"></a>啟用複寫

啟用複寫。 此程序假設主要 Azure 區域為東亞，而次要區域為東南亞。

1. 在保存庫中，按一下 [+複寫]****。
2. 請注意以下欄位：
   - **來源**：VM 的起點，在此案例中為 **Azure**。
   - **來源位置**：您要用來保護 Vm 的 Azure 區域。 在此圖例中，來源位置是 [東亞]
   - **部署模型**：來源機器的 Azure 部署模型。
   - **來源訂**用帳戶：您的來源 vm 所屬的訂用帳戶。 這可以是您的復原服務保存庫所在的相同 Azure Active Directory 租用戶內的任何訂用帳戶。
   - **資源群組**：來源虛擬機器所屬的資源群組。 下一個步驟會列出所選取資源群組下的所有 VM，以供保護。

     ![醒目顯示設定複寫所需欄位的螢幕擷取畫面。](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. 在 [虛擬機器] > [選取虛擬機器]**** 中，按一下並選取您要複寫的每部 VM。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。
    ![醒目顯示您選取虛擬機器之位置的螢幕擷取畫面。](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. 在 [設定]**** 中，您可以選擇性地設定目標網站設定：

   - **目標位置**：將複寫來源虛擬機器資料的位置。 端視您選取的機器位置而定，Site Recovery 將提供適當目標區域的清單。 建議您讓目標位置與復原服務保存庫位置保持相同。
   - **目標訂用帳戶**：用於災害復原的目標訂用帳戶。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。
   - **目標資源群組**：所有已複寫虛擬機器所屬的資源群組。
       - 依預設 Site Recovery 會在目的地區域中建立新的資源群組，其名稱中會有 "asr" 尾碼。
       - 如果 Site Recovery 所建立的資源群組已經存在，則會重複使用。
       - 您可以自訂資源群組設定。
       - 目標資源群組的位置可以是任何 Azure 區域，但是裝載來源 Vm 的區域除外。
   - **目標虛擬網路**：根據預設，Site Recovery 會在目的地區域中建立新的虛擬網路，其名稱中會有 "asr" 尾碼。 這會對應至您的來源網路，並用來進行任何未來的保護。 [深入了解](./azure-to-azure-network-mapping.md)網路對應。
   - **目標儲存體帳戶 (來源 vm 未使用受控磁片) **：根據預設，Site Recovery 會建立新的目標儲存體帳戶，模擬您的來源 vm 儲存體設定。 如果儲存體帳戶已經存在，就會重複使用。
   - **複本受控磁片 (來源 vm 使用受控磁片) **： Site Recovery 會在目的地區域中建立新的複本受控磁片，以使用與來源 vm 的受控磁片相同的儲存體類型 (標準或 premium) 來鏡像來源 vm 的受控磁片。
   - **快取儲存體帳戶**：Site Recovery 需要在來源區域中有額外的儲存體帳戶 (稱為快取儲存體)。 在來源 VM 上發生的所有變更都會受到追蹤，並傳送到快取儲存體帳戶，然後複寫到目標位置。 此儲存體帳戶應該是標準的。
   - **目標可用性設定組**：根據預設，Site Recovery 會在目的地區域中建立具有 "asr" 尾碼的新可用性設定組，該名稱中的 vm 屬於來源區域中的可用性設定組。 如果 Site Recovery 所建立的可用性設定組已經存在，則會重複使用。
   - **目標可用性區域**：根據預設，如果目標區域支援可用性區域，則 Site Recovery 會在目標區域中指派與來源區域相同的區域編號。

     如果目標區域不支援可用性區域，則目標 VM 會預設為單一執行個體。 如有需要，您可以按一下 [自訂]，將這類 VM 設定為目標區域中可用性設定組的一部分。

     >[!NOTE]
     >啟用複寫之後，便無法變更可用性類型 - 單一執行個體、可用性設定組或可用性區域。 您需要停用後再啟用複寫，以變更可用性類型。
     >

   - **複寫原則**：它會定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Azure Site Recovery 會建立新的複寫原則，其預設設定為「24小時」的復原點保留期，而「4小時」表示應用程式一致的快照頻率。

     ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>為新增的磁片啟用複寫

如果您將磁片新增至已啟用複寫的 Azure VM，則會發生下列情況：
-   VM 的複寫健康情況會顯示一則警告，而附注會告知您有一或多個磁片可供保護。
-   如果您為新增的磁片啟用保護，則在磁片的初始複寫之後，會出現警告。
-   如果您選擇不啟用磁片的複寫，您可以選擇關閉警告。


    ![新增的磁片](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

若要為新增的磁片啟用複寫，請執行下列動作：

1.  在保存庫 > 複寫的 **專案**中，按一下您要新增磁片的 VM。
2.  按一下 [ **磁片**]，然後選取您要啟用複寫的資料磁片 (這些磁片具有 [ **未受保護** ] 狀態) 。
3.  在 [ **磁片詳細資料**] 中，按一下 [ **啟用**複寫]。

    ![為新增的磁片啟用複寫](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

啟用複寫作業執行且初始複寫完成之後，就會移除磁片問題的複寫健康情況警告。



## <a name="customize-target-resources"></a>自訂目標資源

您可以修改 Site Recovery 所使用的預設目標設定。

1. 按一下「目標訂用帳戶」旁的 [自訂]**** 來修改預設目標訂用帳戶。 在同一個 Azure Active Directory (AAD) 租用戶中，從所有可用的訂用帳戶清單中選取訂用帳戶。

2. 按一下 [自訂:]**** 以修改預設設定：
    - 在 [目標資源群組]**** 中，從訂用帳戶目標位置上的所有資源群組清單中選取資源群組。
    - 在 [目標虛擬網路]**** 中，從目標位置上的所有虛擬網路清單中選取網路。
    - 在 [可用性設定組]**** 中，如果 VM 是來源區域中可用性設定組的一部分，即可將可用性設定組新增至其中。
    - 在 [目標儲存體帳戶]**** 中，選取您要使用的帳戶。

        ![顯示如何自訂目標訂用帳戶設定的螢幕擷取畫面。](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. 按一下 [自訂:]**** 以修改複寫設定。
4. 在 [ **多 VM 一致性**] 中，選取您要一起複寫的 vm。
    - 在容錯移轉時，複寫群組中的所有機器都會共用當機時保持一致和應用程式一致復原點。
    - 啟用多 VM 一致性可能會影響工作負載效能 (因為它會耗用大量 CPU) 。 只有在機器執行相同的工作負載，且您需要在多部電腦之間保持一致性時，才應啟用此功能。
    - 例如，如果應用程式有2個 SQL Server 虛擬機器和兩部網頁伺服器，則您應該只將 SQL Server Vm 新增至複寫群組。
    - 您可以在複寫群組中選擇最多16個 Vm。
    - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。
    - 確定沒有任何防火牆設備阻礙 Vm 在埠20004之間的內部通訊。
    - 如果您想要讓 Linux Vm 成為複寫群組的一部分，請確定已根據特定 Linux 版本的指引手動開啟埠20004上的輸出流量。
![顯示多 VM 一致性設定的螢幕擷取畫面。](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. 按一下 [**建立目標資源**  >  **啟用**複寫]。
6. 啟用 VM 以進行複寫之後，您就可以在 [複寫的項目]**** 下方檢查 VM 健康情況的狀態。

>[!NOTE]
>
> - 在初始複寫期間，狀態可能需要一些時間才會重新整理，期間不會有任何進展。 按一下 [重新整理]**** 按鈕來取得最新狀態。
> - 如果過去60分鐘內未產生復原點，虛擬機器的複寫健康狀態將會變成「重大」。

## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
