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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683173"
---
# <a name="configure-openssl-for-linux"></a>設定適用於 Linux 的 OpenSSL

在1.9.0 之前使用任何語音 SDK 版本時， [OpenSSL](https://www.openssl.org) 會動態設定為主機系統版本。 在更新版本的語音 SDK 中，OpenSSL (版本 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) 會靜態地連結到語音 sdk 的核心程式庫。

為確保連線能力，請確認您的系統已安裝 OpenSSL 憑證。 執行命令：
```bash
openssl version -d
```

以 Ubuntu/Debian 為基礎的系統輸出應該是：
```
OPENSSLDIR: "/usr/lib/ssl"
```

檢查 `certs` OPENSSLDIR 下是否有子目錄。 在上述範例中，它會是 `/usr/lib/ssl/certs` 。

* 如果有 `/usr/lib/ssl/certs` ，而且其中包含許多個別的憑證檔案 (有 `.crt` 或 `.pem` 擴充) ，就不需要進一步的動作。

* 如果 OPENSSLDIR 不是 `/usr/lib/ssl` 和/或有單一的憑證套件組合檔案，而不是多個個別檔案，您就必須設定適當的 SSL 環境變數來指出可以找到憑證的位置。

## <a name="examples"></a>範例

- OPENSSLDIR 為 `/opt/ssl` 。 有 `certs` 許多或檔案的 `.crt` 子目錄 `.pem` 。
在 `SSL_CERT_DIR` `/opt/ssl/certs` 執行使用語音 SDK 的程式之前，請先設定要指向的環境變數。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` 在 RHEL/CentOS 型系統上 (如) 。 `certs`例如，有一個子目錄具有憑證套件組合檔案 `ca-bundle.crt` 。
先設定環境變數 `SSL_CERT_FILE` 以指向該檔案，然後再執行使用語音 SDK 的程式。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> 另外值得注意的是，某些 Linux 發行版本並未定義 TMP 或 TMPDIR 環境變數。 這會導致語音 SDK 在每次 (CRL) 下載憑證撤銷清單，而不是將 CRL 快取到磁片以供重複使用，直到過期為止。 若要改善初始連接效能，您可以 [建立名為 TMPDIR 的環境變數，並將它設定為所選臨時目錄的路徑](https://help.ubuntu.com/community/EnvironmentVariables)。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)
