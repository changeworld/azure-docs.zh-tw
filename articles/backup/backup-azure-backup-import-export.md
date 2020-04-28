---
title: 使用 Azure 匯入/匯出服務植入離線備份
description: 瞭解如何使用 Azure 備份使用 Azure 匯入/匯出服務從網路傳送資料。 本文說明如何使用 Azure 匯入/匯出服務離線植入初始備份資料。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 642787e17f347bf8233e50c65d26a1661b08fcfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183886"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 備份中的離線備份工作流程

Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料，且需要較多網路頻寬，相較之下，後續備份只會傳輸差異/增量部分。 透過離線植入程序，Azure 備份可以使用磁碟將離線備份資料上傳至 Azure。

Azure 備份離線植入程式與[Azure 匯入/匯出服務](../storage/common/storage-import-export-service.md)緊密整合。 您可以使用此服務，使用磁片將初始備份資料傳輸至 Azure。 如果您需要透過高延遲和低頻寬網路傳輸的初始備份資料量 tb，您可以使用離線植入工作流程將初始備份複本傳送至 Azure 資料中心的一或多個硬碟。 下圖提供工作流程中的步驟概觀。

  ![離線匯入工作流程處理的總覽](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

離線備份程式牽涉到下列步驟：

1. 將備份資料寫入到暫存位置，而不是透過網路傳送備份資料。
1. 使用*AzureOfflineBackupDiskPrep*公用程式，將暫存位置中的資料寫入一或多個 SATA 磁片。
1. 作為準備工作的一部分， *AzureOfflineBackupDiskPrep*公用程式會建立 Azure 匯入作業。 將 SATA 磁碟機傳送至最接近的 Azure 資料中心，並參考匯入作業以連線活動。
1. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
1. Azure 備份會將儲存體帳戶中的資料複製到「復原服務」保存庫，而且會排定增量備份。

## <a name="supported-configurations"></a>支援的設定

下列 Azure 備份的功能或工作負載支援使用離線備份：

> [!div class="checklist"]
>
> * 使用 Microsoft Azure 復原服務（MARS）代理程式來備份檔案和資料夾，也稱為 Azure 備份代理程式。
> * 使用 System Center Data Protection Manager （DPM）來備份所有工作負載和檔案。
> * 使用 Microsoft Azure 備份伺服器來備份所有工作負載和檔案。

   > [!NOTE]
   > 使用 Azure 備份代理程式完成的系統狀態備份不支援離線備份。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisites

  > [!NOTE]
  > 下列必要條件和工作流程僅適用于使用[最新 Azure 復原服務代理程式](https://aka.ms/azurebackup_agent)的離線備份檔案和資料夾。 若要使用 System Center DPM 或 Azure 備份伺服器執行工作負載的離線備份，請參閱[DPM 和 Azure 備份伺服器的離線備份工作流程](backup-azure-backup-server-import-export-.md)。

在您啟動離線備份工作流程之前，請先完成下列必要條件：

* 建立復原[服務保存庫](backup-azure-recovery-services-vault-overview.md)。 若要建立保存庫，請依照[建立復原服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步驟進行。
* 請確定只有[最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)會安裝在 windows Server 或 windows 用戶端上（如果適用），而且電腦已向復原服務保存庫註冊。
* 執行 Azure 備份代理程式的電腦上需要 Azure PowerShell 3.7.0。 下載並[安裝3.7.0 版本的 Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* 在執行 Azure 備份代理程式的電腦上，確定已安裝 Microsoft Edge 或 Internet Explorer 11，而且已啟用 JavaScript。
* 在與復原服務保存庫相同的訂用帳戶中建立 Azure 儲存體帳戶。
* 確定您有建立 Azure Active Directory 應用程式[所需的權限](../active-directory/develop/howto-create-service-principal-portal.md)。 離線備份工作流程會在與 Azure 儲存體帳戶相關聯的訂用帳戶中建立 Azure Active Directory 應用程式。 應用程式的目標是要針對離線備份工作流程所需的 Azure 匯入/匯出服務，提供安全且有範圍的存取權 Azure 備份。
* 向包含 Azure 儲存體帳戶的訂用帳戶註冊*ImportExport*資源提供者。 若要註冊資源提供者：
    1. 在主功能表上，選取 [**訂閱**]。
    1. 如果您訂閱了多個訂用帳戶，請選取您打算用於離線備份的訂用帳戶。 如果您只使用一個訂用帳戶，則您的訂用帳戶隨即出現。
    1. 在 [訂用帳戶] 功能表上，選取 [**資源提供者**] 以查看提供者清單。
    1. 在提供者清單中，向下*ImportExport*。 如果**狀態**為**NotRegistered**，請選取 [**註冊**]。

        ![註冊資源提供者](./media/backup-azure-backup-import-export/registerimportexport.png)

* 已建立具有足夠磁碟空間來存放初始複本的內部或外部暫存位置 (可能是網路共用或電腦上任何額外的磁碟機)。 例如，如果您想要備份 500 GB 的檔案伺服器，請確定臨時區域至少為 500 GB。 (由於壓縮的關係，實際使用量會較少)。
* 當您將磁片傳送至 Azure 時，請只使用2.5 英寸 SSD 或 2.5-寸或 3.5-寸 SATA II/III 內部硬碟。 您可以使用高達 10 TB 的硬碟。 檢查 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-requirements.md#supported-hardware)以取得服務所支援的最新磁碟機組合。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」**)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 請確定已在複本電腦上啟用 BitLocker。

## <a name="workflow"></a>工作流程

本節說明離線備份工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 如果您有關于匯入服務或程式任何層面的問題，請參閱[Azure 匯入/匯出服務總覽檔](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>起始離線備份

1. 當您排定復原服務代理程式上的備份時，您會看到此頁面。

    ![匯入頁面](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. 選取 [**使用我自己的磁片傳送**] 選項。

    > [!NOTE]
    > 使用 [Azure 資料箱] 選項可離線傳輸初始備份資料。 此選項可節省購買您自己的 Azure 相容磁片所需的工作。 它提供 Microsoft 專屬、安全且防拆的 Azure 資料箱裝置，可由復原服務代理程式直接將備份資料寫入其中。

1. 選取 **[下一步]**，並仔細填入方塊。

    ![輸入您的磁片詳細資料](./media/backup-azure-backup-import-export/your-disk-details.png)

   您填寫的方塊如下：

    * **預備位置**：初始備份所寫入的暫時儲存體位置。 預備位置可能位於網路共用或本機電腦上。 如果 [複製電腦] 和 [來源電腦] 不同，請指定預備位置的完整網路路徑。
    * **Azure Resource Manager 儲存體帳戶**：任何 Azure 訂用帳戶中 Resource Manager 類型儲存體帳戶的名稱（一般用途 v1 或一般用途 v2）。
    * **Azure 儲存體容器**： Azure 儲存體帳戶中的目的地儲存體 blob 名稱，其中的備份資料會在複製到復原服務保存庫之前匯入。
    * **Azure 訂**用帳戶識別碼： azure 儲存體帳戶建立所在的 azure 訂用帳戶識別碼。
    * **Azure 匯入作業名稱**： Azure 匯入/匯出服務和 Azure 備份追蹤將磁片上的資料傳輸至 Azure 的唯一名稱。
  
   填寫完方塊之後，請選取 **[下一步]**。 儲存**預備位置**和 Azure 匯**入作業名稱**資訊。 這是準備磁片的必要條件。

1. 出現提示時，請登入您的 Azure 訂用帳戶。 您必須登入，Azure 備份才能建立 Azure Active Directory 應用程式。 輸入存取 Azure 匯入/匯出服務的必要許可權。

    ![Azure 訂用帳戶登入頁面](./media/backup-azure-backup-import-export/azure-login.png)

1. 完成工作流程。 在 [Azure 備份代理程式主控台] 上，選取 [**立即備份**]。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

1. 在嚮導的 [**確認**] 頁面上，選取 [**備份**]。 在設定過程中，初始備份會寫入暫存區域。

   ![確認您已經準備好立即備份](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    作業完成之後，預備位置便已就緒可供用於準備磁碟。

   ![立即備份 Wizard 頁面](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送到 Azure

*AzureOfflineBackupDiskPrep* 公用程式會準備要送到最鄰近之 Azure 資料中心的 SATA 磁碟機。 此公用程式可在下列路徑中的 Azure 備份代理程式安裝目錄中取得：

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 移至目錄，並將*AzureOfflineBackupDiskPrep*目錄複寫到連接 SATA 磁片磁碟機的另一部電腦。 在具有連接 SATA 磁片磁碟機的電腦上，確定：

   * 複製電腦可以使用「起始離線備份」一節的工作流程中所提供的相同網路路徑，存取離線植入工作流程的預備位置。
   * 已在複本電腦上啟用 BitLocker。
   * 已安裝 Azure PowerShell 3.7.0。
   * 已安裝最新相容的瀏覽器（Microsoft Edge 或 Internet Explorer 11），並已啟用 JavaScript。
   * 複本電腦可以存取 Azure 入口網站。 必要時，複本電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果來源電腦是虛擬機器，則複本電腦必須是與來源電腦不同的實體伺服器或用戶端電腦。

1. 以*AzureOfflineBackupDiskPrep*公用程式目錄作為目前的目錄，在複製電腦上開啟提升許可權的命令提示字元。 執行下列命令：

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 參數 | 描述 |
    | --- | --- |
    | s：&lt;*預備位置路徑*&gt; |此強制輸入是用來提供您在「起始離線備份」一節的工作流程中所輸入的預備位置路徑。 |
    | p：&lt;*publishsettingsfile 的路徑的路徑*&gt; |此選擇性輸入是用來提供您在「起始離線備份」一節中的工作流程中所輸入的 Azure 發佈設定檔案的路徑。 |

    當您執行命令時，公用程式會要求選取對應于需要準備之磁片磁碟機的 Azure 匯入作業。 如果只有單一匯入作業與所提供的暫存位置相關聯，您會看到類似這一頁的頁面。

    ![Azure 磁片準備工具輸入](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. 輸入想要準備傳輸到 Azure 之掛接磁碟的磁碟機代號 (不含結尾冒號)。
1. 在出現提示時確認您要格式化磁碟機。
1. 系統會提示您登入您的 Azure 訂用帳戶。 輸入認證。

    ![Azure 訂用帳戶登入](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    然後，此工具會開始準備磁片並複本備份資料。 如果提供的磁片沒有足夠的空間可供備份資料使用，您可能需要在工具提示時附加額外的磁片。 <br/>

    在工具順利執行結束時，命令提示字元會提供三項資訊：

   1. 您所提供的一或多個磁碟已準備好寄送到 Azure。
   1. 您會收到已建立匯入作業的確認。 匯入作業會使用您提供的名稱。
   1. 此工具會顯示 Azure 資料中心的寄送地址。

      ![Azure 磁片準備已完成](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 在命令執行結束時，您可更新寄送資訊。

1. 將磁片寄送至工具提供的位址。 保留追蹤號碼以供日後參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 匯入作業可以有相同的追蹤號碼。 確保公用程式在單一 Azure 匯入作業下準備的磁片磁碟機會一起隨附在單一套件中，而且封裝有單一唯一的追蹤號碼。 請勿將在個別 Azure 匯入作業中準備的磁片磁碟機合併成單一套件。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 匯入作業的寄送詳細資料

下列程式會更新 Azure 匯入作業的寄送詳細資料。 此資訊包含以下詳細資料：

* 將磁片傳送至 Azure 之貨運公司的名稱。
* 傳回磁片的寄送詳細資料。

1. 登入您的 Azure 訂用帳戶。
1. 在主功能表上，選取 [**所有服務**]。 在 [**所有服務**] 對話方塊中，輸入 [匯**入**]。 當您看到 [匯**入/匯出作業**] 時，請選取它。

    ![輸入運送資訊](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    [匯**入/匯出作業**] 功能表隨即開啟，而且會顯示所選訂用帳戶中所有匯入/匯出作業的清單。

1. 如果您有多個訂閱，請選取用來匯入備份資料的訂用帳戶。 然後選取新建立的匯入作業，以開啟其詳細資料。

    ![審查運送資訊](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 在匯入作業的 [**設定**] 功能表上，選取 [**管理運送資訊**]。 輸入退貨運送詳細資料。

    ![儲存運送資訊](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 當您的出貨貨運公司有追蹤號碼時，請選取 [Azure 匯入作業] [總覽] 頁面中的橫幅，並輸入下列詳細資料。

   > [!IMPORTANT]
   > 確定運送業者資訊和追蹤號碼已在建立 Azure 匯入作業的兩週內更新。 若無法在兩周內確認這項資訊，可能會導致作業遭到刪除，而且不會處理磁片磁碟機。

   ![追蹤資訊更新警示](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![承運商資訊和追蹤號碼](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>處理磁碟機所需的時間

處理 Azure 匯入作業所需的時間量會有所不同。 處理時間取決於傳送時間、作業類型、所複製資料的類型和大小，以及所提供磁片的大小等因素。 Azure 匯入/匯出服務沒有 SLA。 在收到磁片之後，服務會努力在7到10天內完成備份資料複製到您的 Azure 儲存體帳戶。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 匯入作業狀態

您可以從 Azure 入口網站監視匯入作業的狀態。 移至 [匯**入/匯出作業**] 頁面，然後選取您的作業。 如需匯入作業狀態的詳細資訊，請參閱[什麼是 Azure 匯入/匯出服務？](../storage/common/storage-import-export-service.md)。

### <a name="finish-the-workflow"></a>完成工作流程

在匯入作業順利完成後，儲存體帳戶中就會有初始備份資料可供使用。 在下一次排程備份時，Azure 備份會將資料的內容從儲存體帳戶複製到復原服務保存庫。

   ![將資料複製到復原服務保存庫](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

到了下一個排定的備份時，Azure 備份會執行增量備份。

### <a name="clean-up-resources"></a>清除資源

完成初始備份之後，您可以安全地刪除匯入至 Azure 儲存體容器的資料，以及預備位置中的備份資料。

## <a name="next-steps"></a>後續步驟

* 如有任何關於 Azure 匯入/匯出服務工作流程的問題，請參閱[使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../storage/common/storage-import-export-service.md)。
