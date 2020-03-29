---
title: SSL - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 用於為 PostgreSQL - 超大規模 （Citus） 和相關應用程式佈建 Azure 資料庫以正確使用 SSL 連線的說明和資訊。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973980"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 資料庫中配置 SSL，用於後縮放 - 超大規模（Citus）
用戶端應用程式連接到超大規模 （Citus） 協調節點需要安全通訊端層 （SSL）。 通過加密伺服器和應用程式之間的資料流程，在資料庫伺服器和用戶端應用程式之間強制實施 SSL 連線，有助於防止"中間人"攻擊。

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線
對於通過 Azure 門戶預配的所有 PostgreSQL 伺服器 Azure 資料庫，預設情況下啟用 SSL 連線的強制。 

同樣地，在 Azure 入口網站中，您的伺服器下方的 [連接字串] 設定中所預先定義的連接字串，也包含通用語言使用 SSL 連線至資料庫伺服器所需的參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線
某些應用程式框架在安裝過程中預設不啟用 SSL， 將 PostgreSQL 用於其資料庫服務。 如果 PostgreSQL 伺服器強制執行 SSL 連線，但應用程式未配置為 SSL，則應用程式可能無法連接到資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 連接到 Azure 資料庫的 PostgreSQL- 超大規模 （Citus） 的證書https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem位於 。 下載證書檔並將其保存到您的首選位置。

### <a name="connect-using-psql"></a>使用 psql 連接
下面的示例演示如何使用 psql 命令列實用程式連接到超大規模 （Citus） 協調器節點。 使用`sslmode=verify-full`連接字串設置強制實施 SSL 憑證驗證。 將本地證書檔路徑傳遞給參數`sslrootcert`。

下面是 psql 連接字串的示例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 確認傳遞給`sslrootcert`的值與保存的證書的檔路徑匹配。

## <a name="next-steps"></a>後續步驟
使用 Azure[資料庫中的防火牆規則進一步提高安全性，用於 PostgreSQL- 超大規模 （Citus）。](concepts-hyperscale-firewall-rules.md)
