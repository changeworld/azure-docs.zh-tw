---
title: 追蹤和記錄 Azure 資料箱，Azure Data Box Heavy 匯入順序的事件 |Microsoft Docs
description: 描述如何在 Azure 資料箱的各個階段追蹤和記錄事件，以及 Azure Data Box Heavy 匯入順序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147933"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>您 Azure 資料箱和 Azure Data Box Heavy 匯入順序的追蹤和事件記錄

資料箱或 Data Box Heavy 匯入順序會經歷下列步驟：訂購、設定、資料複製、傳回、上傳至 Azure 及驗證，以及資料清除。 對應至順序中的每個步驟，您可以採取多個動作來控制訂單的存取權、審核事件、追蹤順序，以及解讀產生的各種記錄。

下表顯示資料箱或 Data Box Heavy 匯入順序步驟的摘要，以及可在每個步驟期間用來追蹤和審核訂單的工具。

| 資料箱匯入順序階段       | 追蹤和審核的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 建立訂單               | [透過 Azure RBAC 設定訂單的存取控制](#set-up-access-control-on-the-order)                                                    |
| 訂單處理            | [追蹤順序](#track-the-order) <ul><li> Azure 入口網站 </li><li> 貨運承運商網站 </li><li>電子郵件通知</ul> |
| 設定裝置              | 記錄在[活動記錄](#query-activity-logs-during-setup)中的裝置認證存取                                              |
| 將資料複製到裝置        | [查看*error.xml* ](#view-error-log-during-data-copy)資料複製的error.xml檔案                                                             |
| 準備寄送            | [檢查裝置上的 BOM](#inspect-bom-during-prepare-to-ship) 檔案或資訊清單檔案                                      |
| 資料上傳至 Azure       | 在 Azure 資料中心的資料上傳期間[檢查複製記錄](#review-copy-log-during-upload-to-azure)檔中的錯誤                         |
| 從裝置抹除資料   | [查看監管記錄的鏈](#get-chain-of-custody-logs-after-data-erasure) ，包括審核記錄和訂單記錄                |

本文詳細說明可用來追蹤和審核資料箱或 Data Box Heavy 匯入順序的各種機制或工具。 本文中的資訊適用于、資料箱和 Data Box Heavy 匯入訂單。 在後續的章節中，任何對資料箱的參考也適用于 Data Box Heavy。

## <a name="set-up-access-control-on-the-order"></a>設定訂單上的存取控制

您可以在第一次建立訂單時，控制可存取訂單的人員。 在不同範圍設定 Azure 角色，以控制資料箱訂單的存取權。 Azure 角色可決定存取權的類型–讀寫、唯讀、讀寫作業的子集。

可以為 Azure 資料箱服務定義的兩個角色為：

- **資料箱讀取器** -依領域定義的順序 (s) 的唯讀存取權。 他們只能查看訂單的詳細資料。 他們無法存取與儲存體帳戶相關的任何其他詳細資料，也無法編輯訂單詳細資料，例如位址等等。
- **資料箱參與者** -只有 *在已有儲存體帳戶的寫入存取權時*，才能建立將資料傳送到指定儲存體帳戶的訂單。 如果他們沒有儲存體帳戶的存取權，他們甚至無法建立資料箱訂單來將資料複製到帳戶。 此角色不會定義任何儲存體帳戶的相關許可權，也不會授與儲存體帳戶的存取權。  

若要限制訂單的存取權，您可以：

- 指派訂單層級的角色。 使用者只具有角色所定義的許可權，才能與特定的資料箱訂單互動，而不是其他任何專案。
- 在資源群組層級指派角色，使用者即可存取資源群組內的所有資料箱訂單。

如需有關建議使用的 Azure RBAC 的詳細資訊，請參閱 [AZURE rbac 的最佳做法](../role-based-access-control/best-practices.md)。

## <a name="track-the-order"></a>追蹤訂單狀態

您可以透過 Azure 入口網站和貨運公司網站來追蹤您的訂單。 以下是可隨時追蹤資料箱順序的機制：

- 若要在裝置位於 Azure 資料中心或您的內部部署時追蹤訂單，請移至您的 **資料箱訂單，>** Azure 入口網站中的總覽。

    ![視圖順序狀態和追蹤](media/data-box-logs/overview-view-status-1.png)

- 若要在裝置傳輸時追蹤訂單，請移至區域貨運公司網站（例如，美國的 UPS 網站）。 提供與您訂單相關聯的追蹤號碼。
- 資料箱也會根據建立訂單時所提供的電子郵件，在訂單狀態變更時傳送電子郵件通知。 如需所有資料箱訂單狀態的清單，請參閱 [視圖順序狀態](data-box-portal-admin.md#view-order-status)。 若要變更與訂單相關聯的通知設定，請參閱 [編輯通知詳細資料](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安裝期間查詢活動記錄

- 您的資料箱會以鎖定狀態抵達您的內部部署。 您可以使用 Azure 入口網站中提供的裝置認證來取得訂單。  

    當資料箱設定完成時，您可能需要知道誰已存取裝置認證。 若要找出存取 **裝置認證** 分頁的人員，您可以查詢活動記錄。  任何牽涉到存取 **裝置詳細資料 > 認證** 分頁的動作都會登入活動記錄中作為 `ListCredentials` 動作。

    ![查詢活動記錄](media/data-box-logs/query-activity-log-1.png)

- 每次登入資料箱都會即時記錄。 不過，只有在成功完成訂單後，才會在 [審核記錄](#audit-logs) 中提供這項資訊。

## <a name="view-error-log-during-data-copy"></a>在資料複製期間查看錯誤記錄檔

在資料複製到資料箱或 Data Box Heavy 中，如果複製的資料有任何問題，就會產生錯誤檔。

### <a name="errorxml-file"></a>Error.xml 檔案

請確定複製作業已完成，且沒有任何錯誤。 如果複製流程中發生錯誤，請從 [連線並複製]  頁面中下載記錄。

- 如果您複製的檔案與資料箱上的受控磁片資料夾不是512位元組，該檔案就不會以分頁 blob 的形式上傳至您的暫存儲存體帳戶。 您將在記錄中看到錯誤。 移除檔案，並複製 512 位元組規格的檔案。
- 如果您複製了 VHDX、動態 VHD 或差異 VHD (不支援這些檔案) ，您將會在記錄中看到錯誤。

以下是複製到受控磁片時，不同錯誤的 *error.xml* 範例。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

以下是複製到分頁 blob 時，不同錯誤的 *error.xml* 範例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


以下是複製到區塊 blob 時，不同錯誤的 *error.xml* 範例。

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

以下是複製到 Azure 檔案儲存體時，不同錯誤的 *error.xml* 範例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

在上述每個案例中，請先解決錯誤，再繼續進行下一個步驟。 如需透過 SMB 或 NFS 通訊協定在將資料複製到資料箱期間收到之錯誤的詳細資訊，請移至 [疑難排解資料箱和 Data Box Heavy 問題](data-box-troubleshoot.md)。 如需透過 REST 在資料複製到資料箱期間收到之錯誤的相關資訊，請移至針對 [資料箱 Blob 儲存體問題進行疑難排解](data-box-troubleshoot-rest.md)。

## <a name="inspect-bom-during-prepare-to-ship"></a>在準備寄送期間檢查 BOM

準備寄送時，會建立稱為「物料清單」 (BOM) 或資訊清單檔案的檔案清單。

- 您可以使用這個檔案來確認實際的名稱和複製到資料箱的檔案數目。
- 您可以使用這個檔案來確認檔案的實際大小。
- 確認 *crc64* 對應至非零字串。 <!--A null value for crc64 indicates that there was a reparse point error)-->

如需準備寄送時所收到之錯誤的詳細資訊，請移至 [疑難排解資料箱並 Data Box Heavy 問題](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>BOM 或資訊清單檔

BOM 或資訊清單檔案包含複製到資料箱裝置的所有檔案清單。 BOM 檔案具有檔案名和對應的大小以及總和檢查碼。 您可以針對區塊 blob、分頁 blob、Azure 檔案儲存體建立個別的 BOM 檔案，以便透過 REST Api 複製，以及複製到資料箱上的受控磁片。 您可以在準備寄送時，從裝置的本機 web UI 下載 BOM 檔案。

這些檔案也位於資料箱裝置上，並上傳至 Azure 資料中心內的相關聯儲存體帳戶。

### <a name="bom-file-format"></a>BOM 檔案格式

BOM 或資訊清單檔案具有下列一般格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

以下是當資料複製到資料箱上的區塊 blob 共用時，所產生的資訊清單範例。

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM 或資訊清單檔案也會複製到 Azure 儲存體帳戶。 您可以使用 BOM 或資訊清單檔案，確認上傳至 Azure 的檔案符合複製到資料箱的資料。

## <a name="review-copy-log-during-upload-to-azure"></a>在上傳至 Azure 期間檢查複製記錄

在資料上傳至 Azure 期間，會建立複製記錄檔。

### <a name="copy-log"></a>複製記錄檔

針對每個已處理的訂單，資料箱服務會在相關聯的儲存體帳戶中建立複製記錄。 複製記錄檔包含已上傳的檔案總數，以及從資料箱複製資料到 Azure 儲存體帳戶期間錯誤的檔案數目。

在上傳至 Azure 期間，迴圈冗余檢查 (CRC) 計算完成。 從資料複製和資料上傳之後的 CRCs 進行比較。 CRC 不符表示對應的檔案無法上傳。

根據預設，記錄會寫入名為 `copylog` 的容器。 記錄檔會以下列命名慣例儲存：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

複製記錄檔路徑也會顯示在入口網站的 [ **總覽** ] 分頁中。

![完成時複製記錄檔的路徑](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>上傳成功完成 

下列範例會針對已成功完成的資料箱上傳，說明複製記錄檔的一般格式：

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>上傳已完成，但發生錯誤 

上傳至 Azure 可能也會完成，但發生錯誤。

![完成但發生錯誤時，用來複製記錄檔的路徑](media/data-box-logs/copy-log-path-2.png)

以下是複製記錄檔的範例，其中的上傳已完成，但有錯誤：

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>上傳已完成，但出現警告

如果您的資料具有不符合 Azure 命名慣例的容器/blob/檔案名，而且已修改名稱以將資料上傳至 Azure，則上傳至 Azure 即完成，並出現警告。

![完成但出現警告時，用來複製記錄檔的路徑](media/data-box-logs/copy-log-path-3.png)

以下是複製記錄檔的範例，其中不符合 Azure 命名慣例的容器在資料上傳至 Azure 期間已重新命名。

容器的新唯一名稱採用格式 `DataBox-GUID` ，而容器的資料會放在新的重新命名容器中。 複製記錄檔會指定容器的舊的和新的容器名稱。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

以下是複製記錄檔的範例，其中不符合 Azure 命名慣例的 blob 或檔案，會在資料上傳至 Azure 期間重新命名。 新的 blob 或檔案名會轉換成容器之相對路徑的 SHA256 摘要，並根據目的地類型上傳至路徑。 目的地可以是區塊 blob、分頁 blob 或 Azure 檔案儲存體。

`copylog`指定舊的和新的 blob 或檔案名，以及 Azure 中的路徑。

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>在資料抹除之後取得監管記錄鏈

根據 NIST SP 800-88 修訂1指導方針，從資料箱磁片中清除資料之後，可取得監管記錄的鏈。 這些記錄包括 audit 記錄和訂單歷程記錄。 BOM 或資訊清單檔也會連同 audit 記錄一起複製。

### <a name="audit-logs"></a>稽核記錄

Audit 記錄檔包含有關如何開啟和存取資料箱上的共用，或在 Azure 資料中心以外的 Data Box Heavy 的資訊。 這些記錄位於： `storage-account/azuredatabox-chainofcustodylogs`

以下是資料箱中的 audit 記錄範例：

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>下載訂單記錄

訂單記錄可在 Azure 入口網站中取得。 如果訂單已完成，且裝置清除 (磁片) 的資料清除完成，請移至您的裝置訂單，然後流覽至 [ **訂單詳細**資料]。 [下載訂單記錄]**** 選項可供使用。 如需詳細資訊，請參閱 [下載訂單記錄](data-box-portal-admin.md#download-order-history)。

如果您有滾動訂單歷程記錄，您會看到：

- 裝置的貨運公司追蹤資訊。
- 具有 *SecureErase* 活動的事件。 這些事件會對應到磁片上的資料抹除。
- 資料箱記錄連結。 系統會顯示 *audit 記錄*檔、 *複製記錄*檔和 *BOM* 檔案的路徑。

以下是 Azure 入口網站的訂單歷程記錄範例：

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [針對資料箱和 Data Box Heavy 的問題進行疑難排解](data-box-troubleshoot.md)。
