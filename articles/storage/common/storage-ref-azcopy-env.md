---
title: azcopy env |Microsoft Docs
description: 本文提供 azcopy env 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74033753"
---
# <a name="azcopy-env"></a>azcopy 環境

顯示可以設定 AzCopy 行為的環境變數。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果您使用命令列設定環境變數，該變數將可在您的命令列歷程記錄中讀取。 請考慮從您的命令列歷程記錄中清除包含認證的變數。 若要讓變數不會出現在歷程記錄中，您可以使用腳本來提示使用者輸入其認證，並設定環境變數。

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 env 命令的說明內容。 |
|--區分顯示|顯示敏感性/密碼環境變數。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
