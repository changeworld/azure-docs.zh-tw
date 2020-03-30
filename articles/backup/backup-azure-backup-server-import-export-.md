---
title: 適用於 DPM 和 Azure 備份伺服器的離線備份
description: 使用 Azure 備份，可以使用 Azure 導入/匯出服務將資料從網路外發送。 本文介紹了 DPM 和 Azure 備份伺服器的離線備份工作流。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197035"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM 和 Azure 備份伺服器的離線備份工作流

Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料，且需要較多網路頻寬，相較之下，後續備份只會傳輸差異/增量部分。 Azure 備份會壓縮初始備份。 透過離線植入程序，Azure 備份可以使用磁碟將壓縮後的初始備份資料離線上傳至 Azure。

Azure 備份的離線種子化過程與[Azure 導入/匯出服務](../storage/common/storage-import-export-service.md)緊密集成。 您可以使用此服務使用磁片將資料傳輸到 Azure。 如果具有需要通過高延遲和低頻寬網路傳輸的初始備份資料 TB （TB），則可以使用離線種子工作流將一個或多個硬碟磁碟機上的初始備份副本傳送到 Azure 資料中心。 本文為系統中心資料保護管理器 （DPM） 和 Microsoft Azure 備份伺服器 （MABS） 提供了完成此工作流的概述和進一步步驟。

> [!NOTE]
> Microsoft Azure 恢復服務 （MARS） 代理的離線備份過程不同于 DPM 和 MABS。 有關使用 MARS 代理離線備份的資訊，請參閱[Azure 備份 中的離線備份工作流](backup-azure-backup-import-export.md)。 使用 Azure 備份代理完成的系統狀態備份不支援離線備份。
>

## <a name="overview"></a>總覽

借助 Azure 備份和 Azure 導入/匯出服務的離線種子化功能，使用磁片將資料離線上載到 Azure 非常簡單。 離線備份程序涉及下列步驟：

> [!div class="checklist"]
>
> * 備份資料寫入暫存位置，而不是通過網路發送。
> * 然後，使用*AzureOfflineBackupDiskPrep*實用程式將暫存位置上的資料寫入一個或多個 SATA 磁片。
> * 實用程式會自動創建 Azure 導入作業。
> * 然後，SATA 磁碟機將發送到最近的 Azure 資料中心。
> * 將備份資料上傳至 Azure 的作業完成之後，Azure 備份就會將備份資料複製至備份保存庫，並排程增量備份。

## <a name="supported-configurations"></a>支援的設定

Azure 備份的所有部署模型都支援離線備份，這些模型將資料從本地備份到 Microsoft 雲。 這些型號包括：

> [!div class="checklist"]
>
> * 使用 MARS 代理或 Azure 備份代理備份檔案和資料夾。
> * 使用 DPM 備份所有工作負載和檔。
> * 使用 MABS 備份所有工作負載和檔。

## <a name="prerequisites"></a>Prerequisites

在啟動離線備份工作流之前，請確保滿足以下先決條件：

* 已建立[復原服務保存庫](backup-azure-recovery-services-vault-overview.md)。 要創建一個，請按照[創建恢復服務保存庫](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步驟操作
* Azure 備份代理或 MABS 或 DPM 已安裝在 Windows 伺服器或 Windows 用戶端（如適用）上，並且電腦已註冊到恢復服務保存庫。 請確定只使用[最新版的 Azure 備份](https://go.microsoft.com/fwlink/?linkid=229525)。
* 在計畫從中備份資料的電腦上[下載 Azure 發佈設置檔](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 從中下載發佈設置檔的訂閱可能不同于包含恢復服務保存庫的訂閱。 如果您的訂閱位於主權 Azure 雲上，請使用以下適當連結下載 Azure 發佈設置檔。

    | 主權雲端區域 | Azure 發佈設置檔連結 |
    | --- | --- |
    | 美國 | [連結](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中國 | [連結](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 在下載發佈設置檔的訂閱中創建了具有資源管理器部署模型的 Azure 存儲帳戶。

  ![使用資源管理器開發創建存儲帳戶](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* 已建立具有足夠磁碟空間來存放初始複本的內部或外部暫存位置 (可能是網路共用或電腦上任何額外的磁碟機)。 例如，如果要備份 500 GB 檔案伺服器，請確保暫存區域至少為 500 GB。 (由於壓縮的關係，實際使用量會較少)。
* 對於發送到 Azure 的磁片，請確保僅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 內部硬碟磁碟機。 您可以使用高達 10 TB 的硬碟。 檢查 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-requirements.md#supported-hardware)以取得服務所支援的最新磁碟機組合。
* SATA 磁碟機必須連接到一個電腦 (又稱為「複本電腦」**)，在其中可將備份資料從「暫存位置」複製到 SATA 磁碟機。 確保在複製電腦上啟用了 BitLocker。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>為離線備份過程準備伺服器

>[!NOTE]
> 如果在安裝 MARS 代理時找不到列出的實用程式（如*AzureOfflineBackupCertGen.exe），* 請寫信AskAzureBackupTeam@microsoft.com訪問它們。

* 在伺服器上打開一個提升的命令提示，並運行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    該工具將創建 Azure 離線備份活動目錄應用程式（如果不存在）。

    如果應用程式已存在，此可執行檔要求您手動將證書上載到租戶中的應用程式。 按照[本節](#manually-upload-an-offline-backup-certificate)中的步驟手動將證書上載到應用程式。

* *Azure 離線備份.exe*工具生成*離線應用程式Params.xml*檔。 使用 MABS 或 DPM 將此檔案複製到伺服器。
* 在 DPM 實例或 Azure 備份伺服器上安裝[最新的 MARS 代理](https://aka.ms/azurebackup_agent)。
* 將伺服器註冊到 Azure。
* 執行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* 前面的命令將創建檔`C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`。

## <a name="manually-upload-an-offline-backup-certificate"></a>手動上載離線備份證書

按照以下步驟手動將離線備份證書上載到以前創建的用於離線備份的 Azure 活動目錄應用程式。

1. 登入 Azure 入口網站。
1. 轉到**Azure 活動目錄** > **應用註冊**。
1. 在 **"擁有的應用程式**"選項卡上，找到具有顯示名稱格式`AzureOfflineBackup _<Azure User Id`的應用程式。

    ![在"擁有的應用程式"選項卡上查找應用程式](./media/backup-azure-backup-import-export/owned-applications.png)

1. 選取應用程式。 在左側窗格中的 **"管理"** 下，轉到**證書&機密**。
1. 檢查現有證書或公開金鑰。 如果沒有，則可以通過選擇應用程式 **"概述"** 頁上的 **"刪除**"按鈕來安全地刪除應用程式。 然後，您可以重試為[離線備份過程準備伺服器](#prepare-the-server-for-the-offline-backup-process)的步驟，並跳過以下步驟。 否則，請繼續從要配置離線備份的 DPM 實例或 Azure 備份伺服器中執行這些步驟。
1. 選擇 **"管理電腦證書應用程式** > **個人"** 選項卡。查找名稱的證書`CB_AzureADCertforOfflineSeeding_<ResourceId>`。
1. 選擇證書，按右鍵 **"所有任務**"，**然後選擇匯出**，而不使用私密金鑰，以 .cer 格式。
1. 轉到 Azure 門戶中的 Azure 離線備份應用程式。
1. 選擇 **"管理** > **證書&機密** > **上載證書**。 上載在上一步驟中匯出的證書。

    ![上傳憑證](./media/backup-azure-backup-import-export/upload-certificate.png)

1. 在伺服器上，通過在運行視窗中輸入**註冊來**打開註冊表。
1. 轉到登錄機碼*電腦\HKEY_LOCAL_MACHINE\軟體\微軟\Windows Azure 備份\配置\雲備份提供程式*。
1. 按右鍵**CloudBackupProvider**，並添加名稱的新字串值`AzureADAppCertThumbprint_<Azure User Id>`。

    >[!NOTE]
    > 要查找 Azure 使用者 ID，執行以下步驟之一：
    >
    >* 從 Azure 連接的 PowerShell`Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`運行命令。
    >* 轉到註冊表路徑`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`。

1. 按右鍵上一步中添加的字串，然後選擇 **"修改**"。 在 值中，提供步驟 7 中匯出的證書的指紋。 然後選擇 **"確定**"。
1. 要獲取指紋的值，請按兩下證書。 選擇"**詳細資訊**"選項卡，然後向下滾動，直到看到指紋欄位。 選擇 **"拇指列印"** 並複製該值。

    ![從指紋欄位中複製值](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. 繼續到["工作流"](#workflow)部分以繼續離線備份過程。

## <a name="workflow"></a>工作流程

本節中的資訊可説明您完成離線備份工作流，以便資料可以傳遞到 Azure 資料中心並上載到 Azure 存儲。 如果您對導入服務或流程的任何方面有疑問，請參閱前面引用的[導入服務概述文檔](../storage/common/storage-import-export-service.md)。

### <a name="initiate-offline-backup"></a>起始離線備份

1. 計畫備份時，在 Windows 伺服器、Windows 用戶端或 DPM 中看到以下頁面。

    ![導入頁面](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    下面是 DPM 中的相應頁面。 <br/>
    
    ![DPM 和 Azure 備份伺服器導入頁](./media/backup-azure-backup-import-export/dpmoffline.png)

    您填寫的框包括：

   * **預備位置**：初始備份所寫入的暫時儲存體位置。 暫存位置可能位於網路共用或本地電腦上。 如果複製電腦和源電腦不同，請指定暫存位置的完整網路路徑。
   * **Azure 發佈設置**：發佈設置檔的本地路徑。
   * **Azure 導入作業名稱**：Azure 導入/匯出服務和 Azure 備份跟蹤磁片上發送到 Azure 的資料傳輸的唯一名稱。
   * **Azure 訂閱 ID**：從中下載 Azure 發佈設置檔的訂閱的 Azure 訂閱 ID。
   * **Azure 存儲帳戶**：與 Azure 發佈設置檔關聯的 Azure 訂閱中的存儲帳戶的名稱。
   * **Azure 儲存體容器**：Azure 儲存體帳戶中備份資料的匯入目的地儲存體 Blob 名稱。

   保存**暫存位置**和提供的**Azure 導入作業名稱**資訊。 準備磁片是必需的。

1. 完成工作流。 要啟動離線備份副本，請在 Azure 備份代理管理主控台上選擇 **"立即備份**"。 作為此步驟的一部分，初始備份會寫入預備區域。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

    要在 DPM 或 Azure 備份伺服器中完成相應的工作流，請按右鍵**保護組**。 選擇"**創建復原點"** 選項。 然後選擇 **"連線保護**"選項。

    ![DPM 和 MABS 立即備份](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    作業完成之後，預備位置便已就緒可供用於準備磁碟。

    ![備份進度](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>準備 SATA 磁碟機並寄送到 Azure

*AzureOfflineBackupDiskPrep*實用程式用於準備發送到最近的 Azure 資料中心的 SATA 磁碟機。 此實用程式在恢復服務代理的安裝目錄中提供，如下所示：

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 移至該目錄，然後將 [AzureOfflineBackupDiskPrep]** 目錄複製到已連接要準備之 SATA 磁碟機的複本電腦。 請確定：

   * 複製電腦可以使用"啟動離線備份"部分中工作流中提供的相同網路路徑訪問離線種子工作流的暫存位置。
   * 已在複本電腦上啟用 BitLocker。
   * 複本電腦可以存取 Azure 入口網站。 必要時，複本電腦可以與來源電腦相同。

     > [!IMPORTANT]
     > 如果源電腦是虛擬機器，則必須使用其他物理伺服器或用戶端電腦作為複製電腦。

1. 在複製電腦上打開一個提升的命令提示，將*AzureOfflineBackupDiskPrep*實用程式目錄作為目前的目錄。 執行以下命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 參數 | 描述 |
    | --- | --- |
    | s：&lt;*暫存位置路徑*&gt; |此強制輸入用於提供在"啟動離線備份"部分的工作流中輸入的暫存位置的路徑。 |
    | p：&lt;*發佈設置檔的路徑*&gt; |此可選輸入用於提供您在"啟動離線備份"部分的工作流中輸入的 Azure 發佈設置檔的路徑。 |

    > [!NOTE]
    > 複本電腦與來源電腦不同時，必須提供 &lt;AzurePublishSettingFile 的路徑&gt;值。
    >
    >

    運行該命令時，實用程式請求選擇與需要準備的磁碟機對應的 Azure 導入作業。 如果只有一個導入作業與提供的暫存位置相關聯，您將看到一個像這樣的頁面。

    ![Azure 磁片準備工具輸入](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. 輸入想要準備傳輸到 Azure 之掛接磁碟的磁碟機代號 (不含結尾冒號)。 出現提示時，請提供磁碟機格式的確認。

    然後，該工具開始準備磁片並複本備份資料。 當工具提示時，您可能需要附加其他磁片，以防提供的磁片沒有足夠的空間用於備份資料。 <br/>

    該工具成功完成後，您提供的一個或多個磁片已準備好用於傳送到 Azure。 在 Azure 中還會創建具有"啟動離線備份"部分工作流期間提供名稱的導入作業。 最後，工具會顯示磁碟所需之寄送目的地的 Azure 資料中心寄送地址。

    ![Azure 磁片準備已完成](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. 在命令執行結束時，您還會看到更新裝運資訊的選項。

    ![更新運輸資訊選項](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. 您可以立即輸入詳細資料。 該工具將指導您完成涉及一系列輸入的過程。 如果您沒有跟蹤編號等資訊或與發貨相關的其他詳細資訊，則可以結束會話。 本文稍後會提供更新寄送詳細資料的步驟。

1. 將磁片運送到工具提供的位址。 保留跟蹤編號以供將來參考。

   > [!IMPORTANT]
   > 沒有兩個 Azure 導入作業可以具有相同的跟蹤編號。 確保實用程式在單個 Azure 導入作業下準備的磁碟機在單個包中一起運送，並且包有一個唯一的跟蹤編號。 不要將作為不同 Azure 導入作業的一部分準備的磁碟機合併到單個包中。

1. 當您有跟蹤編號資訊時，轉到源電腦，該電腦正在等待導入作業完成。 在提升的命令提示中運行以下命令，將*AzureOfflineBackupDiskPrep*實用程式目錄作為目前的目錄。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   您可以選擇從其他電腦（如複製電腦）運行以下命令 *，AzureOfflineBackupDiskPrep*實用程式目錄作為目前的目錄。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 參數 | 描述 |
    | --- | --- |
    | u: | 此強制輸入用於更新 Azure 導入作業的發貨詳細資訊。 |
    | s：&lt;*暫存位置路徑*&gt; | 當命令未在源電腦上運行時，使用此強制輸入。 它用於提供在"啟動離線備份"部分的工作流中輸入的暫存位置的路徑。 |
    | p：&lt;*發佈設置檔的路徑*&gt; | 當命令未在源電腦上運行時，使用此強制輸入。 它用於提供您在"啟動離線備份"部分的工作流中輸入的 Azure 發佈設置檔的路徑。 |

    當命令在另一台電腦上運行時，實用程式會自動檢測源電腦正在等待的導入作業或與暫存位置關聯的導入作業。 然後，它提供了通過一系列輸入更新運輸資訊的選項。

    ![輸入運輸資訊](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. 提供所有輸入後，請仔細查看詳細資訊，並通過輸入**yes**提交您提供的運輸資訊。

    ![查看運輸資訊](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. 成功更新運輸資訊後，實用程式會提供存儲您輸入的運輸詳細資訊的本地位置。

    ![存儲運輸資訊](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > 確保磁碟機在使用*AzureOfflineBackupDiskPrep*實用程式提供運輸資訊後的兩周內到達 Azure 資料中心。 如果無法做到，將導致無法處理磁碟機。 

完成上述步驟後，Azure 資料中心已準備好接收磁碟機，並進一步處理它們，以便將備份資料從磁碟機傳輸到您創建的經典類型的 Azure 存儲帳戶。

### <a name="time-to-process-the-drives"></a>處理磁碟機所需的時間

處理 Azure 導入作業所需的時間各不相同。 處理時間取決於要複製的資料的裝運時間、作業類型、類型和大小以及提供的磁片大小等因素。 Azure 導入/匯出服務沒有 SLA。 收到磁片後，該服務將努力在 7 到 10 天內完成備份資料副本到 Azure 存儲帳戶。 下一節將介紹如何監視 Azure 導入作業的狀態。

### <a name="monitor-azure-import-job-status"></a>監視 Azure 導入作業狀態

當磁碟機正在傳輸或 Azure 資料中心複製到存儲帳戶時，源電腦上的 Azure 備份代理或 DPM 或 MABS 主控台將顯示計畫備份的以下作業狀態：

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

要檢查導入作業狀態：

1. 在源電腦上打開一個提升的命令提示，並運行以下命令：

     `AzureOfflineBackupDiskPrep.exe u:`

1. 輸出顯示導入作業的目前狀態。

    ![檢查導入作業狀態](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

有關 Azure 導入作業的各種狀態的詳細資訊，請參閱[查看 Azure 導入/匯出作業的狀態](../storage/common/storage-import-export-view-drive-status.md)。

### <a name="finish-the-workflow"></a>完成工作流

匯入作業完成後，儲存體帳戶中就會有初始備份資料可供使用。 在下一次計畫備份時，Azure 備份會將資料內容從存儲帳戶複製到恢復服務保存庫。

   ![將資料複製到恢復服務保存庫](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

到了下一個排定的備份時，「Azure 備份」會在初始備份複本上執行增量備份。

## <a name="next-steps"></a>後續步驟

* 有關 Azure 導入/匯出服務工作流的任何問題，請參閱[使用 Microsoft Azure 導入/匯出服務將資料傳輸到 Blob 存儲](../storage/common/storage-import-export-service.md)。
