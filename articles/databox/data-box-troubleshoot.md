---
title: 針對 Azure 資料箱的問題進行疑難排解，Azure Data Box Heavy
description: 說明如何針對在將資料複製到這些裝置時，Azure 資料箱和 Azure Data Box Heavy 中看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77560060"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>針對與 Azure 資料箱和 Azure Data Box Heavy 相關的問題進行疑難排解

本文詳細說明如何針對使用 Azure 資料箱或 Azure Data Box Heavy 時可能會看到的問題進行疑難排解。 此文章包含將資料複製到資料箱時，或從資料箱上傳資料時所看到的可能錯誤清單。

## <a name="error-classes"></a>錯誤類別

資料箱和 Data Box Heavy 中的錯誤摘要如下：

| 錯誤類別 *        | 說明        | 建議的動作    |
|----------------------------------------------|---------|--------------------------------------|
| 容器或共用名稱 | 容器或共用名稱不會遵循 Azure 命名規則。  |下載錯誤清單。 <br> 重新命名容器或共用。 [深入了解](#container-or-share-name-errors)。  |
| 容器或共用大小限制 | 容器或共用中的資料總計超過 Azure 限制。   |下載錯誤清單。 <br> 減少容器或共用中的整體資料。 [深入了解](#container-or-share-size-limit-errors)。|
| 物件或檔案大小限制 | 容器或共用中的物件超過 Azure 限制。|下載錯誤清單。 <br> 減少容器或共用中的檔案大小。 [深入了解](#object-or-file-size-limit-errors)。 |    
| 資料或檔案類型 | 不支援資料格式或檔案類型。 |下載錯誤清單。 <br> 針對分頁 blob 或受控磁片，請確定資料已對齊 512-位元組，並複製到預先建立的資料夾。 [深入了解](#data-or-file-type-errors)。 |
| 非重大 blob 或檔案錯誤  | Blob 或檔案名並未遵循 Azure 命名規則，或檔案類型不受支援。 | 這些 blob 或檔案可能無法複製，或名稱可能已變更。 [瞭解如何修正這些錯誤](#non-critical-blob-or-file-errors)。 |

\*前四個錯誤類別是嚴重錯誤，必須先修正，才能繼續準備寄送。


## <a name="container-or-share-name-errors"></a>容器或共用名稱錯誤

這些是與容器和共用名稱相關的錯誤。

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**錯誤描述：** 容器或共用名稱必須介於3到63個字元之間。 

**建議的解決方式：** 您已複製資料的資料箱或 Data Box Heavy 共用（SMB/NFS）底下的資料夾，會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更資料箱或 Data Box Heavy 共用底下的資料夾名稱，以確保：

    - 名稱的長度必須介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能包含連續的連字號。
    - 有效名稱的範例： `my-folder-1`、`my-really-extra-long-folder-111`
    - 無效名稱的範例`my-folder_1`：、 `my`、 `--myfolder`、、 `myfolder--``myfolder!`

    如需詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名慣例。


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**錯誤描述：** 容器或共用名稱必須只包含字母、數位或連字號。

**建議的解決方式：** 您已複製資料的資料箱或 Data Box Heavy 共用（SMB/NFS）底下的資料夾，會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更資料箱或 Data Box Heavy 共用底下的資料夾名稱，以確保：

    - 名稱的長度必須介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能包含連續的連字號。
    - 有效名稱的範例： `my-folder-1`、`my-really-extra-long-folder-111`
    - 無效名稱的範例`my-folder_1`：、 `my`、 `--myfolder`、、 `myfolder--``myfolder!`

    如需詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名慣例。

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**錯誤描述：** 容器名稱和共用名稱不能以連字號開頭或結尾，而且不能有連續的連字號。

**建議的解決方式：** 您已複製資料的資料箱或 Data Box Heavy 共用（SMB/NFS）底下的資料夾，會變成儲存體帳戶中的 Azure 容器。 

- 在裝置本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案，以找出有問題的資料夾名稱。
- 變更資料箱或 Data Box Heavy 共用底下的資料夾名稱，以確保：

    - 名稱的長度必須介於3到63個字元之間。
    - 名稱只能包含字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能包含連續的連字號。
    - 有效名稱的範例： `my-folder-1`、`my-really-extra-long-folder-111`
    - 無效名稱的範例`my-folder_1`：、 `my`、 `--myfolder`、、 `myfolder--``myfolder!`

    如需詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名慣例。

## <a name="container-or-share-size-limit-errors"></a>容器或共用大小限制錯誤

這些是與資料超過容器或共用中允許的資料大小相關的錯誤。

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**錯誤描述：** Azure 檔案共用會將共用限制為 5 TB 的資料。 部分共用已超過此限制。

**建議的解決方式：** 在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。

識別錯誤記錄檔中有此問題的資料夾，並確認該資料夾中的檔案低於 5 TB。


## <a name="object-or-file-size-limit-errors"></a>物件或檔案大小限制錯誤

這些是與資料超出 Azure 中所允許的物件或檔案大小上限相關的錯誤。 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**錯誤描述：** 檔案大小超過上傳的檔案大小上限。

**建議的解決方式：** Blob 或檔案大小超過上傳所允許的上限。

- 在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定 blob 和檔案大小未超過 Azure 物件大小限制。

## <a name="data-or-file-type-errors"></a>資料或檔案類型錯誤

這些是在容器或共用中找到不支援的檔案類型或資料類型的相關錯誤。 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**錯誤描述：** Blob 或檔案的對齊方式不正確。

**建議的解決方式：** 資料箱或 Data Box Heavy 上的分頁 blob 共用僅支援512位元組對齊的檔案（例如，VHD/VHDX）。 任何複製到分頁 blob 共用的資料，都會以分頁 blob 的形式上傳至 Azure。

從分頁 blob 共用移除任何非 VHD/VHDX 資料。 您可以針對一般資料使用區塊 blob 或 Azure 檔案共用。

如需詳細資訊，請參閱[分頁 blob 的總覽](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**錯誤描述：** 受控磁片共用中有不支援的檔案類型。 只允許固定的 Vhd。

**建議的解決方式：**

- 請確定您只上傳固定的 Vhd 來建立受控磁片。
- 不支援 VHDX 檔案或**動態**和**差異**vhd。

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**錯誤描述：** 在受控磁片的任何預先存在的資料夾中，都不允許使用目錄。 這些資料夾中只允許固定的 Vhd。

**建議的解決方式：** 針對每個共用中的受控磁片，會建立下列三個資料夾，對應至儲存體帳戶中的容器：進階 SSD、標準 HDD 和標準 SSD。 這些資料夾對應至受控磁片的效能層級。

- 請確定您將分頁 blob 資料（Vhd）複製到其中一個現有的資料夾。
- 在這些現有資料夾中不允許有資料夾或目錄。 移除您在預先存在的資料夾內建立的任何資料夾。

如需詳細資訊，請參閱[複製到受控磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**錯誤描述：** 在 Linux 中不允許符號連結。 

**建議的解決方式：** 符號連結通常是連結、管道和其他這類檔案。 請移除連結，或解析連結並複製資料。


## <a name="non-critical-blob-or-file-errors"></a>非重大 blob 或檔案錯誤

下一節將摘要說明資料複製期間所見到的 blob、檔案或容器的名稱相關所有非嚴重錯誤。 如果這些錯誤存在，則會修改名稱以符合 Azure 命名慣例。 資料上傳的對應訂單狀態將會**完成，但出現警告**。  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**錯誤描述：** Blob 或檔案名包含不支援的控制字元。

**建議的解決方式：** 您複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名慣例。

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**錯誤描述：** Blob 或檔案名包含不合法的字元。

**建議的解決方式：** 您複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名慣例。


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**錯誤描述：** Blob 或檔案名以不正確的字元結尾。

**建議的解決方式：** 您複製的 blob 或檔案包含具有不支援字元的名稱。

在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
移除或重新命名檔案，以移除不支援的字元。

如需詳細資訊，請參閱[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名慣例。


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**錯誤描述：** Blob 或檔案名包含太多路徑區段。

**建議的解決方式：** 您複製的 blob 或檔案超過路徑區段的最大數目。 路徑區段是連續分隔符號之間的字串，例如正斜線/。

- 在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名慣例。

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**錯誤描述：** Blob 或檔案名太長。

**建議的解決方式：** Blob 或檔案名超過長度上限。

- 在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
- Blob 名稱不能超過1024個字元。
- 移除或重新命名 blob 或檔案，讓名稱不超過1024個字元。

如需詳細資訊，請參閱 blob 名稱和檔案名的 Azure 命名慣例。

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**錯誤描述：** Blob 或檔案名中的其中一個區段太長。

**建議的解決方式：** Blob 或檔案名中的其中一個路徑區段超過最大字元數。 路徑區段是連續分隔符號之間的字串，例如正斜線/。

- 在本機 web UI 的 [連線**並複製]** 頁面上，下載並檢查錯誤檔案。
- 請確定[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名慣例。


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**錯誤描述：** 針對受控磁片共用指定了不正確的容器名稱。

**建議的解決方式：** 針對每個共用中的受控磁片，會建立下列資料夾，對應至儲存體帳戶中的容器：進階 SSD、標準 HDD 和標準 SSD。 這些資料夾對應至受控磁片的效能層級。

- 請確定您將分頁 blob 資料（Vhd）複製到其中一個現有的資料夾。 只有來自這些現有容器的資料才會上傳至 Azure。
- 在與進階 SSD、標準 HDD 和標準 SSD 相同層級建立的任何其他資料夾，都不會對應到有效的效能層級，因此無法使用。
- 移除在效能層級外部建立的檔案或資料夾。

如需詳細資訊，請參閱[複製到受控磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>後續步驟

- 深入瞭解[資料箱 Blob 儲存體系統需求](data-box-system-requirements-rest.md)。
