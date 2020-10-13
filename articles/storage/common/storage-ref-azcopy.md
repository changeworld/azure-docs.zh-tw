---
title: azcopy |Microsoft Docs
description: 本文提供 azcopy 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 18972e991f08db7fa9548454a5c5cdc3ff0f552f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87285181"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一種命令列工具，可將資料移入和移出 Azure 儲存體。

## <a name="synopsis"></a>概要

命令的一般格式為： `azcopy [command] [arguments] --[flag-name]=[flag-value]` 。

若要報告問題或深入瞭解此工具，請參閱 [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) 。

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項。

**--cap-mbps** (浮點數) 以每秒 mb 數為單位的速率。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。

**--** 說明Azcopy 的說明
      
**--輸出類型**  (字串) 命令輸出的格式。 選項包括： text、json。 預設值為 `text`。  (預設 `text`) 

**--受信任-microsoft-尾碼** (字串) 指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。

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
