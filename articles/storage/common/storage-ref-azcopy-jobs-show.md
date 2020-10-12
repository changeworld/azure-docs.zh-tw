---
title: azcopy 作業顯示 |Microsoft Docs
description: 本文提供 azcopy job show 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ffb16936ec96f30ad7d8e05babbadb85e01a71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281934"
---
# <a name="azcopy-jobs-show"></a>azcopy 作業顯示

顯示指定工作識別碼的詳細資訊。

## <a name="synopsis"></a>概要

如果只提供沒有旗標的工作識別碼，則會傳回作業的進度摘要。

當您執行此命令時，所顯示的位元組計數和完成百分比只會反映在作業中完成的檔案。 它們不會反映部分完成的檔案。

如果 `with-status` 已設定旗標，則會顯示作業中具有指定值的傳輸清單。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項。

|選項|說明|
|--|--|
|-h, --help|顯示顯示命令的說明內容。|
|--with-status 字串|只列出具有此狀態的作業傳輸，可用的值：已啟動、成功、失敗|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps float|以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。|
|--output 類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--受信任-microsoft-尾碼字串   |指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
