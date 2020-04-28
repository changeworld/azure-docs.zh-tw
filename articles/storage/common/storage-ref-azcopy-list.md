---
title: azcopy 清單 |Microsoft Docs
description: 本文提供 azcopy list 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034100"
---
# <a name="azcopy-list"></a>azcopy 清單

列出指定資源中的實體。

## <a name="synopsis"></a>概要

目前的版本僅支援 Blob 容器。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 list 命令的說明內容。|
|--電腦可讀取|列出檔案大小（以位元組為單位）。|
|--百萬位元-單位|以1000的順序顯示單位，而不是1024。|
|--正在執行-計數|計算檔案總數及其大小。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
