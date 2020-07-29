---
title: azcopy make |Microsoft Docs
description: 本文提供 azcopy make 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16d7965b8d42a6fdd6ebd68307b01b322cfa7cc6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285232"
---
# <a name="azcopy-make"></a>azcopy 製作

建立容器或檔案共用。

## <a name="synopsis"></a>概要

建立以指定的資源 URL 表示的容器或檔案共用。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>選項。

|選項|說明|
|--|--|
|-h, --help|顯示 make 命令的說明內容。 |
|--quota-gb uint32|指定共用的大小上限（以 gb （GiB）為單位），零表示您接受檔案服務的預設配額。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps 浮點數|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   |指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
