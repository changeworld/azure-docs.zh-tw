---
title: TLS - 超大規模(Citus) - 用於後格雷SQL的 Azure 資料庫
description: 用於為 PostgreSQL - 超大規模 (Citus) 和相關應用程式配置 Azure 資料庫以正確使用 TLS 連接的說明和資訊。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422327"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 資料庫中為後函數 SQL - 超大規模 (Citus) 設定 TLS
與 Hyperscale (Citus) 協調節點的用戶端應用程式連接需要傳輸層安全 (TLS),以前稱為安全套接字層 (SSL)。 通過加密伺服器和應用程式之間的數據流,在資料庫伺服器和用戶端應用程序之間強制實施 TLS 連接,有助於防止"中間人"攻擊。

## <a name="enforcing-tls-connections"></a>強制 TLS 連線
對於透過 Azure 入口預配的所有 PostgreSQL 伺服器 Azure 資料庫,預設情況下啟用 TLS 連接的強制。 

同樣,在 Azure 門戶中的伺服器下的「連接字串」設定中預定義的連接字串包括使用 TLS 連接到資料庫伺服器的通用語言所需的參數。 TLS 參數因連接器而異,例如"ssl_true"或"sslmode_要求"或"sslmode_需要"和其他變體。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確保您的應用程式或框架支援 TLS 連線
某些應用程式框架在安裝過程中預設不會啟用 TLS,這些框架將 PostgreSQL 用於其資料庫服務。 如果您的 PostgreSQL 伺服器強制執行 TLS 連接,但應用程式未設定為 TLS,則應用程式可能無法連接到資料庫伺服器。 請參閱應用程式的文件,瞭解如何啟用 TLS 連接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要 TLS 連線憑證認證的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 連線到 Azure 資料庫的 PostgreSQL- 超大規模 (Citus) 的憑證https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem, 位於 。 下載憑證檔並將其保存到您的首選位置。

### <a name="connect-using-psql"></a>使用 psql 連線
下面的範例展示如何使用 psql 命令列實用程式連接到超大規模 (Citus) 協調器節點。 使用`sslmode=verify-full`連接字串設置強制執行 TLS 憑證驗證。 將本地端憑證檔案路徑傳遞給`sslrootcert`參數 。

下面是 psql 連接字串的範例:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 確認傳遞給`sslrootcert`的值與保存的證書的文件路徑匹配。

## <a name="next-steps"></a>後續步驟
使用 Azure[資料庫中的防火牆規則進一步提高安全性,用於 PostgreSQL- 超大規模 (Citus)。](concepts-hyperscale-firewall-rules.md)
