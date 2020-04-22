---
title: 追蹤並記錄 Azure 資料框、Azure 資料框重事件*微軟文件
description: 介紹如何在 Azure 數據框和 Azure 數據框重序的各個階段跟蹤和記錄事件。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676417"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure 資料框與 Azure 資料框重的追蹤與事件紀錄記錄

數據框或數據框重訂單通過以下步驟:順序、設置、數據複製、返回、上載到 Azure 並驗證以及數據擦除。 對應於順序中的每個步驟,您可以採取多個操作來控制對訂單的訪問、審核事件、跟蹤順序以及解釋生成的各種日誌。

下表顯示了數據框或數據箱重訂單步驟的摘要,以及可用於跟蹤和審核每個步驟中訂單的工具。

| 資料盒訂單階段       | 追蹤並追蹤並檢視的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 建立訂單               | [透過 RBAC 對訂單設定存取控制](#set-up-access-control-on-the-order)                                                    |
| 已處理的訂單            | [追蹤訂單](#track-the-order)通過 <ul><li> Azure 入口網站 </li><li> 航運承運人網站 </li><li>電子郵件通知</ul> |
| 設定裝置              | 裝置認證在[活動紀錄](#query-activity-logs-during-setup)中                                              |
| 資料複製到裝置        | [檢視*錯誤.xml*檔案](#view-error-log-during-data-copy),進行資料複製                                                             |
| 準備寄送            | [檢查裝置上的物料清單檔案](#inspect-bom-during-prepare-to-ship)或清單檔案                                      |
| 資料上載到 Azure       | 在 Azure 資料中心的資料上載過程中[檢視複本紀錄](#review-copy-log-during-upload-to-azure)中是否存在錯誤                         |
| 從裝置清除資料   | [檢視託管紀錄鍊,](#get-chain-of-custody-logs-after-data-erasure)包括稽核紀錄及訂單歷史記錄                |

本文詳細介紹了可用於跟蹤和審核數據框或數據箱重序的各種機制或工具。 本文中的資訊適用於數據框和數據框重。 在後續部分中,對數據框的任何引用也適用於數據框重。

## <a name="set-up-access-control-on-the-order"></a>在訂單上設定存取控制

您可以控制誰可以存取您的訂單時,第一次創建訂單。 在各種作用網域設置基於角色的存取控制 (RBAC) 角色,以控制對數據框順序的訪問。 RBAC 角色確定對操作子集的存取類型 -讀寫、唯讀、讀寫。

可以為 Azure 資料框服務定義的兩個角色是:

- **數據框讀取器**─具有對範圍定義的訂單的唯讀存取權限。 他們只能查看訂單的詳細資訊。 他們無法訪問與存儲帳戶相關的任何其他詳細資訊,也不能編輯訂單詳細資訊,如位址等。
- **資料框參與者**─ 只能建立一個訂單,將資料傳輸到給定的儲存帳戶 *,如果他們已經擁有儲存帳戶的寫入存取權限*。 如果他們無法存取儲存帳戶,他們甚至不能創建數據盒訂單以將資料複製到帳戶。 此角色不定義任何與存儲帳戶相關的許可權,也不授予對存儲帳戶的訪問許可權。  

要限制對訂單的存取,您可以:

- 在訂單級別分配角色。 使用者僅具有角色定義的這些許可權,以便僅與該特定數據框順序進行交互,而沒有其他許可權。
- 在資源組級別分配角色,用戶可以訪問資源組中的所有數據框訂單。

有關建議的 RBAC 使用的詳細資訊,請參閱[Azure RBAC 的最佳做法](../role-based-access-control/best-practices.md)。

## <a name="track-the-order"></a>追蹤訂單狀態

您可以通過 Azure 門戶和裝運承運人網站跟蹤訂單。 已建立以下機制,可隨時追蹤資料框順序:

- 要追蹤裝置在 Azure 資料中心或本地中時的順序,請轉到 Azure 門戶中**的資料盒順序>概述**。

    ![檢視訂單狀態與追蹤否](media/data-box-logs/overview-view-status-1.png)

- 要在設備傳輸過程中跟蹤訂單,請造訪區域運營商網站,例如,美國的 UPS 網站。 提供與您的訂單關聯的跟蹤號碼。
- Data Box 還會根據訂單創建時提供的電子郵件,隨時發送訂單狀態更改的電子郵件通知。 有關所有資料盒訂單狀態的清單,請參閱[檢視訂單狀態](data-box-portal-admin.md#view-order-status)。 要變更與訂單關聯的通知設定,請參閱[編輯通知詳細資訊](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>設定期間的查詢活動紀錄

- 您的數據框以鎖定狀態到達您的場所。 您可以使用 Azure 門戶中可用的設備認證來進行訂單。  

    設置數據框后,您可能需要知道誰都訪問了設備憑據。 要確定誰訪問了**設備認證列**列列選項卡,可以查詢活動日誌。  涉及訪問**設備詳細資訊>憑據**邊欄選項卡的任何操作`ListCredentials`都作為 操作記錄到活動日誌中。

    ![查詢活動記錄](media/data-box-logs/query-activity-log-1.png)

- 數據框中的每個符號都會即時記錄。 但是,此資訊僅在訂單成功完成後在[審核日誌](#audit-logs)中可用。

## <a name="view-error-log-during-data-copy"></a>在資料複製期間檢視錯誤紀錄

在將資料複製到資料框或數據框重時,如果複製的數據存在任何問題,將生成錯誤檔。

### <a name="errorxml-file"></a>錯誤.xml 檔案

確保複製作業已完成,沒有錯誤。 如果複製流程中發生錯誤，請從 [連線並複製]**** 頁面中下載記錄。

- 如果複製的檔案不是與數據盒上的託管磁碟資料夾對齊的 512 位元組,則該檔不會以頁面 blob 方式上傳到暫存儲存帳戶。 您將在記錄中看到錯誤。 移除檔案，並複製 512 位元組規格的檔案。
- 如果複製了 VHDX 或動態 VHD 或不同的 VHD(不支援這些檔),則日誌中將看到錯誤。

下面是*錯誤.xml*在複製到託管磁碟時出現不同錯誤的範例。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

下面是*錯誤.xml*在複製到頁面 blob 時出現不同錯誤的範例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


下面是*錯誤.xml*在複製到阻止 Blob 時出現不同錯誤的範例。

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

下面是*錯誤.xml*在複製到 Azure 檔時出現不同錯誤的範例。

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

在上述每種情況下,在繼續下一步之前解決錯誤。 有關透過 SMB 或 NFS 協定將資料複製到資料盒期間收到的錯誤的詳細資訊,請轉到[「疑難排解資料盒」 和「資料箱」 嚴重問題](data-box-troubleshoot.md)。 有關通過 REST 將資料拷貝到數據盒期間收到的錯誤的資訊,請轉到[「數據框 Blob 存儲問題疑難解答](data-box-troubleshoot-rest.md)」。。

## <a name="inspect-bom-during-prepare-to-ship"></a>準備裝運期間檢查物料清單

在準備出貨期間,將創建稱為物料清單 (BOM) 或清單檔的檔案清單。

- 使用此檔可以驗證實際名稱和複製到資料框的檔案數。
- 使用此檔可以根據文件的實際大小進行驗證。
- 驗證*crc64*是否對應於非零字串。 <!--A null value for crc64 indicates that there was a reparse point error)-->

有關準備出貨期間收到的錯誤的詳細資訊,請轉到[疑難排解資料框和資料框重問題](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>物料清單或清單檔案

物料清單或清單檔包含複製到數據盒設備的所有檔案的清單。 物料清單檔具有檔名和相應的大小以及校驗和。 為區塊 Blob、頁面 Blob、Azure 檔案、透過 REST API 複製以及資料框中的託管磁碟創建單獨的 BOM 檔。 您可以在準備出貨期間從裝置的本地 Web UI 下載 BOM 檔案。

這些檔也駐留在數據盒設備上,並上載到 Azure 資料中心中的關聯存儲帳戶。

### <a name="bom-file-format"></a>物料清單檔案格式

物料清單或清單檔具有以下通用格式:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

下面是將數據複製到數據框中塊 Blob 共享時生成的清單的範例。

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

物料清單或清單檔也會複製到 Azure 儲存帳戶。 可以使用物料清單或清單檔來驗證上載到 Azure 的檔與複製到數據框的數據匹配。

## <a name="review-copy-log-during-upload-to-azure"></a>在上載到 Azure 期間檢視複本紀錄

在將數據上載到 Azure 期間,將創建一個副本日誌。

### <a name="copy-log"></a>複製紀錄

對於處理的每個訂單,數據盒服務在關聯的存儲帳戶中創建複製日誌。 複製日誌具有上載的檔總數,以及將數據從資料框複製到 Azure 儲存帳戶的數據副本期間出錯的檔數。

循環冗餘檢查 (CRC) 計算在上載到 Azure 期間完成。 比較資料複本和資料上載後的 CRC。 CRC 不匹配表示相應的檔無法上載。

根據預設，記錄會寫入名為 `copylog` 的容器。 紀錄隨以下命名慣例儲存:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

複製日誌路徑也顯示在門戶的 **「概述」** 邊欄選項卡上。

![完成後在「概述」邊欄選項卡中複製日誌的路徑](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>上傳成功完成 

以下範例介紹成功完成的資料盒上載的副本日誌的一般格式:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>上傳已完成錯誤 

上載到 Azure 也可能包含錯誤。

![在錯誤完成後,在「概述」邊欄選項卡中複製日誌的路徑](media/data-box-logs/copy-log-path-2.png)

下面是複製日誌的範例,其中上載已完成錯誤:

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
### <a name="upload-completed-with-warnings"></a>上傳已完成警告

如果資料具有不符合 Azure 命名約定的容器/blob/檔名,並且這些名稱已修改以將數據上載到 Azure,則上傳到 Azure 會完成警告。

![在使用警告完成後,在「概述」邊欄選項卡中複製日誌的路徑](media/data-box-logs/copy-log-path-3.png)

下面是一個複製日誌示例,其中不符合 Azure 命名約定的容器在將數據上載到 Azure 期間重命名。

容器的新唯一名稱採用格式`DataBox-GUID`,容器的數據將放入新的重命名的容器中。 複製紀錄指定容器的舊容器名稱和新容器名稱。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

下面是一個複製日誌示例,其中不符合 Azure 命名約定的 Blob 或檔在將數據上載到 Azure 期間重命名。 新的 blob 或檔案名將轉換為 SHA256 摘要的相對路徑到容器,並根據目標類型上傳到路徑。 目標可以是塊 Blob、頁面 Blob 或 Azure 檔。

指定`copylog`舊和新的 Blob 或檔名以及 Azure 中的路徑。

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>資料清除後取得保管紀錄鍊

根據 NIST SP 800-88 修訂版 1 準則從數據框磁盤中刪除數據后,可以使用保管日誌鏈。 這些日誌包括審核日誌和訂單歷史記錄。 BOM 或清單檔也會隨審核日誌一起複製。

### <a name="audit-logs"></a>稽核記錄

審核日誌包含有關如何在 Azure 資料中心外部打開和訪問數據框或數據框重數據盒上的共享的資訊。 這些紀錄位於:`storage-account/azuredatabox-chainofcustodylogs`

下面是資料框中的審核紀錄範例:

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

訂單歷史記錄在 Azure 門戶中可用。 如果訂單已完成,並且設備清理(從磁碟擦除資料)已完成,則轉到裝置訂單並導航到**訂單詳細資訊**。 [下載訂單記錄]**** 選項可供使用。 有關詳細資訊,請參閱[下載訂單歷史記錄](data-box-portal-admin.md#download-order-history)。

如果捲動瀏覽訂單歷史記錄,您將看到:

- 設備的運營商跟蹤資訊。
- 具有*安全擦除*活動的事件。 這些事件對應於磁碟上數據的擦除。
- 數據框日誌連結。 將顯示*審核紀錄*、*複製紀錄*和*BOM*檔的路徑。

下面是 Azure 門戶的訂單歷史記錄日誌的範例:

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

- 瞭解如何[解決資料盒與資料盒重的問題](data-box-troubleshoot.md)。
