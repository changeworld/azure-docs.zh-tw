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
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878370"
---
# <a name="azcopy-load-clfs"></a>azcopy 載入 CLFS

將本機資料傳輸至容器，並將其儲存在 Microsoft 的 Avere Cloud FileSystem (CLFS) 格式。

## <a name="synopsis"></a>概要

Load 命令會將資料複製到 Azure Blob 儲存體容器中，然後將該資料儲存在 Microsoft 的 Avere Cloud FileSystem (CLFS) 格式。 專屬的 CLFS 格式是由 Azure HPC Cache 和 Avere vFXT for Azure 產品使用。

若要利用此命令，請透過下列方式安裝必要的延伸模組： pip3 install clfsload ~ = 1.0.23。 請確定 CLFSLoad.py 位於您的路徑中。 如需此步驟的詳細資訊，請造訪 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) 。

此命令是一個簡單的選項，可將現有的資料移至雲端儲存體，以搭配特定的 Microsoft 高效能運算快取產品使用。 

因為這些產品使用專屬的雲端檔案系統格式來管理資料，所以無法透過原生複製命令載入該資料。 

相反地，資料必須透過快取產品本身或透過此 load 命令載入，這會使用正確的專屬格式。
此命令可讓您在不使用快取的情況下傳輸資料。 例如，若要預先填入儲存體或將檔案新增至工作集，而不增加快取負載。

目的地是空的 Azure 儲存體容器。 當傳輸完成時，目的地容器可以搭配 Azure HPC Cache 實例或 Avere vFXT for Azure 叢集使用。

> [!NOTE] 
> 這是 load 命令的預覽版本。 請回報 AzCopy Github 存放庫的任何問題。

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

使用 CLFS 格式，將整個目錄載入至具有 SAS 的容器：

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>選項。

**--壓縮類型** 字串指定用於傳輸的壓縮類型。 可用的值為： `DISABLED` 、 `LZ4` 。  (預設 `LZ4`) 

**--**    命令的說明說明 `azcopy load clfs` 。

**--記錄層級** 字串會定義記錄檔的記錄詳細資訊，可用層級如下： `DEBUG` 、 `INFO` 、 `WARNING` 、 `ERROR` 。  (預設 `INFO`) 

**--最大錯誤** uint32 指定容許的最大傳輸失敗數目。 如果發生足夠的錯誤，請立即停止作業。

**--新增-會話**   開始新的工作，而不是繼續進行追蹤資訊所保留的現有工作 `--state-path` 。  (預設值為 true) 

**--preserve-永久連結**    保留永久連結關聯性。

**--狀態-路徑** 字串需要本機目錄的路徑，才能追蹤作業狀態。 路徑必須指向現有的目錄，才能繼續工作。 對於新的作業，它必須是空的。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps float|以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。|
|--output 類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--受信任-microsoft-尾碼字串   | 指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
