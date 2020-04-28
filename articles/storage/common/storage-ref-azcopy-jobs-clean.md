---
title: azcopy 作業已清除 |Microsoft Docs
description: 本文提供 azcopy 作業 clean 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74033713"
---
# <a name="azcopy-jobs-clean"></a>azcopy 作業清除

移除所有作業的所有記錄檔和計畫檔案

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>選項

**-h、--help**               清除的說明。

**--with-status**字串只會移除具有此狀態的工作、可用的值：已取消、已完成、失敗、InProgress、全部（預設值為 "all"）

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps uint32**     上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
