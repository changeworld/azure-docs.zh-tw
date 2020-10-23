---
title: 使用 Azure 匯入/匯出將資料轉送至 Azure Blob | Microsoft Docs
description: 了解如何在 Azure 入口網站中建立匯入和匯出作業，將資料傳入和傳出 Blob 儲存體。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: c3be13dade9cae45994b5f7a9d6f7479e2de6256
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460728"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>使用 Azure 匯入/匯出服務將資料匯入 Azure Blob 儲存體

本文提供的逐步指示會說明如何使用 Azure 匯入/匯出服務，安全地將大量資料匯入 Azure Blob 儲存體。 若要將資料匯入到 Azure Blob，服務會要求您將包含資料的加密磁碟機寄送到 Azure 資料中心。  

## <a name="prerequisites"></a>先決條件

在建立匯入作業來將資料傳入 Azure Blob 儲存體之前，請仔細檢閱並完成此服務的下列必要條件清單。
您必須：

* 具有可用於匯入/匯出服務的有效 Azure 訂用帳戶。
* 至少具有一個包含儲存體容器的 Azure 儲存體帳戶。 請參閱[匯入/匯出服務支援的儲存體帳戶和儲存體類型](storage-import-export-requirements.md)清單。
  * 如需建立新儲存體帳戶的詳細資訊，請參閱 [如何建立儲存體帳戶](storage-account-create.md)(英文)。
  * 如需有關儲存體容器的資訊，請移至[建立儲存體容器](../blobs/storage-quickstart-blobs-portal.md#create-a-container)。
* 具有屬於[支援類型](storage-import-export-requirements.md#supported-disks)的磁碟，且數量足夠。
* 具有執行[受支援 OS 版本](storage-import-export-requirements.md#supported-operating-systems) 的 Windows 系統。
* 在 Windows 系統上啟用 BitLocker。 請參閱[如何啟用 BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)。
* 下載 Windows 系統上[最新的 WAImportExport 第1版](https://www.microsoft.com/download/details.aspx?id=42659)。 此工具的最新版本具有安全性更新，可允許適用于 BitLocker 金鑰的外部保護裝置，以及更新的解除鎖定模式功能。

  * 將檔案解壓縮至預設資料夾 `waimportexportv1`。 例如： `C:\WaImportExportV1`。
* 擁有 FedEx/DHL 帳戶。 如果您想要使用 FedEx/DHL 以外的電訊廠商，請聯絡 Azure 資料箱營運團隊 `adbops@microsoft.com` 。  
  * 帳戶必須是有效的、需要有餘額，且必須有退貨運送功能。
  * 產生匯出作業的追蹤號碼。
  * 每個作業都應該具有個別的追蹤號碼。 不支援多個作業使用相同的追蹤號碼。
  * 如果您沒有貨運公司帳戶，請移至：
    * [建立 FedEx 帳戶](https://www.fedex.com/en-us/create-account.html)，或
    * [建立 DHL 帳戶](http://www.dhl-usa.com/en/express/shipping/open_account.html) \(英文\)。

## <a name="step-1-prepare-the-drives"></a>步驟 1：準備磁碟機

此步驟會產生日誌檔案。 日誌檔案會儲存磁碟機序號、加密金鑰及儲存體帳戶詳細資料等基本資訊。

執行下列步驟來準備磁碟機。

1. 透過 SATA 連接器將磁碟機連線到 Windows 系統。
2. 在每個磁碟機上建立單一 NTFS 磁碟區。 指派磁碟機代號給磁碟區。 請勿使用掛接點。
3. 在 NTFS 磁碟區上啟用 BitLocker 加密。 如果使用 Windows Server 系統，請使用[如何在 Windows Server 2012 R2 上啟用 BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) 中的指示。
4. 將資料複製到加密磁碟區。 使用拖放或 Robocopy，或是任何這類的複製工具。 系統會在您執行此工具的相同資料夾中建立日誌 (*jrn*) 檔案。

   如果磁片磁碟機已鎖定，而您需要解除鎖定磁片磁碟機，則根據您的使用案例而定，解除鎖定的步驟可能會有所不同。

   * 如果您已將資料新增至預先加密的磁片磁碟機 (WAImportExport 工具不會用於加密) ，請使用 BitLocker 金鑰 (您在快顯視窗中指定) 的數位密碼來解除鎖定磁片磁碟機。

   * 如果您已將資料新增至 WAImportExport 工具所加密的磁片磁碟機，請使用下列命令將磁片磁碟機解除鎖定：

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. 以系統管理權限開啟 PowerShell 或命令列視窗。 若要將目錄變更為解壓縮的資料夾，請執行下列命令：

    `cd C:\WaImportExportV1`
6. 若要取得磁碟機的 BitLocker 金鑰，請執行下列命令：

    `manage-bde -protectors -get <DriveLetter>:`
7. 若要準備磁碟，請執行下列命令。 **視資料大小而定，這可能需要數小時到數天的時間。**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    日誌檔案會建立在執行此工具的相同資料夾中。 還會建立其他兩個檔案 - .xml** 檔案 (您執行工具的資料夾) 和 drive-manifest.xml** 檔案 (資料所在的資料夾)。

    下表會說明使用的參數：

    |選項  |描述  |
    |---------|---------|
    |/j:     |日誌檔案的名稱 (具有 .jrn 副檔名)。 每個磁碟機都會產生日誌檔案。 我們建議您使用磁碟序號作為日誌檔案名稱。         |
    |/id:     |工作階段識別碼。 針對命令的每個執行個體使用唯一的工作階段號碼。      |
    |/t:     |要寄送之磁碟的磁碟機代號。 例如，磁碟機 `D`。         |
    |/bk:     |磁碟機的 BitLocker 金鑰。 其數字密碼來自 `manage-bde -protectors -get D:` 的輸出      |
    |/srcdir:     |要寄送之磁碟的磁碟機代號，其後緊接著 `:\`。 例如： `D:\`。         |
    |/dstdir:     |Azure 儲存體中目的地容器的名稱。         |
    |/blobtype     |此選項會指定您想要匯入資料的 blob 類型。 針對區塊 blob，這是 `BlockBlob` 分頁 blob 的和 `PageBlob` 。         |
    |/skipwrite：     |此選項表示不需要複製新資料，且即將準備磁碟上的現有資料。          |
    |/enablecontentmd5:     |啟用時，可確保在每個 blob 上計算 MD5 並設定為 `Content-md5` 屬性。 只有當您想要在 `Content-md5` 資料上傳至 Azure 之後使用欄位時，才能使用此選項。 <br> 此選項不會影響預設) 所發生的資料完整性檢查 (。 此設定會增加將資料上傳至雲端所花費的時間。          |
8. 為每個要寄送的磁碟重複上述步驟。 每次執行命令列時，都會使用提供的名稱來建立日誌檔案。

    > [!IMPORTANT]
    > * 與日誌檔案一起，`<Journal file name>_DriveInfo_<Drive serial ID>.xml` 檔案也會建立在工具所在的相同資料夾中。 如果日誌檔案太大，建立作業時會使用 .xml 檔案代替日誌檔案。

## <a name="step-2-create-an-import-job"></a>步驟 2：建立匯入作業

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中執行下列步驟，以建立匯入作業。

1. 登入 https://portal.azure.com/。
2. 移至 [所有服務] > [儲存體] > [匯入/匯出作業]****。

    ![移至匯入/匯出作業](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. 按一下 [ **建立匯入/匯出作業**]。

    ![按一下 [建立匯入/匯出作業]](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. 在 [基本] 中：

   * 選取 [匯入至 Azure]****。
   * 輸入匯入作業的描述性名稱。 使用此名稱來追蹤作業進度。
       * 名稱只可包含小寫字母、數字和連字號。
       * 名稱必須以字母開頭，並且不能包含空格。
   * 選取一個訂用帳戶。
   * 輸入或選取資源群組。  

     ![建立匯入作業 - 步驟 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. 在 [作業詳細資料]**** 中：

   * 上傳在磁碟機準備步驟中取得的磁碟機日誌檔案。 如果已使用 `waimportexport.exe version1`，您需要針對已備妥的每個磁碟機上傳一個檔案。 如果日誌檔案大小超過 2 MB，則您可以使用與日誌檔案一起建立的 `<Journal file name>_DriveInfo_<Drive serial ID>.xml`。
   * 選取將存放資料的目的地儲存體帳戶。
   * 系統會根據所選儲存體帳戶的區域，自動填入置放位置。

   ![建立匯入作業 - 步驟 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. 在 [寄返資訊]**** 中：

   * 從下拉式清單中選取貨運公司。 如果您想要使用 FedEx/DHL 以外的電訊廠商，請從下拉式清單中選擇現有的選項。 請與 `adbops@microsoft.com`  您打算使用的電訊廠商相關資訊，聯絡 Azure 資料箱營運團隊。
   * 輸入您在該貨運公司中建立的有效貨運帳戶號碼。 當匯入作業完成時，Microsoft 會透過此帳戶將磁碟機寄還給您。 如果您沒有帳戶號碼，請建立 [FedEx](https://www.fedex.com/us/oadr/) 或 [DHL](https://www.dhl.com/) 貨運帳戶。
   * 提供完整且有效的連絡人名稱、電話、電子郵件、街道地址、城市、郵遞區號、州/省和國家/地區。

       > [!TIP]
       > 請提供群組電子郵件，而不是指定單一使用者的電子郵件地址。 這樣可以確保即使當系統管理員不在時，您也可以收到通知。

     ![建立匯入工作 - 步驟 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. 在 [摘要]**** 中：

   * 檢閱摘要中提供的作業資訊。 記下作業名稱和 Azure 資料中心寄送地址，以將磁碟寄回 Azure。 這項資訊稍後會用在出貨標籤上。
   * 按一下 [確定]**** 以完成建立匯入作業。

     ![建立匯入作業 - 步驟 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列步驟，在 Azure CLI 中建立匯入作業。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>建立作業

1. 使用 [az extension add](/cli/azure/extension#az_extension_add) 命令新增 [az 匯入匯出](/cli/azure/ext/import-export/import-export) 延伸模組：

    ```azurecli
    az extension add --name import-export
    ```

1. 您可以使用現有的資源群組，或建立一個群組。 若要建立資源群組，請執行 [az group create](/cli/azure/group#az_group_create) 命令：

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. 您可以使用現有的儲存體帳戶或建立一個帳戶。 若要建立儲存體帳戶，請執行 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令：

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. 若要取得您可以寄送磁片的位置清單，請使用 az 匯 [入-匯出位置清單](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) 命令：

    ```azurecli
    az import-export location list
    ```

1. 使用 [az 匯入-匯出位置 show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) 命令來取得您區域的位置：

    ```azurecli
    az import-export location show --location "West US"
    ```

1. 執行下列 [az import-export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) 命令來建立匯入作業：

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > 請提供群組電子郵件，而不是指定單一使用者的電子郵件地址。 這樣可以確保即使當系統管理員不在時，您也可以收到通知。

1. 使用 [az 匯入-匯出清單](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) 命令來查看 myierg 資源群組的所有工作：

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 若要更新您的作業或取消作業，請執行 [az 匯入-匯出更新](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) 命令：

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>步驟 3 (選用) ：設定客戶管理的金鑰

如果您想要使用 Microsoft 管理的金鑰來保護磁片磁碟機的 BitLocker 金鑰，請略過此步驟，並移至下一個步驟。 若要設定您自己的金鑰來保護 BitLocker 金鑰，請依照 Azure 入口網站中的[使用 Azure 匯入/匯出的 Azure Key Vault 設定客戶管理的金鑰](storage-import-export-encryption-key-portal.md)中的指示

## <a name="step-4-ship-the-drives"></a>步驟4：寄送磁片磁碟機

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>步驟5：使用追蹤資訊更新作業

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>步驟6：確認資料上傳至 Azure

追蹤作業到完成為止。 作業完成之後，請確認您的資料已上傳至 Azure。 確認上傳成功之後才刪除內部部署的資料。

## <a name="next-steps"></a>後續步驟

* [檢視作業和磁碟機狀態](storage-import-export-view-drive-status.md)
* [檢閱匯入/匯出的需求](storage-import-export-requirements.md)
