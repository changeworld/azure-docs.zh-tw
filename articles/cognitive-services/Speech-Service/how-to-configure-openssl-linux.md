---
title: 如何設定適用於 Linux 的 OpenSSL
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定適用于 Linux 的 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683173"
---
# <a name="configure-openssl-for-linux"></a>設定適用於 Linux 的 OpenSSL

在1.9.0 之前使用任何語音 SDK 版本時， [OpenSSL](https://www.openssl.org)會以動態方式設定為主機系統版本。 在較新版本的語音 SDK 中，OpenSSL （版本[1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）會以靜態方式連結至語音 sdk 的核心程式庫。

若要確保連線能力，請確認您的系統中已安裝 OpenSSL 憑證。 執行命令：
```bash
openssl version -d
```

以 Ubuntu/Debian 為基礎的系統上的輸出應該是：
```
OPENSSLDIR: "/usr/lib/ssl"
```

檢查是否有 OPENSSLDIR `certs`下的子目錄。 在上述範例中，它會是`/usr/lib/ssl/certs`。

* 如果有`/usr/lib/ssl/certs` ，而且它包含許多個別的憑證檔案（ `.crt`含`.pem`或副檔名），則不需要進一步的動作。

* 如果 OPENSSLDIR 不是`/usr/lib/ssl`和/或有單一憑證組合檔案，而不是多個個別檔案，您就必須設定適當的 SSL 環境變數，以指出可以找到憑證的位置。

## <a name="examples"></a>範例

- OPENSSLDIR 為`/opt/ssl`。 有多`certs`個`.crt`或`.pem`檔案的子目錄。
在執行使用`SSL_CERT_DIR`語音 SDK 的`/opt/ssl/certs`程式之前，請將環境變數設定為指向。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR 是`/etc/pki/tls` （例如以 RHEL/CentOS 為基礎的系統）。 例如`ca-bundle.crt`， `certs`有一個具有憑證配套檔案的子目錄。
在執行使用`SSL_CERT_FILE`語音 SDK 的程式之前，請將環境變數設定為指向該檔案。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> 另外值得一提的是，有些 Linux 散發套件並未定義 TMP 或 TMPDIR 環境變數。 這會導致語音 SDK 每次下載憑證撤銷清單（CRL），而不是將 CRL 快取到磁片以供重複使用，直到過期為止。 若要改善初始連線效能，您可以[建立名為 TMPDIR 的環境變數，並將它設定為您所選臨時目錄的路徑](https://help.ubuntu.com/community/EnvironmentVariables)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)
