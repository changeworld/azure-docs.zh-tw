---
title: 適用於 DPM 和 Azure 備份伺服器的離線備份
description: 使用 Azure 備份，您可以使用 Azure 匯入/匯出服務，將資料從網路傳送。 本文說明 DPM 和 Azure 備份伺服器的離線備份工作流程。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701808"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>DPM 和 Azure 備份伺服器 (MABS) 的離線備份工作流程

>[!IMPORTANT]
> 這些步驟適用于 DPM 2019 UR1 (或更新版本) 和 MABS v3 UR1 (或更高版本) 。

System Center Data Protection Manager 和 Azure 備份伺服器 (MABS) 與 Azure 備份整合，並使用數個內建的效率，在資料備份至 Azure 的初始完整備份期間，節省網路和儲存成本。 初始完整備份通常會傳輸大量資料且需要更多網路頻寬，相較之下，後續備份只傳輸差異/增量部分。 Azure 備份會壓縮初始備份。 透過離線植入的程序，Azure 備份可以使用磁碟，將已壓縮的離線初始備份資料上傳至 Azure。

Azure 備份的離線植入程式與 [Azure 匯入/匯出服務](../import-export/storage-import-export-service.md)緊密整合。 您可以使用這項服務，使用磁片將資料傳輸至 Azure。 如果您有 tb (Tb 的初始備份資料) 需要透過高延遲和低頻寬網路傳輸的資料，您可以使用離線植入工作流程，將一或多個硬碟上的初始備份複本傳送至 Azure 資料中心。 本文提供完成此工作流程的總覽和後續步驟，可針對 System Center Data Protection Manager (DPM) 和 Microsoft Azure 備份 Server (MABS) 。

> [!NOTE]
> Microsoft Azure 復原服務 (MARS) 代理程式的離線備份程式與 DPM 和 MABS 不同。 如需搭配 MARS 代理程式使用離線備份的詳細資訊，請參閱 [Azure 備份中的離線備份工作流程](backup-azure-backup-import-export.md)。 使用 Azure 備份代理程式完成的系統狀態備份不支援離線備份。
>
> MABS UR1 update 也會在 MABS 中使用 Azure 資料箱來提供離線備份的預覽。 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)若要深入瞭解，請聯絡。

## <a name="overview"></a>概觀

使用 Azure 備份和 Azure 匯入/匯出服務的離線植入功能，可輕鬆地使用磁片將資料離線上傳至 Azure。 離線備份程序涉及下列步驟：

> [!div class="checklist"]
>
> * 備份資料會寫入至預備位置，而不是透過網路傳送。
> * 接著會使用 *AzureOfflineBackupDiskPrep* 公用程式，將預備位置上的資料寫入一或多個 SATA 磁片。
> * 公用程式會自動建立 Azure 匯入作業。
> * 然後，SATA 磁片磁碟機會傳送至最接近的 Azure 資料中心。
> * 完成將備份資料上傳至 Azure 之後，Azure 備份會將備份資料複製到備份保存庫，並排定增量備份。

## <a name="prerequisites"></a>必要條件

開始離線備份工作流程之前，請確定符合下列必要條件：

* 已建立[復原服務保存庫](backup-azure-recovery-services-vault-overview.md) \(部分機器翻譯\)。 若要建立帳戶，請遵循 [建立復原服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)教學課程中的步驟------------------------------) 。
* 確定只有 [最新版的 Microsoft Azure 復原服務代理程式](https://aka.ms/azurebackup_agent) 安裝在 SC DPM 或 MABS 上，並已向復原服務保存庫註冊。
* 更新彙總套件1安裝在 SC DPM 2019 或 MABS v3 上。

  > [!NOTE]
  > 使用 DPM 2019 UR1 和 MABS v3 UR1 離線植入驗證（使用 Azure Active Directory）。

* 在 DPM 或 MABS 伺服器上，請確定已安裝 Microsoft Edge 或 Internet Explorer 11，並已啟用 JavaScript。
* 在與復原服務保存庫相同的訂用帳戶中建立 Azure 儲存體帳戶。
* 請確定您有建立 Azure Active Directory 應用程式的[必要權限](../active-directory/develop/howto-create-service-principal-portal.md) \(部分機器翻譯\)。 離線備份工作流程會在與 Azure 儲存體帳戶相關聯的訂用帳戶中建立 Azure Active Directory 應用程式。 該應用程式的目標是要讓 Azure 備份能夠安全且有範圍地存取必要的 Azure 匯入服務，以執行離線備份工作流程。
* 向包含 Azure 儲存體帳戶的訂用帳戶註冊 Microsoft.ImportExport 資源提供者。 註冊資源提供者：
    1. 在主功能表中，選取 [ **訂閱**]。
    2. 如果您訂閱多個訂用帳戶，請選取您要用於離線備份的訂用帳戶。 如果您只使用一個訂用帳戶，則會顯示您的訂用帳戶。
    3. 在 [訂用帳戶] 功能表中，選取 [ **資源提供者** ] 以查看提供者清單。
    4. 在提供者清單中，向下捲動至 Microsoft.ImportExport。 如果狀態為 NotRegistered，請選取 [ **註冊**]。

       ![註冊資源提供者](./media/backup-azure-backup-server-import-export/register-import-export.png)

* 會建立預備位置，可以是網路共用或電腦上的任何其他磁碟機 (內接式或外接式)，且必須有足夠磁碟空間保存您的初始複本。 例如，如果您想要備份 500 GB 的檔案伺服器，請確定預備區域至少為 500 GB。 (因為會壓縮，所以使用量更小。)
* 針對傳送至 Azure 的磁片，請確定只使用2.5 英寸 SSD 或2.5 英寸或 3.5-英寸的 SATA II/III 內部硬碟。 您可以使用高達 10 TB 的硬碟。 如需服務支援的最新一組磁碟機，請參閱 [Azure 匯入/匯出服務文件](../import-export/storage-import-export-requirements.md#supported-hardware) \(部分機器翻譯\)。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 確定已在複製電腦上啟用 BitLocker。

## <a name="workflow"></a>工作流程

此節中的資訊可協助您完成離線備份工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 如果您對匯入服務或此程序的任何層面有疑問，請參閱稍早提及的[匯入服務概觀](../import-export/storage-import-export-service.md)文件 \(部分機器翻譯\)。

## <a name="initiate-offline-backup"></a>起始離線備份

1. 當您建立使用線上保護的新保護群組，或將線上保護新增至現有的保護群組時，您會看到下列畫面。 若要選取初始線上複寫方法，請選取 [ **使用我自己的磁片傳輸** ] 並選取 **[下一步]**。

    ![匯入畫面](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. [Azure 登入] 頁面隨即開啟。 使用您的 Azure 使用者帳戶登入，該帳戶具有 Azure 訂用帳戶的 *擁有* 者角色許可權。

    ![Azure 登入頁面](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. 在 [使用您自己的磁碟] 頁面上輸入資料。

   ![使用您自己的磁片的輸入](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   輸入的描述如下：

   * **預備位置**：初始備份複本寫入的暫存位置。 預備位置可能位於網路共用或本機電腦上。 如果複製電腦和來源電腦不同，請指定預備位置的完整網路路徑。
   * **Azure Resource Manager 儲存體帳戶**：任何 Azure 訂用帳戶中 Resource Manager 類型儲存體帳戶 (一般用途 v1 或一般用途 v2) 的名稱。
   * **Azure 儲存體容器**：Azure 儲存體帳戶中備份資料所匯入的目的地 Blob 儲存體容器名稱。
   * **Azure 訂用帳戶識別碼**：Azure 儲存體帳戶建立於其中的 Azure 訂用帳戶識別碼。
   * **Azure 匯入工作名稱**：「Azure 匯入服務」與「Azure 備份」追蹤資料傳輸 (使用磁碟寄送至 Azure) 的唯一名稱。

    儲存 **預備位置** 和您所提供的 **Azure 匯入作業名稱** 資訊。 需要準備磁片。

4. 完成建立或更新保護的工作流程。 接著，為了起始離線備份複本，以滑鼠右鍵按一下 [保護群組]，然後選擇 [建立復原點] 選項。 然後選擇 [線上保護] 選項。

   ![建立復原點](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. 在 [監視] 窗格中，監視線上複本建立作業。 作業應該會順利完成，並出現警告 [正在等待 Azure 匯入工作完成]。

   ![完成復原點](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. 作業完成後，預備位置即可用於磁碟準備。

## <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送至 Azure

*AzureOfflineBackupDiskPrep* 公用程式會準備要傳送到最近 Azure 資料中心的 SATA 磁碟機。 此公用程式可在下列路徑) 的 Azure 備份代理程式安裝目錄 (中取得： `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. 移至該目錄，並將 [AzureOfflineBackupDiskPrep] 目錄複製到連接 SATA 磁碟機的另一部電腦。 在連接 SATA 磁碟機的電腦上，請確定：

   * 複製電腦可以使用在「起始離線備份」一節的工作流程中所提供的相同網路路徑，存取離線植入工作流程的預備位置。
   * 已在複製電腦上啟用 BitLocker。
   * 如果您是在 DPM 或 MABS 伺服器) 上執行 AzureOfflineBackupDiskPrep 公用程式，則 Azure PowerShell 3.7.0 安裝在複製電腦上 (不需要。
   * 已安裝最新的相容瀏覽器 (Microsoft Edge 或 Internet Explorer 11) 且已啟用 JavaScript。
   * 複製電腦可以存取 Azure 入口網站。 如有必要，複製電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果來源電腦是虛擬機器，則必須使用與複製電腦不同的實體伺服器或用戶端電腦。

1. 使用 *AzureOfflineBackupDiskPrep* 公用程式目錄作為目前的目錄，在複製電腦上開啟提高許可權的命令提示字元。 執行以下命令：

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | 參數 | 說明 |
    | --- | --- |
    | s:&lt;*預備位置路徑*&gt; |這項強制輸入是用來提供您在「起始離線備份」一節的工作流程中輸入的預備位置路徑。 |
    | p:&lt;*PublishSettingsFile 的路徑*&gt; |這個選擇性的輸入是用來提供 Azure 發行設定檔案的路徑。 |

    當您執行命令時，公用程式會要求選取 Azure 匯入作業 (對應至需要準備的磁碟機)。 如果只有單一匯入作業與所提供的預備位置相關聯，您會看到如下的畫面。

      ![磁片準備主控台](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. 針對您要準備傳輸到 Azure 的已掛接磁碟，輸入不含後置冒號的磁碟機代號。
1. 出現提示時，請確認進行磁碟機格式化。
1. 系統會提示您登入 Azure 訂用帳戶。 提供您的認證。

    ![Azure 登入畫面](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    然後，此工具會開始準備磁片並複本備份資料。 如果所提供的磁片沒有足夠的空間可用於備份資料，則您可能需要在工具提示時連接其他磁片。 <br/>

    在工具成功執行結束時，命令提示字元會提供三個資訊：
    * 您提供的一或多個磁碟已準備好寄送至 Azure。
    * 您收到已建立匯入作業的確認。 匯入作業使用您提供的名稱。
    * 工具顯示 Azure 資料中心的寄送地址。

     ![Azure 磁碟準備完成](./media/backup-azure-backup-server-import-export/console.png)

1. 在命令執行結束時，您也會看到更新寄送資訊的選項。

1. 將磁碟寄送至工具提供的地址，並保留追蹤號碼供日後參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 匯入作業可以有相同的追蹤號碼。 請確定單一 Azure 匯入作業下的公用程式所備妥的磁片磁碟機會在單一套件中一起出貨，而且套件具有單一唯一的追蹤號碼。 請勿在單一套件中將備妥的磁片磁碟機合併為不同 Azure 匯入作業的一部分。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 匯入作業上的寄送詳細資料

下列程序會更新 Azure 匯入作業的寄送詳細資料。 此資訊包含有關下列項目的詳細資料：

* 將磁碟運送至 Azure 的貨運公司名稱
* 您磁碟的退貨詳細資料

   1. 登入 Azure 訂用帳戶。
   2. 在主功能表中，選取 [ **所有服務** ]，然後在 [所有服務] 對話方塊中輸入匯入。 當您看到匯 **入/匯出作業** 時，請選取它。
       ![輸入寄送資訊](./media/backup-azure-backup-server-import-export/search-import-job.png)

       [匯入/匯出作業] 功能表清單隨即開啟，並顯示所選訂用帳戶中所有匯入/匯出作業的清單。

   3. 如果您有多個訂用帳戶，請務必選取用來匯入備份資料的訂用帳戶。 然後選取新建立的匯入作業，以開啟其詳細資料。

       ![檢閱寄送資訊](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. 在匯入作業的 [設定] 功能表上，選取 [ **管理寄送資訊** ]，然後輸入退貨出貨詳細資料。

       ![儲存寄送資訊](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. 當您有來自貨運公司的追蹤號碼時，請選取 [Azure 匯入作業] 總覽頁面中的橫幅，並輸入下列詳細資料：

      > [!IMPORTANT]
      > 務必在 Azure 匯入作業建立的兩週內更新貨運公司資訊與追蹤號碼。 如果無法在兩週內驗證此資訊，可能會導致作業遭刪除且沒有處理磁碟機。

      ![作業總覽](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![追蹤資訊](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>處理磁碟機的時間

處理 Azure 匯入作業所需的時間量會有所不同。 處理時間取決於運送時間、作業類型、複製的資料類型和大小，以及所提供磁片的大小等因素。 Azure 匯入/匯出服務沒有 SLA。 收到磁片之後，服務會在7到10天內，致力於將備份資料複製到您的 Azure 儲存體帳戶。 下一節會說明如何監視 Azure 匯入作業的狀態。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 匯入作業狀態

若要從 Azure 入口網站監視匯入作業的狀態，您可以瀏覽到 [匯入/匯出作業] 頁面並選取您的作業。 如需匯入作業狀態的詳細資訊，請參閱[儲存體匯入匯出服務](../import-export/storage-import-export-service.md) \(部分機器翻譯\) 一文。

### <a name="complete-the-workflow"></a>完成工作流程

匯入作業完成之後，即可在您的儲存體帳戶中取得初始備份資料。 下次排定的備份時，Azure 備份會將資料的內容從儲存體帳戶複製到復原服務保存庫。

在下次排定的線上複本建立作業期間，Data Protection Manager 會對初始備份複本執行增量備份。

## <a name="next-steps"></a>後續步驟

* 如有任何關於 Azure 匯入/匯出服務工作流程的問題，請參閱 [使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../import-export/storage-import-export-service.md)。
