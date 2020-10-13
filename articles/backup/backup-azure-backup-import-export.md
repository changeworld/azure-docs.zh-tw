---
title: 使用 Azure 匯入/匯出服務植入離線備份
description: 瞭解如何使用「Azure 匯入/匯出服務」 Azure 備份從網路傳送資料。 本文說明如何使用 Azure 匯入/匯出服務，離線植入初始備份資料。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: f3cf44a34babab79d135923db040630a1c8e3dfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88892009"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 備份中的離線備份工作流程

Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料且需要更多網路頻寬，相較之下，後續備份只傳輸差異/增量部分。 透過離線植入程序，Azure 備份可以使用磁碟將離線備份資料上傳至 Azure。

Azure 備份離線植入程式與 [Azure 匯入/匯出服務](../storage/common/storage-import-export-service.md)緊密整合。 您可以使用這項服務，使用磁片將初始備份資料傳輸至 Azure。 如果您有 tb (Tb 的初始備份資料) 需要透過高延遲和低頻寬網路傳輸的資料，您可以使用離線植入工作流程，將一或多個硬碟上的初始備份複本傳送至 Azure 資料中心。 下圖提供工作流程中的步驟概觀。

  ![離線匯入工作流程處理的總覽](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

離線備份程式涉及下列步驟：

1. 將備份資料寫入到暫存位置，而不是透過網路傳送備份資料。
1. 使用 *AzureOfflineBackupDiskPrep* 公用程式將預備位置中的資料寫入一或多個 SATA 磁片。
1. 作為準備工作的一部分， *AzureOfflineBackupDiskPrep* 公用程式會建立 Azure 匯入作業。 將 SATA 磁碟機傳送至最接近的 Azure 資料中心，並參考匯入作業以連線活動。
1. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
1. Azure 備份會將儲存體帳戶中的資料複製到「復原服務」保存庫，而且會排定增量備份。

## <a name="supported-configurations"></a>支援的設定

下列 Azure 備份功能或工作負載支援使用離線備份：

> [!div class="checklist"]
>
> * 使用 Microsoft Azure 復原服務 (MARS) 代理程式來備份檔案和資料夾，也稱為 Azure 備份代理程式。
> * 使用 System Center Data Protection Manager (DPM) 來備份所有工作負載和檔案。
> * 使用 Microsoft Azure 備份 Server 來備份所有工作負載和檔案。

   > [!NOTE]
   > 使用 Azure 備份代理程式完成的系統狀態備份不支援離線備份。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisites

  > [!NOTE]
  > 下列必要條件和工作流程僅適用于使用 [最新的 Azure 復原服務代理程式](https://aka.ms/azurebackup_agent)進行檔案和資料夾的離線備份。 若要使用 System Center DPM 或 Azure 備份伺服器執行工作負載的離線備份，請參閱 [DPM 和 Azure 備份伺服器的離線備份工作流程](backup-azure-backup-server-import-export.md)。

開始離線備份工作流程之前，請先完成下列必要條件：

* 建立復原 [服務保存庫](backup-azure-recovery-services-vault-overview.md)。 若要建立保存庫，請依照 [建立復原服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步驟執行。
* 請確定只在 Windows Server 或 Windows 用戶端上安裝 [最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent) ，並向復原服務保存庫註冊電腦。
* 執行 Azure 備份代理程式的電腦上必須要有 Azure PowerShell 3.7.0。 下載並 [安裝 Azure PowerShell 的3.7.0 版本](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* 在執行 Azure 備份代理程式的電腦上，確定已安裝 Microsoft Edge 或 Internet Explorer 11，而且已啟用 JavaScript。
* 在與復原服務保存庫相同的訂用帳戶中建立 Azure 儲存體帳戶。
* 請確定您有建立 Azure Active Directory 應用程式的[必要權限](../active-directory/develop/howto-create-service-principal-portal.md) \(部分機器翻譯\)。 離線備份工作流程會在與 Azure 儲存體帳戶相關聯的訂用帳戶中建立 Azure Active Directory 應用程式。 應用程式的目標是要為 Azure 備份提供 Azure 匯入/匯出服務的安全且限域存取權，這是離線備份工作流程所需的服務。
* 向包含 Azure 儲存體帳戶的訂用帳戶註冊 *microsoft.importexport* 資源提供者。 註冊資源提供者：
    1. 在主功能表上，選取 [ **訂閱**]。
    1. 如果您訂閱多個訂用帳戶，請選取您打算用於離線備份的訂用帳戶。 如果您只使用一個訂用帳戶，則會顯示您的訂用帳戶。
    1. 在 [訂用帳戶] 功能表上選取 [ **資源提供者** ]，以查看提供者清單。
    1. 在提供者清單中，向下 *microsoft.importexport*。 如果 **狀態** 為 **NotRegistered**，請選取 [ **註冊**]。

        ![註冊資源提供者](./media/backup-azure-backup-import-export/registerimportexport.png)

* 會建立預備位置，可以是網路共用或電腦上的任何其他磁碟機 (內接式或外接式)，且必須有足夠磁碟空間保存您的初始複本。 例如，如果您想要備份 500 GB 的檔案伺服器，請確定預備區域至少為 500 GB。 (因為會壓縮，所以使用量更小。)
* 當您將磁片傳送至 Azure 時，請只使用2.5 英寸 SSD 或 2.5/英寸或 3.5-英寸 SATA II/III 內部硬碟。 您可以使用高達 10 TB 的硬碟。 如需服務支援的最新一組磁碟機，請參閱 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-requirements.md#supported-hardware) \(部分機器翻譯\)。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」**)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 確定已在複製電腦上啟用 BitLocker。

## <a name="workflow"></a>工作流程

本節說明離線備份工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 如果您有關于匯入服務或處理常式任何層面的問題，請參閱 [Azure 匯入/匯出服務的總覽檔](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>起始離線備份

1. 當您在復原服務代理程式上排程備份時，您會看到此頁面。

    ![匯入頁面](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. 選取 [ **使用我自己的磁片傳輸**] 選項。

    > [!NOTE]
    > 使用 Azure 資料箱選項可離線傳送初始備份資料。 此選項可節省購買您自己的 Azure 相容磁片所需的工作。 它提供 Microsoft 專屬、安全且防拆的 Azure 資料箱裝置，可讓復原服務代理程式直接將備份資料寫入其中。

1. 選取 **[下一步]**，並仔細填寫方塊。

    ![輸入您的磁片詳細資料](./media/backup-azure-backup-import-export/your-disk-details.png)

   您填寫的方塊如下：

    * **預備位置**：初始備份複本寫入的暫存位置。 預備位置可能位於網路共用或本機電腦上。 如果複製電腦和來源電腦不同，請指定預備位置的完整網路路徑。
    * **Azure Resource Manager 儲存體帳戶**：任何 Azure 訂用帳戶中 Resource Manager 類型儲存體帳戶 (一般用途 v1 或一般用途 v2) 的名稱。
    * **Azure 儲存體容器**：在複製到復原服務保存庫之前，在 Azure 儲存體帳戶中匯入備份資料的目的地 blob 儲存體容器名稱。
    * **Azure 訂用帳戶識別碼**：Azure 儲存體帳戶建立於其中的 Azure 訂用帳戶識別碼。
    * **Azure 匯入作業名稱**： Azure 匯入/匯出服務和 Azure 備份用來追蹤將磁片上的資料傳輸至 Azure 的唯一名稱。
  
   填妥方塊之後，請選取 **[下一步]**。 儲存 **預備位置** 和 Azure 匯 **入作業名稱** 資訊。 需要準備磁片。

1. 出現提示時，請登入您的 Azure 訂用帳戶。 您必須登入，才能讓 Azure 備份建立 Azure Active Directory 應用程式。 輸入存取 Azure 匯入/匯出服務的必要許可權。

    ![Azure 訂用帳戶登入頁面](./media/backup-azure-backup-import-export/azure-login.png)

1. 完成工作流程。 在 Azure 備份代理程式主控台上，選取 [ **立即備份**]。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

1. 在嚮導的 [ **確認** ] 頁面上，選取 [ **備份**]。 在設定過程中，初始備份會寫入暫存區域。

   ![確認您已準備好立即備份](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    作業完成後，預備位置即可用於磁碟準備。

   ![立即備份嚮導頁面](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送至 Azure

*AzureOfflineBackupDiskPrep* 公用程式會準備要傳送到最近 Azure 資料中心的 SATA 磁碟機。 此公用程式可在下列路徑的 Azure 備份代理程式安裝目錄中取得：

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 移至目錄，並將 *AzureOfflineBackupDiskPrep* 目錄複寫到連接 SATA 磁片磁碟機的另一部電腦。 在連接 SATA 磁片磁碟機的電腦上，確定：

   * 複製電腦可以使用在「起始離線備份」一節的工作流程中所提供的相同網路路徑，存取離線植入工作流程的預備位置。
   * 已在複製電腦上啟用 BitLocker。
   * 已安裝 Azure PowerShell 3.7.0。
   * 已安裝最新的相容瀏覽器 (Microsoft Edge 或 Internet Explorer 11) ，且已啟用 JavaScript。
   * 複製電腦可以存取 Azure 入口網站。 如有必要，複製電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果來源電腦是虛擬機器，則複製電腦必須是與來源電腦不同的實體伺服器或用戶端機器。

1. 使用 *AzureOfflineBackupDiskPrep* 公用程式目錄作為目前的目錄，在複製電腦上開啟提高許可權的命令提示字元。 執行下列命令：

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 參數 | 說明 |
    | --- | --- |
    | s:&lt;*預備位置路徑*&gt; |這項強制輸入是用來提供您在「起始離線備份」一節的工作流程中輸入的預備位置路徑。 |
    | p:&lt;*PublishSettingsFile 的路徑*&gt; |這個選擇性的輸入是用來提供 Azure 發行設定檔案的路徑。  |

    當您執行命令時，公用程式會要求選取對應于需要準備之磁片磁碟機的 Azure 匯入作業。 如果只有單一匯入作業與提供的預備位置相關聯，您會看到類似此頁面的頁面。

    ![Azure 磁片準備工具輸入](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. 針對您要準備傳輸到 Azure 的已掛接磁碟，輸入不含後置冒號的磁碟機代號。
1. 出現提示時，請確認進行磁碟機格式化。
1. 系統會提示您登入 Azure 訂用帳戶。 輸入認證。

    ![Azure 訂用帳戶登入](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    然後，此工具會開始準備磁片並複本備份資料。 如果所提供的磁片沒有足夠的空間可用於備份資料，則您可能需要在工具提示時連接其他磁片。 <br/>

    在工具成功執行結束時，命令提示字元會提供三個資訊：

   1. 您提供的一或多個磁碟已準備好寄送至 Azure。
   1. 您會收到已建立匯入作業的確認。 匯入作業會使用您提供的名稱。
   1. 工具顯示 Azure 資料中心的寄送地址。

      ![Azure 磁片準備完成](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 在命令執行結束時，您可以更新寄送資訊。

1. 將磁片寄送至工具提供的位址。 保留追蹤號碼供日後參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 匯入作業可以有相同的追蹤號碼。 請確定單一 Azure 匯入作業下的公用程式所備妥的磁片磁碟機會在單一套件中一起出貨，而且套件具有單一唯一的追蹤號碼。 請勿在單一套件中將備妥的磁片磁碟機合併為個別 Azure 匯入作業的一部分。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 匯入作業的寄送詳細資料

下列程式會更新 Azure 匯入作業的寄送詳細資料。 此資訊包含有關下列項目的詳細資料：

* 將磁片傳遞給 Azure 的貨運公司名稱。
* 傳回磁片的運送詳細資料。

1. 登入 Azure 訂用帳戶。
1. 在主功能表上，選取 [ **所有服務**]。 在 [ **所有服務** ] 對話方塊中，輸入匯 **入**。 當您看到匯 **入/匯出作業**時，請選取它。

    ![輸入出貨資訊](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    [匯 **入/匯出作業** ] 功能表隨即開啟，並顯示所選訂用帳戶中所有匯入/匯出作業的清單。

1. 如果您有多個訂用帳戶，請選取用來匯入備份資料的訂用帳戶。 然後選取新建立的匯入作業，以開啟其詳細資料。

    ![複習出貨資訊](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 在匯入作業的 [ **設定** ] 功能表上，選取 [ **管理寄送資訊**]。 輸入退貨寄送詳細資料。

    ![儲存寄送資訊](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 當您有來自貨運公司的追蹤號碼時，請選取 [Azure 匯入作業] 總覽頁面中的橫幅，並輸入下列詳細資料。

   > [!IMPORTANT]
   > 務必在 Azure 匯入作業建立的兩週內更新貨運公司資訊與追蹤號碼。 若無法在兩周內確認這項資訊，將會導致作業遭到刪除，而且不會處理磁片磁碟機。

   ![追蹤資訊更新警示](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![承運商資訊和追蹤號碼](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>處理磁碟機的時間

處理 Azure 匯入作業所需的時間量會有所不同。 處理時間是根據出貨時間、作業類型、複製的資料類型和大小，以及所提供的磁片大小等因素而定。 Azure 匯入/匯出服務沒有 SLA。 收到磁片之後，服務會在7到10天內，致力於將備份資料複製到您的 Azure 儲存體帳戶。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 匯入作業狀態

您可以從 Azure 入口網站監視匯入作業的狀態。 移至 [匯 **入/匯出作業** ] 頁面，然後選取您的作業。 如需匯入作業狀態的詳細資訊，請參閱 [什麼是 Azure 匯入/匯出服務？](../storage/common/storage-import-export-service.md)。

### <a name="finish-the-workflow"></a>完成工作流程

在匯入作業順利完成後，儲存體帳戶中就會有初始備份資料可供使用。 下次排定的備份時，Azure 備份會將資料的內容從儲存體帳戶複製到復原服務保存庫。

   ![將資料複製到復原服務保存庫](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

到了下一個排定的備份時，Azure 備份會執行增量備份。

### <a name="clean-up-resources"></a>清除資源

初始備份完成後，您可以安全地刪除匯入至 Azure 儲存體容器的資料，以及預備位置中的備份資料。

## <a name="next-steps"></a>接下來的步驟

* 如有任何關於 Azure 匯入/匯出服務工作流程的問題，請參閱 [使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../storage/common/storage-import-export-service.md)。
