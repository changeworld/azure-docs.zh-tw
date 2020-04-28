---
title: azcopy 作業顯示 |Microsoft Docs
description: 本文提供 azcopy 作業 show 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034127"
---
# <a name="azcopy-jobs-show"></a>azcopy 作業顯示

顯示給定作業識別碼的詳細資訊。

## <a name="synopsis"></a>概要

如果只有在沒有旗標的情況下提供作業識別碼，則會傳回作業的進度摘要。

當您執行此命令時所顯示的位元組計數和完成百分比，只會反映作業中完成的檔案。 它們不會反映部分完成的檔案。

如果設定`with-status`了旗標，則會顯示作業中具有指定值的傳輸清單。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 show 命令的說明內容。|
|--with-status string|僅列出具有此狀態的作業傳輸，可用的值： [已啟動]、[成功]、[失敗]|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
