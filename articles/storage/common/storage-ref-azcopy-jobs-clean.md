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
ms.openlocfilehash: a06e428908777c526602166f127a28304b595ba0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220072"
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

**--[受信任]-[microsoft 尾碼**] 字串會指定可以傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
