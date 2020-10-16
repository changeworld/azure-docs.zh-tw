---
title: 針對 Azure 資料箱、Azure Data Box Heavy 的問題進行疑難排解
description: 說明如何針對在將資料複製到這些裝置時，Azure 資料箱和 Azure Data Box Heavy 中所看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124669"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>針對與 Azure 資料箱和 Azure Data Box Heavy 相關的問題進行疑難排解

本文將詳細說明如何針對使用 Azure 資料箱或 Azure Data Box Heavy 匯入訂單時可能看到的問題進行疑難排解。 本文包含當資料複製到資料箱時，或從資料箱上傳匯入順序的資料時，可能出現的錯誤清單。

本文中的資訊不適用於為數據箱建立的匯出訂單。

## <a name="error-classes"></a>錯誤類別

資料箱和 Data Box Heavy 中的錯誤摘要如下：

| 錯誤類別 *        | 描述        | 建議的動作    |
|----------------------------------------------|---------|--------------------------------------|
| 容器或共用名稱 | 容器或共用名稱未遵循 Azure 命名規則。  |下載錯誤清單。 <br> 重新命名容器或共用。 [深入了解](#container-or-share-name-errors)。  |
| 容器或共用大小限制 | 容器或共用中的資料總計超過 Azure 限制。   |下載錯誤清單。 <br> 減少容器或共用中的整體資料。 [深入了解](#container-or-share-size-limit-errors)。|
| 物件或檔案大小限制 | 容器或共用中的物件超過 Azure 限制。|下載錯誤清單。 <br> 減少容器或共用中的檔案大小。 [深入了解](#object-or-file-size-limit-errors)。 |    
| 資料或檔案類型 | 不支援資料格式或檔案類型。 |下載錯誤清單。 <br> 針對分頁 blob 或受控磁片，請確定資料是 512-位元組對齊，並複製到預先建立的資料夾。 [深入了解](#data-or-file-type-errors)。 |
| 非重大 blob 或檔案錯誤  | Blob 或檔案名未遵循 Azure 命名規則，或檔案類型不受支援。 | 可能不會複製這些 blob 或檔案，或可能會變更這些名稱。 [瞭解如何修正這些錯誤](#non-critical-blob-or-file-errors)。 |

\* 前四個錯誤類別是嚴重的錯誤，必須先修正，才能繼續準備寄送。


## <a name="container-or-share-name-errors"></a>容器或共用名稱錯誤

這些是與容器和共用名稱相關的錯誤。

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**錯誤描述：** 容器或共用名稱必須介於3到63個字元之間。 

**建議的解決方式：** 資料箱或 Data Box Heavy 共用下的資料夾 (SMB/NFS) 您複製的資料會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更 Data Box 或 Data Box Heavy 共用下的資料夾名稱，以確定：

    - 名稱的長度介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1` 、 `my-really-extra-long-folder-111`
    - 無效名稱的範例： `my-folder_1` 、 `my` 、 `--myfolder` 、 `myfolder--` 、 `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例的 [容器名稱](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 和 [共用名稱](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**錯誤描述：** 容器或共用名稱必須僅包含字母、數位或連字號。

**建議的解決方式：** 資料箱或 Data Box Heavy 共用下的資料夾 (SMB/NFS) 您複製的資料會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更 Data Box 或 Data Box Heavy 共用下的資料夾名稱，以確定：

    - 名稱的長度介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1` 、 `my-really-extra-long-folder-111`
    - 無效名稱的範例： `my-folder_1` 、 `my` 、 `--myfolder` 、 `myfolder--` 、 `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例的 [容器名稱](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 和 [共用名稱](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**錯誤描述：** 容器名稱和共用名稱不能以連字號開頭或結尾，也不能有連續的連字號。

**建議的解決方式：** 資料箱或 Data Box Heavy 共用下的資料夾 (SMB/NFS) 您複製的資料會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更 Data Box 或 Data Box Heavy 共用下的資料夾名稱，以確定：

    - 名稱的長度介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1` 、 `my-really-extra-long-folder-111`
    - 無效名稱的範例： `my-folder_1` 、 `my` 、 `--myfolder` 、 `myfolder--` 、 `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例的 [容器名稱](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 和 [共用名稱](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**錯誤描述**：目錄或容器名稱包含不合法的字元。

**建議的解決**方式：您複製的目錄或容器名稱包含不支援的字元。

- 在本機 web UI 的 [連線並複製] 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。 
- 重新命名目錄或容器，以確保它們符合 Azure 命名慣例的規範。

如需詳細資訊，請參閱 [目錄](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)   和[容器](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)的 Azure 命名慣例。

## <a name="container-or-share-size-limit-errors"></a>容器或共用大小限制錯誤

這些是資料超過容器或共用中允許的資料大小的相關錯誤。

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**錯誤描述：** Azure 檔案共用會將共用限制為 5 TiB 的資料，且不會在儲存體帳戶上啟用大型檔案共用。 某些共用已超過此限制。

**建議的解決方式：** 在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。

- 從錯誤記錄檔中找出具有此問題的資料夾，並確定該資料夾中的檔案是在 5 TiB 以下。
- 5 TiB 限制不適用於允許大型檔案共用的儲存體帳戶。 但是，當您放置訂單時，必須設定大型檔案共用。 
  - 聯絡 [Microsoft 支援服務](data-box-disk-contact-microsoft-support.md) 並要求新的出貨標籤。
  - [在儲存體帳戶上啟用大型檔案共用。](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [展開儲存體帳戶中的檔案共用](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) ，並將配額設定為 100 TiB。
  
  
## <a name="object-or-file-size-limit-errors"></a>物件或檔案大小限制錯誤

這些是與超出物件大小上限或在 Azure 中允許的檔案相關的錯誤。 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**錯誤描述：** 檔案大小超過上傳的檔案大小上限。

**建議的解決方式：** Blob 或檔案大小超過上傳允許的最大限制。

- 在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定 blob 和檔案大小不超過 Azure 物件大小限制。

## <a name="data-or-file-type-errors"></a>資料或檔案類型錯誤

這些是與不支援的檔案類型或容器或共用中的資料類型相關的錯誤。 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**錯誤描述：** Blob 或檔案的對齊方式不正確。

**建議的解決方式：** 資料箱上的分頁 blob 共用或 Data Box Heavy 僅支援512位元組 (對齊的檔案，例如 VHD/VHDX) 。 任何複製到分頁 blob 共用的資料，都會以分頁 blob 的形式上傳至 Azure。

從分頁 blob 共用移除任何非 VHD/VHDX 資料。 您可以使用區塊 blob 的共用或一般資料的 Azure 檔案。

如需詳細資訊，請參閱 [分頁 blob 的總覽](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**錯誤描述：** 受控磁片共用中有不支援的檔案類型。 只允許固定的 Vhd。

**建議的解決方式：**

- 請確定您只上傳固定的 Vhd 以建立受控磁片。
- 不支援 VHDX 檔案或 **動態** 和 **差異** vhd。

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**錯誤描述：** 受控磁片的任何預先存在的資料夾中都不允許有目錄。 這些資料夾中只允許固定的 Vhd。

**建議的解決方式：** 針對受控磁片，在每個共用內，會建立下列三個資料夾，對應至儲存體帳戶中的容器：進階 SSD、標準 HDD 和標準 SSD。 這些資料夾會對應到受控磁片的效能層級。

- 請務必將分頁 blob 資料 (Vhd) 複製到其中一個現有的資料夾。
- 這些現有的資料夾中不允許有資料夾或目錄。 移除您在預先存在的資料夾內建立的任何資料夾。

如需詳細資訊，請參閱 [複製到受控磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**錯誤描述：** Linux 中不允許符號連結。 

**建議的解決方式：** 符號連結通常是連結、管道和其他這類檔案。 請移除連結，或解析連結並複製資料。


## <a name="non-critical-blob-or-file-errors"></a>非重大 blob 或檔案錯誤

下一節將摘要說明在資料複製期間看到的所有與 blob、檔案或容器名稱相關的非重大錯誤。 如果出現這些錯誤，則會修改名稱以符合 Azure 命名慣例。 資料上傳的對應訂單狀態將會 **完成，但出現警告**。  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**錯誤描述：** Blob 或檔案名包含不支援的控制字元。

**建議的解決方式：** 您所複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱 Azure [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 和 [檔案名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的命名慣例。

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**錯誤描述：** Blob 或檔案名包含不合法的字元。

**建議的解決方式：** 您所複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱 Azure [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 和 [檔案名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的命名慣例。


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**錯誤描述：** Blob 或檔案名結尾為不正確的字元。

**建議的解決方式：** 您所複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱 Azure [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 和 [檔案名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的命名慣例。


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**錯誤描述：** Blob 或檔案名包含太多路徑區段。

**建議的解決方式：** 您複製的 blob 或檔案超過路徑區段的最大數目。 路徑區段是連續分隔符號之間的字串，例如正斜線/。

- 在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定 [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 和 [檔案名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) 符合 Azure 命名慣例。

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**錯誤描述：** Blob 或檔案名太長。

**建議的解決方式：** Blob 或檔案名超過長度上限。

- 在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
- Blob 名稱不能超過1024個字元。
- 移除或重新命名 blob 或檔案，使名稱不超過1024個字元。

如需詳細資訊，請參閱 Azure blob 名稱和檔案名的命名慣例。

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**錯誤描述：** Blob 或檔案名中的其中一個區段太長。

**建議的解決方式：** Blob 或檔案名中的其中一個路徑區段超過最大字元數。 路徑區段是連續分隔符號之間的字串，例如正斜線/。

- 在本機 web UI 的 [連線 **並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定 [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 和 [檔案名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) 符合 Azure 命名慣例。


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**錯誤描述：** 針對受控磁片共用指定了不適當的容器名稱。

**建議的解決方式：** 針對受控磁片，在每個共用內，會建立下列資料夾，對應至儲存體帳戶中的容器：進階 SSD、標準 HDD 和標準 SSD。 這些資料夾會對應到受控磁片的效能層級。

- 請務必將分頁 blob 資料 (Vhd) 複製到其中一個現有的資料夾。 只有來自這些現有容器的資料會上傳至 Azure。
- 在進階 SSD、標準 HDD 和標準 SSD 相同層級建立的任何其他資料夾都不會對應到有效的效能層級，因此無法使用。
- 移除在效能層以外建立的檔案或資料夾。

如需詳細資訊，請參閱 [複製到受控磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>下一步

- 瞭解 [資料箱 Blob 儲存體系統需求](data-box-system-requirements-rest.md)。