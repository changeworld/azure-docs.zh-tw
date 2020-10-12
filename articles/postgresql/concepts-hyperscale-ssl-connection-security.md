---
title: 傳輸層安全性 (TLS) -超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 設定適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 和相關聯的應用程式，以正確使用 TLS 連線的指示和資訊。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071464"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>在適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 中設定 TLS
超大規模 (Citus) 協調器節點需要用戶端應用程式與傳輸層安全性 (TLS) 連線。 在資料庫伺服器和用戶端應用程式之間強制執行 TLS 有助於讓資料在傳輸中保持機密。 以下所述的額外驗證設定也可防止「攔截式」攻擊。

## <a name="enforcing-tls-connections"></a>強制執行 TLS 連接
應用程式會使用「連接字串」來識別目的地資料庫和連接的設定。 不同的用戶端需要不同的設定。 若要查看通用用戶端所使用的連接字串清單，請參閱 Azure 入口網站中伺服器群組的 [ **連接字串** ] 區段。

TLS 參數 `ssl` 和 `sslmode` 會根據連接器的功能而有所不同，例如 `ssl=true` 或 `sslmode=require` 或 `sslmode=required` 。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確定您的應用程式或架構支援 TLS 連接
某些應用程式架構預設不會啟用 TLS 的于 postgresql 連接。 不過，若沒有安全連線，應用程式將無法連線到超大規模 (Citus) 協調器節點。 請參閱您應用程式的檔，以瞭解如何啟用 TLS 連接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要憑證驗證以進行 TLS 連接的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 要連接到適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 的憑證位於 https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。

> [!NOTE]
>
> 若要檢查憑證的真實性，您可以使用 OpenSSL 命令列工具來確認其 256 SHA-1 指紋：
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>使用 psql 連接
下列範例示範如何使用 psql 命令列公用程式，連接到您的超大規模 (Citus) 協調器節點。 使用 `sslmode=verify-full` 連接字串設定來強制執行 TLS 憑證驗證。 將本機憑證檔案路徑傳遞至 `sslrootcert` 參數。

以下是 psql 連接字串的範例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 確認傳遞給的值 `sslrootcert` 符合您儲存之憑證的檔案路徑。

## <a name="next-steps"></a>接下來的步驟
使用 [適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中的防火牆規則 ](concepts-hyperscale-firewall-rules.md)進一步提高安全性。
