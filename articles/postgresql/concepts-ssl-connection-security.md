---
title: SSL - Azure 資料庫，用於後格雷SQL - 單伺服器
description: 有關如何為 PostgreSQL - 單伺服器配置 Azure 資料庫的 SSL 連線的說明和資訊。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476995"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure 資料庫中為 PostgreSQL - 單伺服器配置 SSL 連線

適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 通過加密伺服器和應用程式之間的資料流程，在資料庫伺服器和用戶端應用程式之間強制實施 SSL 連線，有助於防止"中間人"攻擊。

預設會將 PostgreSQL 資料庫服務設定為需要 SSL 連接。 如果用戶端應用程式不支援 SSL 連線，您可以選擇禁用需要 SSL。

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線

針對透過 Azure 入口網站和 CLI 佈建之所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用強制執行 SSL 連接。 

同樣地，在 Azure 入口網站中，您的伺服器下方的 [連接字串] 設定中所預先定義的連接字串，也包含通用語言使用 SSL 連線至資料庫伺服器所需的參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="configure-enforcement-of-ssl"></a>設定強制使用 SSL

您可以選擇性地停用強制執行 SSL 連線能力。 Microsoft Azure 建議始終啟用**強制 SSL 連線**設置，以提高安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

瀏覽您適用於 PostgreSQL 的 Azure 資料庫伺服器，然後按一下 [連接安全性]****。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線]**** 設定。 然後按一下 [儲存]****。

![連接安全性：停用強制執行 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

您可以檢視 [概觀]**** 頁面來查看 [SSL 強制執行狀態]**** 指標，以確認此設定。

### <a name="using-azure-cli"></a>使用 Azure CLI

您可以在 Azure CLI 中分別使用 `Enabled` 或 `Disabled` 值，來啟用或停用 **sssl-enforcement** 參數。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線

某些應用程式框架在安裝過程中預設不啟用 SSL， 將 PostgreSQL 用於其資料庫服務。 如果 PostgreSQL 伺服器強制執行 SSL 連線，但應用程式未配置為 SSL，則應用程式可能無法連接到資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式

在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 連接到 PostgreSQL 伺服器的 Azure 資料庫的證書位於https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem。 下載證書檔並將其保存到您的首選位置。

### <a name="connect-using-psql"></a>使用 psql 連接

下面的示例演示如何使用 psql 命令列實用程式連接到 PostgreSQL 伺服器。 使用`sslmode=verify-full`連接字串設置強制實施 SSL 憑證驗證。 將本地證書檔路徑傳遞給參數`sslrootcert`。

以下命令是 psql 連接字串的示例：

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 確認傳遞給`sslrootcert`的值與保存的證書的檔路徑匹配。

## <a name="next-steps"></a>後續步驟

查看[PostgreSQL Azure 資料庫的連接庫中](concepts-connection-libraries.md)的各種應用程式連接選項。
