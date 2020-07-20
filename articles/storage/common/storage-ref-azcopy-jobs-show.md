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
ms.openlocfilehash: 06efebb1c92b03036f4ae0b904ebfcc4c0c0f8ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220005"
---
# <a name="azcopy-jobs-show"></a>azcopy 作業顯示

顯示給定作業識別碼的詳細資訊。

## <a name="synopsis"></a>概要

如果只有在沒有旗標的情況下提供作業識別碼，則會傳回作業的進度摘要。

當您執行此命令時所顯示的位元組計數和完成百分比，只會反映作業中完成的檔案。 它們不會反映部分完成的檔案。

如果 `with-status` 設定了旗標，則會顯示作業中具有指定值的傳輸清單。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項

|選項|說明|
|--|--|
|-h, --help|顯示 show 命令的說明內容。|
|--with-status string|僅列出具有此狀態的作業傳輸，可用的值： [已啟動]、[成功]、[失敗]|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   |指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
