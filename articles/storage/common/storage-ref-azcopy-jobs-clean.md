---
title: 副本作業清潔 |微軟文檔
description: 本文為 azcopy 作業乾淨命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033713"
---
# <a name="azcopy-jobs-clean"></a>azcopy 作業清除

刪除所有作業的所有日誌和計畫檔

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>選項。

**-h， -- 説明**               説明清潔。

**--帶狀態**字串 僅刪除具有此狀態的作業，可用值：已取消、已完成、失敗、進度、全部（預設"全部"）

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

**--蓋-mbps uint32**     以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。

**--輸出類型**字串 命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。 （預設"文本"）

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
