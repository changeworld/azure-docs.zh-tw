---
title: 如何設定適用於 Linux 的 OpenSSL
titleSuffix: Azure Cognitive Services
description: 瞭解如何為 Linux 設定 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683173"
---
# <a name="configure-openssl-for-linux"></a>設定適用於 Linux 的 OpenSSL

在 1.9.0 之前使用任何語音 SDK 版本時[,OpenSSL](https://www.openssl.org)會動態配置為主機系統版本。 在更高版本的語音 SDK 中,OpenSSL(版本[1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html))以靜態方式連結到語音 SDK 的核心庫。

為確保連接,請驗證系統中是否安裝了 OpenSSL 證書。 執行指令:
```bash
openssl version -d
```

基於 Ubuntu/Debian 的系統上的輸出應為:
```
OPENSSLDIR: "/usr/lib/ssl"
```

檢查 OPENSSLDIR`certs`下是否有子目錄。 在上面的範例中,它將是`/usr/lib/ssl/certs`。

* 如果有`/usr/lib/ssl/certs`並且它包含許多單獨的證書檔(帶`.crt`有`.pem`或擴展名),則無需進一步操作。

* 如果 OPENSSLDIR`/usr/lib/ssl`是和 /或存在單個證書捆綁檔而不是多個單個檔,則需要設置適當的 SSL 環境變數以指示在何處可以找到證書。

## <a name="examples"></a>範例

- OPENSSLDIR`/opt/ssl`是 。 有`certs`具有許多`.crt`或`.pem`檔的子目錄。
在運行使用`SSL_CERT_DIR`語音 SDK`/opt/ssl/certs`的程式之前,將環境變數設置為指向。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR`/etc/pki/tls`是(如在基於 RHEL/CentOS 的系統上)。 有帶有`certs`憑證捆綁檔案的子目錄,`ca-bundle.crt`例如 。
在運行使用`SSL_CERT_FILE`語音 SDK 的程式之前,將環境變數設置為指向該檔。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> 還值得注意的是,Linux 的某些發行版沒有定義 TMP 或 TMPDIR 環境變數。 這將導致語音 SDK 每次下載證書吊銷清單 (CRL),而不是將 CRL 快取到磁碟以進行重用,直到過期。 為了提高初始連接性能[,您可以創建名為 TMPDIR 的環境變數並將其設置為所選臨時目錄的路徑。 .](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)
