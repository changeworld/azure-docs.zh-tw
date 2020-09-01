---
title: 將 Windows 系統狀態備份至 Azure
description: 瞭解如何將 Windows Server 電腦的系統狀態備份至 Azure。
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 14ca17284d48355260cdeda6ef3b20b4794a0c4f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181624"
---
# <a name="back-up-windows-system-state-to-azure"></a>將 Windows 系統狀態備份至 Azure

本文說明如何將 Windows Server 的系統狀態備份至 Azure。 它的目的是要讓您逐步瞭解基本概念。

如果您想要深入了解 Azure 備份，請閱讀此 [概觀](backup-overview.md)。

如果您沒有 Azure 訂用帳戶，請建立 [免費帳戶](https://azure.microsoft.com/free/) ，以便存取任何 Azure 服務。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>設定保存庫的儲存體備援

當您建立復原服務保存庫時，務必以您想要的方式設定儲存體備援。

1. 從 [復原 **服務保存庫** ] 窗格中，選取新的保存庫。

    ![從復原服務保存庫清單中選取新的保存庫](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    當您選取保存庫時，[復原 **服務保存庫** ] 窗格會縮小，且 [設定] 窗格 (， *其中包含保存庫在頂端*) 的名稱，而 [保存庫詳細資料] 窗格會開啟。

    ![檢視新保存庫的儲存體組態](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 在新保存庫的 [設定] 窗格中，使用垂直投影片向下滾動至 [管理] 區段，然後選取 [ **備份基礎結構**]。
    [備份基礎結構] 窗格隨即開啟。
3. 在 [備份基礎結構] 窗格中，選取 [ **備份** 設定] 以開啟 [ **備份** 設定] 窗格。

    ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 為保存庫選擇適當的儲存體複寫選項。

    ![儲存體設定選項](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    根據預設，保存庫具有異地備援儲存體。 如果您使用 Azure 做為主要的備份儲存體端點，請繼續使用 [異地備援]****。 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]****，以減少 Azure 儲存體成本。 在此[儲存體備援概觀](../storage/common/storage-redundancy.md)中，深入了解[異地備援](../storage/common/storage-redundancy.md)和[本地備援](../storage/common/storage-redundancy.md)儲存體選項。

您已建立了保存庫，接著請設定它來備份 Windows 系統狀態。

## <a name="configure-the-vault"></a>設定保存庫

1. 在 [復原服務保存庫] 窗格上 (您剛才建立的保存庫) 的 [消費者入門] 區段中，選取 [ **備份**]，然後在 [備份] 窗格的 [ **消費者入門** ] 中選取 [ **備份目標**]。

    ![開啟備份設定](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    [ **備份目標** ] 窗格隨即開啟。

    ![開啟備份目標窗格](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. 從 [您的工作負載在何處執行?]**** 下拉式功能表中，選取 [內部部署]****。

    因為您的 Windows Server 或 Windows 電腦是不在 Azure 中的實體機器，所以您可以選擇 [ **內部部署** ]。

3. 從 [ **您要備份什麼？** ] 功能表，選取 [ **系統狀態**]，然後選取 **[確定]**。

    ![設定檔案和資料夾](./media/backup-azure-system-state/backup-goal-system-state.png)

    按一下 [確定] 之後，[ **備份目標**] 旁邊會出現核取記號，[ **準備基礎結構** ] 窗格隨即開啟。

    ![已設定備份目標，接下來是準備基礎結構](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. 在 [ **準備基礎結構** ] 窗格中，選取 [ **下載 Windows Server 或 windows 用戶端的代理程式**]。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    如果您使用的是 Windows Server，請選擇下載適用于 Windows Server 的代理程式。 快顯功能表會提示您執行或儲存 MARSAgentInstaller.exe。

    ![MARSAgentInstaller dialog](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 在 [下載] 快顯功能表中，選取 [ **儲存**]。

    根據預設，**MARSagentinstaller.exe** 檔案會儲存至 [下載] 資料夾。 當安裝程式完成時，您會看到快顯視窗，詢問您是否要執行安裝程式，或開啟資料夾。

    ![MARS 安裝程式已完成](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    您還不需要安裝代理程式。 下載保存庫認證之後，您可以安裝代理程式。

6. 在 [ **準備基礎結構** ] 窗格中，選取 [ **下載**]。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    保存庫認證會下載到您的 [ **下載** ] 資料夾。 保存庫認證下載完成之後，您會看到快顯視窗，詢問您是否要開啟或儲存認證。 選取 \[儲存\]。 如果您不小心選取 [ **開啟**]，請讓嘗試開啟保存庫認證的對話方塊失敗。 您將無法開啟保存庫認證。 繼續進行下一個步驟。 保存庫認證位於 [ **下載** ] 資料夾中。

    ![保存庫認證下載完成](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > 保存庫認證只能儲存到您想要在其上使用代理程式之 Windows 伺服器的本機位置。
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

> [!NOTE]
> 無法透過 Azure 入口網站啟用備份。 使用 Microsoft Azure 復原服務代理程式來備份 Windows Server 系統狀態。
>

1. 在 [下載] 資料夾 (或其他儲存位置) 中找到 **MARSagentinstaller.exe** 並對其按兩下。

    安裝程式在擷取、安裝和註冊復原服務代理程式時會提供一系列訊息。

    ![執行復原服務代理安裝程式的認證](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. 完成 Microsoft Azure 復原服務代理程式安裝精靈。 若要完成精靈，您需要︰

   * 選擇安裝和快取資料夾的位置。
   * 如果您使用 Proxy 伺服器來連線到網際網路，請提供您的 Proxy 伺服器資訊。
   * 如果您使用已驗證的 Proxy，請提供您的使用者名稱和密碼詳細資料。
   * 提供下載的保存庫認證
   * 將加密複雜密碼存放在安全的位置。

     > [!NOTE]
     > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。 將檔案存放在安全的位置。 需要還原備份。
     >
     >

現已安裝代理程式，且已向保存庫註冊您的電腦。 您已準備好可以設定及排程備份。

## <a name="back-up-windows-server-system-state"></a>備份 Windows Server 系統狀態

初始備份包括兩項工作：

* 排程備份
* 第一次備份系統狀態

若要完成初始備份，請使用 Microsoft Azure 復原服務代理程式。

> [!NOTE]
> 您可以透過 Windows Server 2016，在 Windows Server 2008 R2 上備份系統狀態。 用戶端 Sku 不支援系統狀態備份。 系統狀態不會顯示為 Windows 用戶端或 Windows Server 2008 SP2 電腦的選項。
>
>

### <a name="to-schedule-the-backup-job"></a>排程備份作業

1. 開啟 Microsoft Azure 復原服務代理程式。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。

    ![啟動 Azure 復原服務代理程式](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. 在 [復原服務代理程式] 中，選取 [ **排程備份**]。

    ![Windows Server 備份排程](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 在 [排程備份] 的 [開始使用 **] 頁面上** ，選取 **[下一步**]。

4. 在 [ **選取要備份的專案** ] 頁面上，選取 [ **加入專案**]。

5. 選取 [ **系統狀態** ]，然後選取 **[確定]**。

6. 選取 [下一步]  。

7. 在後續頁面中針對系統狀態備份選取所需的備份頻率和保留原則。

8. 在 [確認] 頁面上，檢查資訊，然後選取 **[完成]**。

9. 當精靈建立好備份排程時，請選取 [關閉] 。

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>第一次備份 Windows Server 系統狀態

1. 確定 Windows Server 沒有需要重新啟動的擱置中更新。

2. 在 [復原服務代理程式] 中，選取 [ **立即備份** ] 以完成網路的初始植入。

    ![立即備份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 在 [**選取備份專案**] 畫面上選取 [**系統狀態**]，然後選取 [**下一步]**。

4. 在 [確認] 頁面上，檢閱立即備份精靈將用於備份電腦的設定。 然後選取 [備份]。

5. 選取 [關閉]  即可關閉精靈。 如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。
    > [!NOTE]
    > MARS 代理程式會在 `SFC /verifyonly` 每個系統狀態備份之前觸發為前置檢查的一部分。 這是為了確保備份為系統狀態一部分的檔案具有對應至 Windows 版本的正確版本。 在 [本文](/windows-server/administration/windows-commands/sfc)中深入瞭解系統檔案檢查工具 (SFC) 。
    >

完成初始備份之後，備份主控台中會顯示 [作業已完成] **** 狀態。

  ![IR 已完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>有問題嗎？

如果您有任何問題，請 [傳送意見](https://feedback.azure.com/forums/258995-azure-backup)反應給我們。

## <a name="next-steps"></a>後續步驟

* 詳細了解如何 [備份 Windows 電腦](backup-windows-with-mars-agent.md)。
* 現在您已備份好 Windows Server 系統狀態，接下來您可以[管理您的保存庫和伺服器](backup-azure-manage-windows-server.md)。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
