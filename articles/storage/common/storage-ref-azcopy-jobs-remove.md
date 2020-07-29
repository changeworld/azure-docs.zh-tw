---
title: azcopy 作業移除 |Microsoft Docs
description: 本文提供 azcopy 作業 [移除] 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b1f1b58e9cce061aaa313457ec43256a766e3a2e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281968"
---
# <a name="azcopy-jobs-remove"></a>azcopy 作業移除

移除與指定之工作識別碼相關聯的所有檔案。

> [!NOTE] 
> 您可以自訂記錄檔和計畫檔案的儲存位置。 若要深入瞭解，請參閱[azcopy env](storage-ref-azcopy-env.md)命令。

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>選項。

**--help**               移除的說明。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps 浮點數**     上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值是 `text`。 （預設值 `text` ）

**--[受信任]-[microsoft 尾碼**] 字串會指定可以傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
