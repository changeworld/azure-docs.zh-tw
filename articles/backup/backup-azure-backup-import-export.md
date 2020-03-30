---
title: 使用 Azure 導入/匯出服務進行種子離線備份
description: 瞭解如何使用 Azure 備份將資料從網路外發送，使用 Azure 導入/匯出服務。 本文介紹使用 Azure 導入/匯出服務的初始備份資料的離線種子設定。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206753"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 備份中的離線備份工作流

Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料，且需要較多網路頻寬，相較之下，後續備份只會傳輸差異/增量部分。 透過離線植入程序，Azure 備份可以使用磁碟將離線備份資料上傳至 Azure。

Azure 備份離線種子設定過程與[Azure 導入/匯出服務](../storage/common/storage-import-export-service.md)緊密集成。 您可以使用此服務使用磁片將初始備份資料傳輸到 Azure。 如果具有需要通過高延遲和低頻寬網路傳輸的初始備份資料 TB （TB），則可以使用離線種子工作流將初始備份副本（一個或多個硬碟磁碟機）傳送到 Azure 資料中心。 下圖提供工作流程中的步驟概觀。

  ![離線導入工作流流程概述](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

離線備份過程涉及以下步驟：

1. 將備份資料寫入到暫存位置，而不是透過網路傳送備份資料。
1. 使用*AzureOfflineBackupDiskPrep*實用程式將暫存位置中的資料寫入一個或多個 SATA 磁片。
1. 作為準備工作的一部分 *，AzureOfflineBackupDiskPrep*實用程式將創建 Azure 導入作業。 將 SATA 磁碟機傳送至最接近的 Azure 資料中心，並參考匯入作業以連線活動。
1. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
1. Azure 備份會將儲存體帳戶中的資料複製到「復原服務」保存庫，而且會排定增量備份。

## <a name="supported-configurations"></a>支援的設定

以下 Azure 備份功能或工作負載支援對以下人員使用離線備份：

> [!div class="checklist"]
>
> * 使用 Microsoft Azure 恢復服務 （MARS） 代理（也稱為 Azure 備份代理）備份檔案和資料夾。
> * 使用系統中心資料保護管理器 （DPM） 備份所有工作負載和檔。
> * 使用 Microsoft Azure 備份伺服器備份所有工作負荷和檔。
 
   > [!NOTE]
   > 使用 Azure 備份代理完成的系統狀態備份不支援離線備份。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisites

  > [!NOTE]
  > 以下先決條件和工作流僅適用于使用[最新的 Azure 恢復服務代理](https://aka.ms/azurebackup_agent)離線備份檔案和資料夾。 要使用系統中心 DPM 或 Azure 備份伺服器對工作負載執行離線備份，請參閱[DPM 和 Azure 備份伺服器的離線備份工作流](backup-azure-backup-server-import-export-.md)。

在啟動離線備份工作流之前，完成以下先決條件：

* 創建[恢復服務保存庫](backup-azure-recovery-services-vault-overview.md)。 要創建保存庫，請按照[創建恢復服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步驟操作。
* 確保僅在 Windows 伺服器或 Windows 用戶端上安裝[最新版本的 Azure 備份代理](https://aka.ms/azurebackup_agent)（如適用），並且電腦已註冊到恢復服務保存庫。
* 運行 Azure 備份代理的電腦需要 Azure PowerShell 3.7.0。 下載並[安裝 Azure PowerShell 的 3.7.0 版本](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* 在運行 Azure 備份代理的電腦上，請確保安裝了 Microsoft 邊緣或 Internet 資源管理器 11 並啟用了 JavaScript。
* 在與恢復服務保存庫相同的訂閱中創建 Azure 存儲帳戶。
* 確定您有建立 Azure Active Directory 應用程式[所需的權限](../active-directory/develop/howto-create-service-principal-portal.md)。 離線備份工作流在與 Azure 存儲帳戶關聯的訂閱中創建 Azure 活動目錄應用程式。 應用程式的目標是向 Azure 備份提供安全和範圍的 Azure 導入/匯出服務存取權限，這是離線備份工作流所必需的。
* 使用包含 Azure 存儲帳戶的訂閱註冊*Microsoft.ImportExport*資來源提供者。 若要註冊資源提供者：
    1. 在主功能表上，選擇 **"訂閱**"。
    1. 如果您訂閱了多個訂閱，請選擇計畫用於離線備份的訂閱。 如果您只使用一個訂用帳戶，則您的訂用帳戶隨即出現。
    1. 在訂閱功能表上，選擇 **"資來源提供者**"以查看提供程式的清單。
    1. 在提供程式清單中，向下滾動到*Microsoft.ImportExport*。 如果**狀態****未註冊**，請選擇 **"註冊**"。

        ![註冊資源提供者](./media/backup-azure-backup-import-export/registerimportexport.png)

* 已建立具有足夠磁碟空間來存放初始複本的內部或外部暫存位置 (可能是網路共用或電腦上任何額外的磁碟機)。 例如，如果要備份 500 GB 檔案伺服器，請確保暫存區域至少為 500 GB。 (由於壓縮的關係，實際使用量會較少)。
* 將磁片發送到 Azure 時，請僅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 內部硬碟磁碟機。 您可以使用高達 10 TB 的硬碟。 檢查 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-requirements.md#supported-hardware)以取得服務所支援的最新磁碟機組合。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」**)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 確保在複製電腦上啟用了 BitLocker。

## <a name="workflow"></a>工作流程

本節介紹離線備份工作流，以便資料可以傳遞到 Azure 資料中心並上載到 Azure 存儲。 如果對導入服務或該過程的任何方面有疑問，請參閱[Azure 導入/匯出服務概述文檔](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>起始離線備份

1. 在恢復服務代理上安排備份時，您將看到此頁面。

    ![導入頁面](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. **選擇使用我自己的磁片傳輸**的選項。

    > [!NOTE]
    > 使用 Azure 資料框選項離線傳輸初始備份資料。 此選項可節省採購自己的 Azure 相容磁片所需的工作量。 它提供 Microsoft 專有、安全和防篡改的 Azure 資料盒設備，恢復服務代理可以直接將備份資料寫入這些設備。

1. 選擇 **"下一步**"，然後仔細填寫框。

    ![輸入磁片詳細資訊](./media/backup-azure-backup-import-export/your-disk-details.png)

   您填寫的框包括：

    * **預備位置**：初始備份所寫入的暫時儲存體位置。 暫存位置可能位於網路共用或本地電腦上。 如果複製電腦和源電腦不同，請指定暫存位置的完整網路路徑。
    * **Azure 資源管理器存儲帳戶**：任何 Azure 訂閱中的資源管理器類型存儲帳戶（通用 v1 或通用 v2）的名稱。
    * **Azure 存儲容器**：在將備份資料複製到恢復服務保存庫之前，Azure 存儲帳戶中的目標存儲 Blob 的名稱。
    * **Azure 訂閱 ID**：創建 Azure 存儲帳戶的 Azure 訂閱的 ID。
    * **Azure 導入作業名稱**：Azure 導入/匯出服務和 Azure 備份跟蹤磁片上發送到 Azure 的資料傳輸的唯一名稱。
  
   填寫完框後，選擇 **"下一步**"。 保存**暫存位置**和**Azure 導入作業名稱**資訊。 準備磁片是必需的。

1. 出現提示後，請登錄到 Azure 訂閱。 您必須登錄，以便 Azure 備份可以創建 Azure 活動目錄應用程式。 輸入訪問 Azure 導入/匯出服務所需的許可權。

    ![Azure 訂閱登錄頁](./media/backup-azure-backup-import-export/azure-login.png)

1. 完成工作流。 在 Azure 備份代理主控台上，選擇 **"立即備份**"。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

1. 在嚮導的**確認**頁面上，選擇 **"備份**"。 在設定過程中，初始備份會寫入暫存區域。

   ![確認您已準備好立即備份](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    作業完成之後，預備位置便已就緒可供用於準備磁碟。

   ![立即備份嚮導頁面](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送到 Azure

*AzureOfflineBackupDiskPrep* 公用程式會準備要送到最鄰近之 Azure 資料中心的 SATA 磁碟機。 此實用程式在 Azure 備份代理安裝目錄中提供，如下所示：

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 轉到目錄，並將*AzureOfflineBackupDiskPrep*目錄複寫到連接 SATA 磁碟機的另一台電腦。 在具有連接 SATA 磁碟機的電腦上，確保：

   * 複製電腦可以使用"啟動離線備份"部分中工作流中提供的相同網路路徑訪問離線種子工作流的暫存位置。
   * 已在複本電腦上啟用 BitLocker。
   * 已安裝 Azure PowerShell 3.7.0。
   * 安裝了最新的相容瀏覽器（微軟邊緣或 Internet 資源管理器 11），並啟用了 JavaScript。
   * 複本電腦可以存取 Azure 入口網站。 必要時，複本電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果來源電腦是虛擬機器，則複本電腦必須是與來源電腦不同的實體伺服器或用戶端電腦。

1. 在複製電腦上打開一個提升的命令提示，將*AzureOfflineBackupDiskPrep*實用程式目錄作為目前的目錄。 執行以下命令：

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 參數 | 描述 |
    | --- | --- |
    | s：&lt;*暫存位置路徑*&gt; |此強制輸入用於提供在"啟動離線備份"部分的工作流中輸入的暫存位置的路徑。 |
    | p：&lt;*發佈設置檔的路徑*&gt; |此可選輸入用於提供您在"啟動離線備份"部分的工作流中輸入的 Azure 發佈設置檔的路徑。 |

    運行該命令時，實用程式請求選擇與需要準備的磁碟機對應的 Azure 導入作業。 如果只有一個導入作業與提供的暫存位置相關聯，您將看到一個像這樣的頁面。

    ![Azure 磁片準備工具輸入](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. 輸入想要準備傳輸到 Azure 之掛接磁碟的磁碟機代號 (不含結尾冒號)。
1. 在出現提示時確認您要格式化磁碟機。
1. 系統將提示您登錄到 Azure 訂閱。 輸入認證。

    ![Azure 訂閱登錄](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    然後，該工具開始準備磁片並複本備份資料。 當工具提示時，您可能需要附加其他磁片，以防提供的磁片沒有足夠的空間用於備份資料。 <br/>

    在工具順利執行結束時，命令提示字元會提供三項資訊：

   1. 您所提供的一或多個磁碟已準備好寄送到 Azure。
   1. 您將收到已創建導入作業的確認。 匯入作業會使用您提供的名稱。
   1. 此工具會顯示 Azure 資料中心的寄送地址。

      ![Azure 磁片準備已完成](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 在命令執行結束時，您可更新寄送資訊。

1. 將磁片運送到工具提供的位址。 保留跟蹤編號以供將來參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 導入作業可以具有相同的跟蹤編號。 確保實用程式在單個 Azure 導入作業下準備的磁碟機在單個包中一起運送，並且包有一個唯一的跟蹤編號。 不要將作為單獨 Azure 導入作業的一部分準備的磁碟機合併到單個包中。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 導入作業的發貨詳細資訊

以下過程更新 Azure 導入作業傳送詳細資訊。 此資訊包含以下詳細資料：

* 將磁片傳遞到 Azure 的運營商的名稱。
* 返回磁片的運輸詳細資訊。

1. 登入您的 Azure 訂用帳戶。
1. 在主功能表上，選擇 **"所有服務**"。 在"**所有服務**"對話方塊中，輸入 **"導入**"。 當您看到**導入/匯出作業**時，請選擇它。

    ![輸入運輸資訊](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    將打開 **"導入/匯出作業"** 功能表，並顯示所選訂閱中的所有導入/匯出作業的清單。

1. 如果您有多個訂閱，請選擇用於導入備份資料的訂閱。 然後選擇新創建的導入作業以打開其詳細資訊。

    ![查看運輸資訊](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 在導入作業的 **"設置"** 功能表上，選擇 **"管理發貨資訊**"。 輸入退貨運輸詳細資訊。

    ![存儲運輸資訊](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 當您從運輸承運人獲得跟蹤編號時，請在 Azure 導入作業概述頁中選擇橫幅，然後輸入以下詳細資訊。

   > [!IMPORTANT]
   > 確定運送業者資訊和追蹤號碼已在建立 Azure 匯入作業的兩週內更新。 未能在兩周內驗證此資訊可能會導致作業被刪除，並且磁碟機無法處理。

   ![跟蹤資訊更新警報](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![承運商資訊和跟蹤號碼](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>處理磁碟機所需的時間

處理 Azure 導入作業所需的時間各不相同。 處理時間基於裝運時間、作業類型、要複製的資料的類型和大小以及提供的磁片大小等因素。 Azure 導入/匯出服務沒有 SLA。 收到磁片後，該服務將努力在 7 到 10 天內完成 Azure 存儲帳戶的備份資料副本。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 導入作業狀態

可以從 Azure 門戶監視導入作業的狀態。 轉到 **"導入/匯出作業"頁**並選擇作業。 有關導入作業狀態的詳細資訊，請參閱[什麼是 Azure 導入/匯出服務？](../storage/common/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>完成工作流

在匯入作業順利完成後，儲存體帳戶中就會有初始備份資料可供使用。 在下一次計畫備份時，Azure 備份會將資料內容從存儲帳戶複製到恢復服務保存庫。

   ![將資料複製到恢復服務保存庫](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

到了下一個排定的備份時，Azure 備份會執行增量備份。

### <a name="clean-up-resources"></a>清除資源

完成初始備份後，可以安全地刪除導入到 Azure 存儲容器的資料和暫存位置中的備份資料。

## <a name="next-steps"></a>後續步驟

* 有關 Azure 導入/匯出服務工作流的任何問題，請參閱[使用 Microsoft Azure 導入/匯出服務將資料傳輸到 Blob 存儲](../storage/common/storage-import-export-service.md)。
