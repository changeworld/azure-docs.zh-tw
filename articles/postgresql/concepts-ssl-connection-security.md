---
title: SSL/TLS-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 有關如何為適用於 PostgreSQL 的 Azure 資料庫單一伺服器設定 TLS 連線能力的指示和資訊。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 615e8c80d194bb37feac1c09af22d2aa5d4aa3fc
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142721"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中設定 TLS 連線能力

適用於 PostgreSQL 的 Azure 資料庫偏好將您的用戶端應用程式連接到使用傳輸層安全性 (TLS) ，先前稱為安全通訊端層 (SSL) 。 在您的資料庫伺服器和用戶端應用程式之間強制執行 TLS 連線，可將伺服器與應用程式之間的資料流程加密，以協助防止「攔截式」攻擊。

根據預設，于 postgresql 資料庫服務會設定為需要 TLS 連接。 如果您的用戶端應用程式不支援 TLS 連線，您可以選擇停用需要 TLS。

## <a name="enforcing-tls-connections"></a>強制執行 TLS 連線

針對透過 Azure 入口網站和 CLI 布建的所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用強制執行 TLS 連線。 

同樣地，在 Azure 入口網站伺服器下的 [連接字串] 設定中預先定義的連接字串，包括通用語言使用 TLS 連接到您的資料庫伺服器所需的參數。 TLS 參數會根據連接器而有所不同，例如 "ssl = true" 或 "sslmode = required" 或 "sslmode = required" 和其他變化。

## <a name="configure-enforcement-of-tls"></a>設定 TLS 的強制執行

您可以選擇性地停用強制 TLS 連線。 Microsoft Azure 建議一律啟用 [**強制執行 SSL**連線] 設定，以增強安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

瀏覽您適用於 PostgreSQL 的 Azure 資料庫伺服器，然後按一下 [連接安全性]****。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線]**** 設定。 然後按一下 [儲存]****。

![連線安全性-停用強制 TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

您可以檢視 [概觀]**** 頁面來查看 [SSL 強制執行狀態]**** 指標，以確認此設定。

### <a name="using-azure-cli"></a>使用 Azure CLI

您可以在 Azure CLI 中分別使用 `Enabled` 或 `Disabled` 值，來啟用或停用 **sssl-enforcement** 參數。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確保您的應用程式或架構支援 TLS 連線

某些針對其資料庫服務使用於 postgresql 的應用程式架構，預設不會在安裝期間啟用 TLS。 如果您的于 postgresql 伺服器強制執行 TLS 連線，但未針對 TLS 設定應用程式，則應用程式可能無法連接到您的資料庫伺服器。 請參閱您的應用程式檔，以瞭解如何啟用 TLS 連線。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要憑證驗證以進行 TLS 連線的應用程式

在某些情況下，應用程式需要由受信任的憑證授權單位單位所產生的本機憑證檔案 (CA) 憑證檔案，以安全地連接。 用來連接到適用於 PostgreSQL 的 Azure 資料庫伺服器的憑證位於 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。 

請參閱下列連結，以取得主權雲端中伺服器的憑證： [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、 [Azure 中國](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)和[azure 德國](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)。

### <a name="connect-using-psql"></a>使用 psql 連接

下列範例示範如何使用 psql 命令列公用程式連線到您的于 postgresql 伺服器。 使用 [ `sslmode=verify-full` 連接字串] 設定來強制執行 TLS/SSL 憑證驗證。 將本機憑證檔案路徑傳遞給 `sslrootcert` 參數。

下列命令是 psql 連接字串的範例：

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 確認傳遞給的值 `sslrootcert` 符合您儲存之憑證的檔案路徑。

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的 TLS 強制

適用於 PostgreSQL 的 Azure 資料庫-單一伺服器支援使用傳輸層安全性 (TLS) 來連接到資料庫伺服器的用戶端加密。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器和用戶端應用程式之間的安全網路連線，讓您遵守合規性需求。

### <a name="tls-settings"></a>TLS 設定

適用於 PostgreSQL 的 Azure 資料庫單一伺服器提供強制執行用戶端連線 TLS 版本的功能。 若要強制執行 TLS 版本，請使用**最小的 tls 版本**選項設定。 此選項設定允許下列值：

|  最小 TLS 設定             | 支援的用戶端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (預設)  | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 和更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本          |
| TLS1_2                           | TLS 1.2 版和更新版本           |


例如，將此最小的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許使用 TLS 1.0、1.1 和 1.2 + 的用戶端進行連接。 或者，將此值設定為1.2，表示您只允許使用 TLS 1.2 + 的用戶端連線，且 TLS 1.0 和 TLS 1.1 的所有連線都會遭到拒絕。

> [!Note] 
> 根據預設，適用於 PostgreSQL 的 Azure 資料庫不會強制執行最低的 TLS 版本， (設定 `TLSEnforcementDisabled`) 。
>
> 一旦您強制執行最低的 TLS 版本，您之後就無法停用最低版本強制。

若要瞭解如何設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的 TLS 設定，請參閱 how [to CONFIGURE tls 設定](howto-tls-configurations.md)。

## <a name="next-steps"></a>後續步驟

在[適用於 PostgreSQL 的 Azure 資料庫的連接程式庫](concepts-connection-libraries.md)中，檢查各種應用程式連接選項。

- 瞭解如何[設定 TLS](howto-tls-configurations.md)