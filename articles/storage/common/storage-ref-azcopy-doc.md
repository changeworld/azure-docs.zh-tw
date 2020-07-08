---
title: azcopy 檔 |Microsoft Docs
description: 本文提供 azcopy doc 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f198d2ceef70764c464d3ce1db3d6c2bcae398bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220110"
---
# <a name="azcopy-doc"></a>azcopy 文件

以 Markdown 格式產生工具的檔。

## <a name="synopsis"></a>概要

以 Markdown 格式產生工具的檔，並將其儲存在指定的位置。

根據預設，檔案會儲存在目前目錄中名為 ' doc ' 的資料夾中。

```azcopy
azcopy doc [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項

|選項|說明|
|--|--|
|-h, --help|顯示 doc 命令的說明內容。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   | 指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
