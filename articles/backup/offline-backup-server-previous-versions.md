---
title: Data Protection Manager （DPM）和 Microsoft Azure 備份 Server （MABS）的離線備份-舊版
description: 使用 Azure 備份，您可以使用 Azure 匯入/匯出服務從網路傳送資料。 本文說明 DPM 和 Azure 備份伺服器的離線備份工作流程。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: f39e93973deab09eb328eeafcff4e49b326483f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374826"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM 和 Azure 備份伺服器的離線備份工作流程（舊版）

>[!IMPORTANT]
>這些步驟適用于 DPM 2019 RTM 和較舊版本，以及 MABS v3 RTM 和更早版本。

Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料且需要更多網路頻寬，相較之下，後續備份只傳輸差異/增量部分。 Azure 備份會壓縮初始備份。 透過離線植入的程序，Azure 備份可以使用磁碟，將已壓縮的離線初始備份資料上傳至 Azure。

Azure 備份的離線植入程式與[Azure 匯入/匯出服務](../storage/common/storage-import-export-service.md)緊密整合。 您可以使用這項服務，使用磁片將資料傳輸至 Azure。 如果您需要透過高延遲和低頻寬網路傳輸的初始備份資料量 tb，您可以使用離線植入工作流程，將一或多個硬碟上的初始備份複本傳送至 Azure 資料中心。 本文提供針對 System Center Data Protection Manager （DPM）和 Microsoft Azure 備份 Server （MABS）完成此工作流程的總覽和後續步驟。

> [!NOTE]
> Microsoft Azure 復原服務（MARS）代理程式的離線備份進程與 DPM 和 MABS 不同。 如需搭配使用離線備份與 MARS 代理程式的相關資訊，請參閱[Azure 備份中的離線備份工作流程](backup-azure-backup-import-export.md)。 使用 Azure 備份代理程式完成的系統狀態備份不支援離線備份。
>

## <a name="overview"></a>總覽

透過 Azure 備份和 Azure 匯入/匯出服務的離線植入功能，您可以輕鬆地使用磁片將資料離線上傳至 Azure。 離線備份程序涉及下列步驟：

> [!div class="checklist"]
>
> * 備份資料會寫入至預備位置，而不是透過網路傳送。
> * 接著，使用*AzureOfflineBackupDiskPrep*公用程式，將預備位置上的資料寫入一或多個 SATA 磁片。
> * 公用程式會自動建立 Azure 匯入作業。
> * 然後，SATA 磁片磁碟機會傳送至最接近的 Azure 資料中心。
> * 完成將備份資料上傳至 Azure 之後，Azure 備份會將備份資料複製到備份保存庫，並排定增量備份。

## <a name="supported-configurations"></a>支援的設定

將資料從內部部署備份至 Microsoft 雲端的所有 Azure 備份部署模型都支援離線備份。 這些模型包括：

> [!div class="checklist"]
>
> * 使用 MARS 代理程式或 Azure 備份代理程式來備份檔案和資料夾。
> * 使用 DPM 備份所有工作負載和檔案。
> * 使用 MABS 備份所有工作負載和檔案。

## <a name="prerequisites"></a>必要條件

啟動離線備份工作流程之前，請確定符合下列必要條件：

* 已建立[復原服務保存庫](backup-azure-recovery-services-vault-overview.md) \(部分機器翻譯\)。 若要建立一個，請遵循[建立復原服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步驟
* Windows Server 或 Windows 用戶端上已安裝 Azure 備份代理程式或 MABS 或 DPM （如適用），而且電腦已向復原服務保存庫註冊。 請確定只使用[最新版的 Azure 備份](https://go.microsoft.com/fwlink/?linkid=229525)。
* 在您計畫用來備份資料的電腦上，[下載 Azure 發佈設定檔](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)案。 您從中下載發佈設定檔案的訂用帳戶，可能與包含復原服務保存庫的訂用帳戶不同。 如果您的訂用帳戶位於主權 Azure 雲端，請適當地使用下列連結來下載 Azure 發佈設定檔案。

    | 主權雲端區域 | Azure 發佈設定檔案連結 |
    | --- | --- |
    | 美國 | [連結](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中國 | [連結](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 已在您從中下載發佈設定檔案的訂用帳戶中，建立具有 Resource Manager 部署模型的 Azure 儲存體帳戶。 在儲存體帳戶中，建立將用來做為目的地的新 blob 容器。

  ![建立具有 Resource Manager 開發的儲存體帳戶](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* 會建立預備位置，可以是網路共用或電腦上的任何其他磁碟機 (內接式或外接式)，且必須有足夠磁碟空間保存您的初始複本。 例如，如果您想要備份 500 GB 的檔案伺服器，請確定臨時區域至少為 500 GB。 (因為會壓縮，所以使用量更小。)
* 針對傳送至 Azure 的磁片，請確定只使用2.5 英寸 SSD 或 2.5-寸或 3.5-寸 SATA II/III 內部硬碟。 您可以使用高達 10 TB 的硬碟。 如需服務支援的最新一組磁碟機，請參閱 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-requirements.md#supported-hardware) \(部分機器翻譯\)。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」**)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 請確定已在複本電腦上啟用 BitLocker。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>準備伺服器以進行離線備份程式

>[!NOTE]
> 如果您在 MARS 代理程式的安裝中找不到列出的公用程式，例如*AzureOfflineBackupCertGen.exe*，請寫入來 AskAzureBackupTeam@microsoft.com 取得它們的存取權。

* 在伺服器上開啟提升許可權的命令提示字元，然後執行下列命令：

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    此工具會建立 Azure 離線備份 Active Directory 應用程式（如果不存在的話）。

    如果應用程式已存在，此可執行檔會要求您手動將憑證上傳至租使用者中的應用程式。 請依照本節中的步驟，[手動將憑證](#manually-upload-an-offline-backup-certificate)上傳至應用程式。

* *AzureOfflineBackupCertGen.exe*工具會產生*OfflineApplicationParams.xml*檔案。 使用 MABS 或 DPM 將此檔案複製到伺服器。
* 在 DPM 實例或 Azure 備份伺服器上安裝[最新的 MARS 代理程式](https://aka.ms/azurebackup_agent)。
* 向 Azure 註冊伺服器。
* 執行下列命令：

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* 先前的命令會建立檔案 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` 。

## <a name="manually-upload-an-offline-backup-certificate"></a>手動上傳離線備份憑證

請遵循下列步驟，手動將離線備份憑證上傳至先前建立的 Azure Active Directory 應用程式，以供離線備份。

1. 登入 Azure 入口網站。
1. 移至**Azure Active Directory**  >  **應用程式註冊**。
1. 在 [**擁有的應用程式**] 索引標籤上，找出具有顯示名稱格式的應用程式 `AzureOfflineBackup _<Azure User Id` 。

    ![在擁有的應用程式索引標籤上尋找應用程式](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. 選取應用程式。 在左窗格的 [**管理**] 下，移至 [憑證] [ **& 密碼**]。
1. 檢查是否有預先存在的憑證或公開金鑰。 如果沒有，您可以在應用程式的 [**總覽**] 頁面上選取 [**刪除**] 按鈕，安全地刪除應用程式。 然後，您可以重試步驟來[準備伺服器以進行離線備份程式](#prepare-the-server-for-the-offline-backup-process)，並略過下列步驟。 否則，請繼續遵循 DPM 實例或您要設定離線備份之 Azure 備份伺服器上的這些步驟。
1. 從 [**開始**] – [**執行**]，輸入*certlm.msc*。 在 [**憑證-本機電腦**] 視窗中，選取 [**憑證-本機電腦**  >  ] [**個人**] 索引標籤。尋找名稱為的憑證 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 。
1. 選取憑證，以滑鼠右鍵按一下 [**所有**工作]，然後選取 [**匯出**] （不含私密金鑰，格式為 .cer）。
1. 移至 Azure 入口網站中的 Azure 離線備份應用程式。
1. 選取 [**管理**  >  **憑證] & 密碼**  >  **上傳憑證**]。 上傳在上一個步驟中匯出的憑證。

    ![上傳憑證](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. 在伺服器上，在 [執行] 視窗中輸入**regedit**來開啟登錄。
1. 移至登錄專案*Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows server\ Azure Backup\Config\CloudBackupProvider*。
1. 以滑鼠右鍵按一下 [ **CloudBackupProvider**]，並新增名稱為的新字串值 `AzureADAppCertThumbprint_<Azure User Id>` 。

    >[!NOTE]
    > 若要尋找 Azure 使用者識別碼，請執行下列其中一個步驟：
    >
    >* 從 Azure 連線的 PowerShell，執行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` 命令。
    >* 移至登錄路徑 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` 。

1. 以滑鼠右鍵按一下上一個步驟中新增的字串，然後選取 [**修改**]。 在 [值] 中，提供您在步驟7中匯出之憑證的指紋。 然後選取 [確定]。
1. 若要取得指紋的值，請按兩下憑證。 選取 [**詳細資料**] 索引標籤，然後向下滾動，直到您看到 [指紋] 欄位為止。 選取 [**指紋**]，然後複製值。

    ![從 [指紋] 欄位複製值](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. 繼續進行 [[工作流程](#workflow)] 區段以繼續進行離線備份程式。

## <a name="workflow"></a>工作流程

本節中的資訊可協助您完成離線備份工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 如果您有關于匯入服務或處理常式任何層面的問題，請參閱稍早所參考的匯[入服務總覽檔](../storage/common/storage-import-export-service.md)。

### <a name="initiate-offline-backup"></a>起始離線備份

1. 當您排程備份時，您會在 Windows Server、Windows 用戶端或 DPM 中看到下列頁面。

    ![匯入頁面](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    以下是 DPM 中的對應頁面。 <br/>

    ![DPM 和 Azure 備份伺服器匯入頁面](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    您填寫的方塊如下：

   * **預備位置**：初始備份複本寫入的暫存位置。 預備位置可能位於網路共用或本機電腦上。 如果 [複製電腦] 和 [來源電腦] 不同，請指定預備位置的完整網路路徑。
   * **Azure 發佈設定**：發佈設定檔案的本機路徑。
   * **Azure 匯入作業名稱**： Azure 匯入/匯出服務和 Azure 備份追蹤將磁片上的資料傳輸至 Azure 的唯一名稱。
   * **Azure 訂**用帳戶識別碼：您從中下載 azure 發佈設定檔案之訂用帳戶的 azure 訂用帳戶識別碼。
   * **Azure 儲存體帳戶**：與 azure 發佈設定檔關聯之 azure 訂用帳戶中的儲存體帳戶名稱。
   * **Azure 儲存體容器**：Azure 儲存體帳戶中備份資料的匯入目的地儲存體 Blob 名稱。

   儲存**預備位置**和您所提供的**Azure 匯入作業名稱**資訊。 這是準備磁片的必要條件。

1. 完成工作流程。 若要起始離線備份複本，請選取 [Azure 備份代理程式管理主控台] 上的 [**立即備份**]。 作為此步驟的一部分，初始備份會寫入預備區域。

    ![立即備份](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    若要在 DPM 或 Azure 備份伺服器中完成對應的工作流程，請以滑鼠右鍵按一下**保護群組**。 選取 [**建立復原點**] 選項。 然後選取 [**線上保護**] 選項。

    ![DPM 和 MABS 立即備份](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    作業完成後，預備位置即可用於磁碟準備。

    ![備份進度](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送至 Azure

*AzureOfflineBackupDiskPrep*公用程式是用來準備傳送到最接近 Azure 資料中心的 SATA 磁片磁碟機。 此公用程式可在復原服務代理程式的安裝目錄中取得，位於下列路徑中：

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 移至該目錄，然後將 [AzureOfflineBackupDiskPrep]** 目錄複製到已連接要準備之 SATA 磁碟機的複本電腦。 請確定：

   * 複製電腦可以使用「起始離線備份」一節的工作流程中所提供的相同網路路徑，存取離線植入工作流程的預備位置。
   * 已在複製電腦上啟用 BitLocker。
   * 複製電腦可以存取 Azure 入口網站。 如有必要，複製電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果來源電腦是虛擬機器，則必須使用不同的實體伺服器或用戶端電腦作為複本電腦。

1. 以*AzureOfflineBackupDiskPrep*公用程式目錄作為目前的目錄，在複製電腦上開啟提升許可權的命令提示字元。 執行下列命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 參數 | 說明 |
    | --- | --- |
    | s:&lt;*預備位置路徑*&gt; |此強制輸入是用來提供您在「起始離線備份」一節的工作流程中所輸入的預備位置路徑。 |
    | p:&lt;*PublishSettingsFile 的路徑*&gt; |此選擇性輸入是用來提供您在「起始離線備份」一節中的工作流程中所輸入的 Azure 發佈設定檔案的路徑。 |

    > [!NOTE]
    > 複本電腦與來源電腦不同時，必須提供 &lt;AzurePublishSettingFile 的路徑&gt;值。
    >
    >

    當您執行命令時，公用程式會要求選取對應于需要準備之磁片磁碟機的 Azure 匯入作業。 如果只有單一匯入作業與所提供的暫存位置相關聯，您會看到類似這一頁的頁面。

    ![Azure 磁片準備工具輸入](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. 針對您要準備傳輸到 Azure 的已掛接磁碟，輸入不含後置冒號的磁碟機代號。 出現提示時，請提供磁片磁碟機格式的確認。

    然後，此工具會開始準備磁片並複本備份資料。 如果提供的磁片沒有足夠的空間可供備份資料使用，您可能需要在工具提示時附加額外的磁片。 <br/>

    在工具順利完成之後，您所提供的一或多個磁片就已準備好寄送至 Azure。 在「起始離線備份」一節中，您在工作流程中提供之名稱的匯入作業也會在 Azure 中建立。 最後，工具會顯示磁碟所需之寄送目的地的 Azure 資料中心寄送地址。

    ![Azure 磁片準備已完成](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. 在命令執行結束時，您也會看到更新寄送資訊的選項。

    ![更新寄送資訊選項](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. 您可以立即輸入詳細資料。 此工具會引導您完成涉及一系列輸入的程式。 如果您沒有像是追蹤號碼或其他與運送相關的詳細資料等資訊，您可以結束會話。 本文稍後會提供更新寄送詳細資料的步驟。

1. 將磁片寄送至工具提供的位址。 保留追蹤號碼以供日後參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 匯入作業可以有相同的追蹤號碼。 確保公用程式在單一 Azure 匯入作業下準備的磁片磁碟機會一起隨附在單一套件中，而且封裝有單一唯一的追蹤號碼。 請不要在單一套件中將已備妥的磁片磁碟機合併為不同 Azure 匯入作業的一部分。

1. 當您有追蹤號碼資訊時，請移至正在等待匯入作業完成的來源電腦。 在提高許可權的命令提示字元中執行下列命令，並以*AzureOfflineBackupDiskPrep*公用程式目錄作為目前的目錄。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   您可以選擇性地從另一部電腦執行下列命令，例如複製電腦，並以*AzureOfflineBackupDiskPrep*公用程式目錄作為目前的目錄。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 參數 | 說明 |
    | --- | --- |
    | u: | 此強制輸入是用來更新 Azure 匯入作業的寄送詳細資料。 |
    | s:&lt;*預備位置路徑*&gt; | 當命令不是在來源電腦上執行時，會使用此強制輸入。 它可用來提供您在「起始離線備份」一節的工作流程中所輸入的預備位置路徑。 |
    | p:&lt;*PublishSettingsFile 的路徑*&gt; | 當命令不是在來源電腦上執行時，會使用此強制輸入。 它可用來提供您在「起始離線備份」一節的工作流程中所輸入的 Azure 發佈設定檔案的路徑。 |

    當命令在不同的電腦上執行時，公用程式會自動偵測來源電腦正在等待的匯入工作，或與暫存位置相關聯的匯入作業。 接著，它會提供選項，讓您透過一系列的輸入來更新出貨資訊。

    ![輸入運送資訊](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. 提供所有輸入之後，請仔細檢查詳細資料，並輸入 **[是]** 來認可您提供的出貨資訊。

    ![審查運送資訊](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. 成功更新寄送資訊之後，公用程式會提供本機位置，其中會儲存您所輸入的出貨詳細資料。

    ![儲存運送資訊](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > 使用*AzureOfflineBackupDiskPrep*公用程式，確保磁片磁碟機在提供運送資訊後的兩周內抵達 Azure 資料中心。 如果無法做到，將導致無法處理磁碟機。

在您完成先前的步驟之後，Azure 資料中心已準備好接收磁片磁碟機，並進一步處理它們，以將備份資料從磁片磁碟機傳輸到您所建立的傳統類型 Azure 儲存體帳戶。

### <a name="time-to-process-the-drives"></a>處理磁碟機的時間

處理 Azure 匯入作業所需的時間量會有所不同。 處理時間取決於運送時間、作業類型、所複製資料的類型和大小，以及所提供磁片的大小等因素。 Azure 匯入/匯出服務沒有 SLA。 在收到磁片之後，服務會努力在7到10天內完成備份資料複製到您的 Azure 儲存體帳戶。 下一節會說明如何監視 Azure 匯入作業的狀態。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 匯入作業狀態

當您的磁片磁碟機正在傳輸中，或在 Azure 資料中心複製到儲存體帳戶時，Azure 備份代理程式或 DPM 或來源電腦上的 MABS 主控台會顯示排程備份的下列作業狀態：

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

若要檢查匯入作業狀態：

1. 在來源電腦上開啟提升許可權的命令提示字元，然後執行下列命令：

     `AzureOfflineBackupDiskPrep.exe u:`

1. 輸出會顯示匯入作業的目前狀態。

    ![檢查匯入作業狀態](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

如需 Azure 匯入作業各種狀態的詳細資訊，請參閱[查看 azure 匯入/匯出作業的狀態](../storage/common/storage-import-export-view-drive-status.md)。

### <a name="finish-the-workflow"></a>完成工作流程

匯入作業完成之後，即可在您的儲存體帳戶中取得初始備份資料。 在下一次排程備份時，Azure 備份會將資料的內容從儲存體帳戶複製到復原服務保存庫。

   ![將資料複製到復原服務保存庫](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

到了下一個排定的備份時，「Azure 備份」會在初始備份複本上執行增量備份。

## <a name="next-steps"></a>後續步驟

* 如有任何關於 Azure 匯入/匯出服務工作流程的問題，請參閱[使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../storage/common/storage-import-export-service.md)。
