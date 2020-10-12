---
title: azcopy 作業乾淨 |Microsoft Docs
description: 本文提供 azcopy 工作 clean 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f3e9d70ced0d2974a66717436c28c5b6914f6745
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287147"
---
# <a name="azcopy-jobs-clean"></a>azcopy 作業清除

移除所有作業的所有記錄檔和計畫檔案

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>選項。

**--**                說明               清除的說明。

**--with-status** string 只會移除具有此狀態的作業，可用的值如下： `Canceled` 、 `Completed` 、 `Failed` 、 `InProgress` 、 `All` (預設值 `All`) 

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps float**      以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。

**--** 命令輸出的輸出類型字串格式。 選項包括： text、json。 預設值為 [text]。  (預設的「文字」 ) 

**--受信任-microsoft 尾碼** 字串指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
