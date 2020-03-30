---
title: 茲抄工作恢復 |微軟文檔
description: 本文為茲複製作業恢復命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034150"
---
# <a name="azcopy-jobs-resume"></a>azcopy 作業繼續

使用給定的工作 ID 恢復現有作業。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|--目標-sas 字串|給定 JobId 的目標的目標 SAS。|
|--排除字串|篩選器：在恢復作業時排除這些失敗的轉移。 檔應以";"分隔。|
|-h, --help|顯示恢復命令的説明內容。|
|--包括字串|篩選器：在恢復作業時，僅包括這些失敗的轉移。 檔應以";"分隔。|
|--源-sas 字串 |給定 JobId 的源 SAS。|

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

|選項|描述|
|---|---|
|--蓋-mbps uint32|以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。|
|--輸出類型字串|命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
