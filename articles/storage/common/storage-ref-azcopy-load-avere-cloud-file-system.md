---
title: azcopy load clfs |Microsoft Docs
titleSuffix: Azure Storage
description: 本文提供 azcopy load clfs 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8f0a8903f90b134c35d9adb35a493d989d414b56
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294133"
---
# <a name="azcopy-load-clfs"></a>azcopy 載入 CLFS

將本機資料傳輸到容器中，並將其儲存為 Microsoft 的 Avere Cloud FileSystem （CLFS）格式。

## <a name="synopsis"></a>概要

Load 命令會將資料複製到 Azure Blob 儲存體容器中，然後將該資料儲存為 Microsoft 的 Avere Cloud FileSystem （CLFS）格式。 Azure HPC 快取和 Avere vFXT for Azure 產品會使用專屬的 CLFS 格式。

若要利用此命令，請透過下列方式安裝必要的延伸模組： pip3 install clfsload ~ = 1.0.23。 請確定 CLFSLoad.py 位於您的路徑中。 如需此步驟的詳細資訊，請造訪 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) 。

此命令是將現有資料移至雲端儲存體以用於特定 Microsoft 高效能計算快取產品的簡單選項。 

因為這些產品使用專屬的雲端檔案系統格式來管理資料，所以無法透過原生複製命令載入該資料。 

相反地，您必須透過快取產品本身或使用正確專屬格式的 load 命令來載入資料。
此命令可讓您在不使用快取的情況下傳輸資料。 例如，預先填入儲存區，或將檔案新增至工作集，而不需要增加快取載入。

目的地是空的 Azure 儲存體容器。 當傳送完成時，目的地容器可以與 Azure HPC 快取實例或 Avere vFXT for Azure 叢集搭配使用。

> [!NOTE] 
> 這是 load 命令的預覽版本。 請在 AzCopy Github 存放庫中報告任何問題。

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

將整個目錄載入具有 CLFS 格式之 SAS 的容器：

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>選項。

**--compression-類型**字串指定要用於傳輸的壓縮類型。 可用的值為： `DISABLED` 、 `LZ4` 。 （預設值 `LZ4` ）

**--** 命令的說明協助 `azcopy load clfs` 。

**--記錄層級**字串會定義記錄檔的記錄詳細資訊，可用的層級為： `DEBUG` 、 `INFO` 、 `WARNING` 、 `ERROR` 。 （預設值 `INFO` ）

**--最大-錯誤**uint32 指定要容忍的傳輸失敗數目上限。 如果發生足夠的錯誤，請立即停止作業。

**--新增-會話**  啟動新的工作，而不要繼續現有的作業，其追蹤資訊會保留在 `--state-path` 。 （預設值為 true）

**--preserve-永久連結**   保留永久連結關聯性。

**--狀態-路徑**字串需要本機目錄的路徑，以進行作業狀態追蹤。 路徑必須指向現有的目錄，才能繼續作業。 對於新作業，它必須是空的。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps 浮點數|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   | 指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
