---
title: 阿茲比斯 |微軟文檔
description: 本文為 azcopy 命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038097"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一種命令列工具，用於將資料移入和移出 Azure 存儲。

## <a name="synopsis"></a>概要

命令的一般格式是： `azcopy [command] [arguments] --[flag-name]=[flag-value]`。

要報告問題或瞭解有關該工具的更多資訊，請參閱[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)。

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項。

**--蓋-mbps uint32**  以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。

**-h， -- 説明**提取複寫的説明
      
**--輸出型** 命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。 （預設"文本"）

## <a name="see-also"></a>另請參閱

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [azcopy 工作台](storage-ref-azcopy-bench.md)
- [azcopy 複製](storage-ref-azcopy-copy.md)
- [azcopy 文件](storage-ref-azcopy-doc.md)
- [azcopy 環境](storage-ref-azcopy-env.md)
- [azcopy 作業](storage-ref-azcopy-jobs.md)
- [azcopy 作業清除](storage-ref-azcopy-jobs-clean.md)
- [azcopy 作業清單](storage-ref-azcopy-jobs-list.md)
- [azcopy 作業移除](storage-ref-azcopy-jobs-remove.md)
- [azcopy 作業繼續](storage-ref-azcopy-jobs-resume.md)
- [azcopy 作業顯示](storage-ref-azcopy-jobs-show.md)
- [azcopy 清單](storage-ref-azcopy-list.md)
- [azcopy 登入](storage-ref-azcopy-login.md)
- [azcopy 登出](storage-ref-azcopy-logout.md)
- [azcopy 製作](storage-ref-azcopy-make.md)
- [azcopy 移除](storage-ref-azcopy-remove.md)
- [azcopy 同步](storage-ref-azcopy-sync.md)
