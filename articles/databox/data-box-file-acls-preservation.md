---
title: 使用 Azure 資料箱保留檔案 Acl、屬性和時間戳記
description: 透過 SMB 將資料複製到 Azure 資料箱時保留的 Acl、時間戳記和屬性。 使用 Windows 和 Linux data copy 工具複製中繼資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: alkohli
ms.openlocfilehash: 74b8bfcd8cfedaa7c5e24b6c29d9229a4db5828a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450700"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>使用 Azure 資料箱保留檔案 Acl、屬性和時間戳記

Azure 資料箱可讓您在將資料傳送至 Azure 時，保留 (Acl) 、時間戳記和檔案屬性的存取控制清單。 本文說明當您透過伺服器訊息區 (SMB) 將資料複製到資料箱，以將其上傳到 Azure 檔案儲存體時，可傳送的中繼資料。 提供使用 Windows 和 Linux data copy 工具複製中繼資料的特定步驟。

在本文中，傳輸的 Acl、時間戳記和檔案屬性統稱為 *中繼資料*。

## <a name="transferred-metadata"></a>傳送的中繼資料

當資料箱中的資料上傳至 Azure 檔案儲存體時，會傳送下列中繼資料。

#### <a name="timestamps"></a>時間戳記

以下是傳輸的時間戳記：
- CreationTime
- LastWriteTime

下列時間戳記未傳送：
- LastAccessTime
  
#### <a name="file-attributes"></a>檔案屬性

除非另有說明，否則會傳送檔案和目錄上的檔案屬性。

傳送下列檔案屬性：
- FILE_ATTRIBUTE_READONLY 只 (檔案) 
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- 僅 FILE_ATTRIBUTE_DIRECTORY (DIRECTORY) 
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY 只 (檔案) 
- FILE_ATTRIBUTE_NO_SCRUB_DATA

下列檔案屬性不會傳送：
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
不會傳送目錄的唯讀屬性。

#### <a name="acls"></a>ACL

系統會複製並傳輸您透過 SMB 複製到資料箱的目錄和檔案的所有 Acl。 傳輸包含 (Dacl) 的任意 Acl，以及 (Sacl) 的系統 Acl。 針對 Linux，只會傳輸 Windows NT Acl。

在透過網路檔案系統進行資料複製時，不會傳送 Acl (NTS) 以及當您使用資料複製服務來傳輸資料時。 資料複製服務會直接從您的共用讀取資料，而且無法讀取 Acl。

即使您的資料複製工具不會複製 Acl，目錄和檔案上的預設 Acl 仍會傳送至 Azure 檔案儲存體。 預設 Acl 具有內建的系統管理員帳戶、系統帳戶，以及用來在資料箱中掛接和複製資料的 SMB 共用使用者帳戶的許可權。

Acl 包含具有下列屬性的安全描述項： Acl、擁有者、群組、SACL。

Acl 預設會啟用傳輸。 您可能會想要在資料箱的本機 web UI 中停用這項設定。 如需詳細資訊，請參閱 [使用本機 WEB UI 來管理您的資料箱及 Data Box Heavy](./data-box-local-web-ui-admin.md)。

> [!NOTE]
> 包含包含條件式存取控制專案之 Acl 的檔案不會複製 (ACE) 字串。 這是已知問題。 若要解決此問題，請掛接共用，然後使用支援複製 Acl 的複製工具，以手動方式將這些檔案複製到 Azure 檔案儲存體共用。

## <a name="copying-data-and-metadata"></a>複製資料和中繼資料

若要為您的資料傳輸 Acl、時間戳記和屬性，請使用下列程式，將資料複製到資料箱中。 

### <a name="windows-data-copy-tool"></a>Windows 資料複製工具

若要透過 SMB 將資料複製到資料箱，請使用 SMB 相容的檔案複製工具（例如） `robocopy` 。 下列範例命令會複製所有檔案和目錄，並連同資料一起傳送中繼資料。

使用 `/copyall` 或選項時 `/dcopy:DAT` ，請確定所需的備份操作員許可權未停用。 如需詳細資訊，請參閱 [使用本機 WEB UI 來管理您的資料箱及 Data Box Heavy](./data-box-local-web-ui-admin.md)。 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

其中

|選項 |描述 |
|------------------- | ----- |
|`/copyall` |複製所有屬性。|
|`/e`      |複製子目錄，包括空白目錄。         |
|`/dcopy:DAT`  |複製資料、屬性和時間戳記。 注意：/dcopy： DAT 選項必須用來 `CreationTime` 在目錄上傳輸。 |
|`/r:3`    |針對失敗的複本指定3次重試。         |
|`/w:60`   |指定重試之間的等候時間60秒。         |
|`/is`     |包含相同的檔案。         |
|`/nfl`    |不記錄檔案名。         |
|`/ndl`    |不會記錄目錄名稱。        |
|`/np`     |不會顯示覆製作業的進度。         |
|`/MT:32 or 64`  |使用多執行緒，搭配32或64執行緒。           |
|`/fft`    |減少任何檔案系統的時間戳記細微性。        |
|`/log+:<LogFile>`  |將輸出附加至現有的記錄檔。|

如需這些參數的詳細資訊 `robocopy` ，請參閱 [教學課程：透過 SMB 將資料複製到 Azure 資料箱](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Linux 資料複製工具

在 Linux 中傳輸中繼資料是兩個步驟的程式。 首先，您會使用不復制中繼資料的工具（例如）複製來源資料 `rsync` 。 複製資料之後，您可以使用或之類的工具來複製中繼資料 `smbcacls` `cifsacl` 。 

下列範例命令會執行第一個步驟，使用複製資料 `rsync` 。 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>後續步驟

- [透過 SMB 將資料複製到 Azure 資料箱](./data-box-deploy-copy-data.md)