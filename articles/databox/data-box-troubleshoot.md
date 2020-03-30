---
title: Azure 資料盒、Azure 資料框重的疑難排解問題
description: 介紹在將資料複製到這些設備時，如何排除 Azure 資料框和 Azure 資料框中看到的問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560060"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>解決與 Azure 資料框和 Azure 資料框重的問題

本文詳細介紹了如何在使用 Azure 資料框或 Azure 資料框重磅時看到的問題進行故障排除。 本文包括將資料複製到資料框或從資料框上載資料時看到的可能錯誤的清單。

## <a name="error-classes"></a>錯誤類

資料框和資料框重中的錯誤匯總如下：

| 錯誤類別*        | 描述        | 建議的動作    |
|----------------------------------------------|---------|--------------------------------------|
| 容器或共用名稱稱 | 容器或共用名稱稱不遵循 Azure 命名規則。  |下載錯誤清單。 <br> 重命名容器或共用。 [深入了解](#container-or-share-name-errors)。  |
| 容器或共用大小限制 | 容器或共用中的總數據超過 Azure 限制。   |下載錯誤清單。 <br> 減少容器或共用中的總體資料。 [深入了解](#container-or-share-size-limit-errors)。|
| 物件或檔案大小限制 | 容器或共用中的物件或檔超過 Azure 限制。|下載錯誤清單。 <br> 減小容器或共用中的檔案大小。 [深入了解](#object-or-file-size-limit-errors)。 |    
| 資料或檔案類型 | 不支援資料格式或檔案類型。 |下載錯誤清單。 <br> 對於頁面 Blob 或託管磁片，請確保資料是 512 位元組對齊並複製到預先創建的資料夾中。 [深入了解](#data-or-file-type-errors)。 |
| 非關鍵 Blob 或檔錯誤  | blob 或檔案名不遵循 Azure 命名規則，或者不支援檔案類型。 | 這些 Blob 或檔可能無法複製，或者名稱可能已更改。 [瞭解如何修復這些錯誤](#non-critical-blob-or-file-errors)。 |

\*前四個錯誤類別是嚴重錯誤，必須先修復，然後才能繼續準備裝運。


## <a name="container-or-share-name-errors"></a>容器或共用名稱稱錯誤

這些是與容器和共用名稱稱相關的錯誤。

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**錯誤描述：** 容器或共用名稱稱必須介於 3 到 63 個字元之間。 

**建議的解決方案：** 已將資料複製到的資料框或資料盒重共用 （SMB/NFS） 下的資料夾將成為存儲帳戶中的 Azure 容器。 

- 在設備本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔以標識有問題資料夾名稱。
- 更改資料框或資料框"重共用"下的資料夾名稱，以確保：

    - 名稱具有 3 到 63 個字元。
    - 名稱只能有字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能具有連續連字號。
    - 有效名稱的示例： `my-folder-1`，`my-really-extra-long-folder-111`
    - 不正確名稱`my-folder_1`示例： 、 `my`、 `--myfolder`、 、 `myfolder--`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`myfolder!`

    有關詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名約定。


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**錯誤描述：** 容器或共用名稱稱必須僅包含字母、數位或連字號。

**建議的解決方案：** 已將資料複製到的資料框或資料盒重共用 （SMB/NFS） 下的資料夾將成為存儲帳戶中的 Azure 容器。 

- 在設備本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔以標識有問題資料夾名稱。
- 更改資料框或資料框"重共用"下的資料夾名稱，以確保：

    - 名稱具有 3 到 63 個字元。
    - 名稱只能有字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能具有連續連字號。
    - 有效名稱的示例： `my-folder-1`，`my-really-extra-long-folder-111`
    - 不正確名稱`my-folder_1`示例： 、 `my`、 `--myfolder`、 、 `myfolder--`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`myfolder!`

    有關詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名約定。

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**錯誤描述：** 容器名稱和共用名稱稱不能以連字號開頭或結尾，也不能具有連續連字號。

**建議的解決方案：** 已將資料複製到的資料框或資料盒重共用 （SMB/NFS） 下的資料夾將成為存儲帳戶中的 Azure 容器。 

- 在設備本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔以標識有問題資料夾名稱。
- 更改資料框或資料框"重共用"下的資料夾名稱，以確保：

    - 名稱具有 3 到 63 個字元。
    - 名稱只能有字母、數位和連字號。
    - 名稱不能以連字號開頭或結尾。
    - 名稱不能具有連續連字號。
    - 有效名稱的示例： `my-folder-1`，`my-really-extra-long-folder-111`
    - 不正確名稱`my-folder_1`示例： 、 `my`、 `--myfolder`、 、 `myfolder--`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`myfolder!`

    有關詳細資訊，請參閱[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共用名稱稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名約定。

## <a name="container-or-share-size-limit-errors"></a>容器或共用大小限制錯誤

這些錯誤與超出容器或共用中允許的資料大小相關的資料。

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**錯誤描述：** Azure 檔共用將共用限制為 5 TB 資料。 某些股票的此限制已超出。

**建議的解決方案：** 在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。

從錯誤日誌中標識存在此問題的資料夾，並確保該資料夾中的檔低於 5 TB。


## <a name="object-or-file-size-limit-errors"></a>物件或檔案大小限制錯誤

這些錯誤與超出物件的最大大小或 Azure 中允許的檔相關的資料。 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**錯誤描述：** 檔案大小超過上載的最大檔案大小。

**建議的解決方案：** blob 或檔案大小超過允許上載的最大限制。

- 在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
- 確保 blob 和檔案大小不超過 Azure 物件大小限制。

## <a name="data-or-file-type-errors"></a>資料或檔案類型錯誤

這些錯誤與容器或共用中找到的不受支援的檔案類型或資料類型相關。 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**錯誤描述：** Blob 或檔未正確對齊。

**建議的解決方案：** 資料框或資料框粗重上的頁面 blob 共用僅支援對齊 512 位元組的檔（例如 VHD/VHDX）。 複製到頁面 Blob 共用的任何資料都將作為頁面 blob 上載到 Azure。

從頁面 blob 共用中刪除任何非 VHD/VHDX 資料。 可以將共用用於塊 Blob 或 Azure 檔，用於通用資料。

有關詳細資訊，請參閱頁面[blob 的概述](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**錯誤描述：** 託管磁片共用中存在不受支援的檔案類型。 只允許固定 VHD。

**建議的解決方案：**

- 請確保僅上載固定 VHD 以創建託管磁片。
- 不支援 VHDX 檔或**動態**和**不同的**VHD。

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**錯誤描述：** 託管磁片的任何預先存在的資料夾中都不允許使用目錄。 這些資料夾中只允許固定 VHD。

**建議的解決方案：** 對於託管磁片，在每個共用中，將創建與存儲帳戶中的容器對應的以下三個資料夾：高級 SSD、標準硬碟和標準 SSD。 這些資料夾對應于託管磁片的性能層。

- 請確保將頁面 blob 資料 （VHD） 複製到這些現有資料夾中之一。
- 這些現有資料夾中不允許使用資料夾或目錄。 刪除在預先存在的資料夾中創建的任何資料夾。

有關詳細資訊，請參閱[複製到託管磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**錯誤描述：** Linux 中不允許符號連結。 

**建議的解決方案：** 符號連結通常是連結、管道和其他此類檔。 刪除連結，或解析連結並複製資料。


## <a name="non-critical-blob-or-file-errors"></a>非關鍵 Blob 或檔錯誤

下節將概述資料複製期間看到的與 Blob、檔或容器名稱相關的所有非嚴重錯誤。 如果存在這些錯誤，則名稱將被修改以符合 Azure 命名約定。 資料上傳的相應訂單狀態將**完成，並帶有警告**。  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**錯誤描述：** blob 或檔案名包含不支援的控制字元。

**建議的解決方案：** 已複製的 blob 或檔包含具有不支援字元的名稱。

在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
刪除或重命名檔以刪除不支援的字元。

有關詳細資訊，請參閱[Blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名約定。

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**錯誤描述：** blob 或檔案名包含非法字元。

**建議的解決方案：** 已複製的 blob 或檔包含具有不支援字元的名稱。

在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
刪除或重命名檔以刪除不支援的字元。

有關詳細資訊，請參閱[Blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名約定。


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**錯誤描述：** blob 或檔案名以錯誤字元結尾。

**建議的解決方案：** 已複製的 blob 或檔包含具有不支援字元的名稱。

在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
刪除或重命名檔以刪除不支援的字元。

有關詳細資訊，請參閱[Blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名約定。


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**錯誤描述：** blob 或檔案名包含的路徑段太多。

**建議的解決方案：** 複製的 blob 或檔超過路徑段的最大數量。 路徑段是連續分隔符號字元之間的字串，例如，正向斜杠 /。

- 在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
- 確保 blob[名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名約定。

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**錯誤描述：** blob 或檔案名太長。

**建議的解決方案：** blob 或檔案名超過最大長度。

- 在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
- blob 名稱不得超過 1，024 個字元。
- 刪除或重命名 blob 或檔，以便名稱不超過 1024 個字元。

有關詳細資訊，請參閱 Blob 名稱和檔案名的 Azure 命名約定。

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**錯誤描述：** blob 或檔案名中的一個段太長。

**建議的解決方案：** blob 或檔案名中的路徑段之一超過最大字元數。 路徑段是連續分隔符號字元之間的字串，例如，正向斜杠 /。

- 在本地 Web UI 的 **"連接和複製**"頁上，下載並查看錯誤檔。
- 確保 blob[名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[檔案名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名約定。


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**錯誤描述：** 為託管磁片共用指定了不正確的容器名稱。

**建議的解決方案：** 對於託管磁片，在每個共用中，將創建與存儲帳戶中的容器對應的以下資料夾：高級 SSD、標準硬碟和標準 SSD。 這些資料夾對應于託管磁片的性能層。

- 請確保將頁面 blob 資料 （VHD） 複製到這些現有資料夾中之一。 只有來自這些現有容器的資料上載到 Azure。
- 與高級 SSD、標準硬碟和標準 SSD 在同一級別創建的任何其他資料夾都不符合有效的性能層，並且不能使用。
- 刪除在性能層之外創建的檔或資料夾。

有關詳細資訊，請參閱[複製到託管磁片](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>後續步驟

- 瞭解[資料盒 Blob 存儲系統要求](data-box-system-requirements-rest.md)。
