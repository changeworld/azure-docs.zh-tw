---
title: azcopy |Microsoft Docs
description: 本文提供 azcopy 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74038097"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一種命令列工具，可將資料移入和移出 Azure 儲存體。

## <a name="synopsis"></a>概要

命令的一般格式為： `azcopy [command] [arguments] --[flag-name]=[flag-value]`。

若要報告問題或深入瞭解此工具，請參閱[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)。

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項

**--cap-mbps uint32**  上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**-h、--help**Azcopy 的說明
      
**--output-類型** 命令輸出的格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

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
