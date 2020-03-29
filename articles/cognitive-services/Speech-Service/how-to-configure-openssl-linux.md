---
title: 如何設定適用於 Linux 的 OpenSSL
titleSuffix: Azure Cognitive Services
description: 瞭解如何為 Linux 配置 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331139"
---
# <a name="configure-openssl-for-linux"></a>設定適用於 Linux 的 OpenSSL

在 1.9.0 之前使用任何語音 SDK 版本時[，OpenSSL](https://www.openssl.org)會動態配置為主機系統版本。 在更高版本的語音 SDK 中，OpenSSL（版本[1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）以靜態方式連結到語音 SDK 的核心庫。

為確保連接，請驗證系統中是否安裝了 OpenSSL 憑證。 運行命令：
```bash
openssl version -d
```

基於 Ubuntu/Debian 的系統上的輸出應為：
```
OPENSSLDIR: "/usr/lib/ssl"
```

檢查 OPENSSLDIR 下是否有`certs`子目錄。 在上面的示例中，它將是`/usr/lib/ssl/certs`。

* 如果有`/usr/lib/ssl/certs`並且它包含許多單獨的證書檔（帶有`.crt`或`.pem`副檔名），則無需進一步操作。

* 如果 OPENSSLDIR 是和`/usr/lib/ssl`/或存在單個證書捆綁檔而不是多個單個檔，則需要設置適當的 SSL 環境變數以指示在何處可以找到證書。

## <a name="examples"></a>範例

- OPENSSLDIR`/opt/ssl`是 。 有`certs`具有許多`.crt`或`.pem`檔的子目錄。
在運行使用`SSL_CERT_DIR`語音 SDK`/opt/ssl/certs`的程式之前，將環境變數設置為指向。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR`/etc/pki/tls`是（如在基於 RHEL/CentOS 的系統上）。 有帶有`certs`證書捆綁檔的子目錄，例如`ca-bundle.crt`。
在運行使用`SSL_CERT_FILE`語音 SDK 的程式之前，將環境變數設置為指向該檔。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)
