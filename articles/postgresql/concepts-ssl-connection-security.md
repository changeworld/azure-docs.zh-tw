---
title: SSL/TLS-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 有關如何為適用於 PostgreSQL 的 Azure 資料庫單一伺服器設定 TLS 連線能力的指示和資訊。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 2785f79d327402a40be0a905877b5113b3f751b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710442"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中設定 TLS 連線能力

適用於 PostgreSQL 的 Azure 資料庫偏好將用戶端應用程式連接到使用傳輸層安全性 (TLS) （先前稱為安全通訊端層 (SSL) ）的于 postgresql 服務。 在您的資料庫伺服器與用戶端應用程式之間強制執行 TLS 連線，可透過加密伺服器和應用程式之間的資料流程，來防止「攔截式」攻擊。

根據預設，于 postgresql 資料庫服務會設定為需要 TLS 連接。 如果您的用戶端應用程式不支援 TLS 連接，您可以選擇停用需要 TLS。

## <a name="enforcing-tls-connections"></a>強制執行 TLS 連接

針對透過 Azure 入口網站和 CLI 布建的所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用 TLS 連接的強制。 

同樣地，在 Azure 入口網站的伺服器下的 [連接字串] 設定中預先定義的連接字串，也包括使用 TLS 連接到您的資料庫伺服器所需的通用語言參數。 TLS 參數會根據連接器而有所不同，例如「ssl = true」或「sslmode = 需要」或「sslmode = 必要」，以及其他變化。

## <a name="configure-enforcement-of-tls"></a>設定 TLS 的強制執行

您可以選擇性地停用強制執行 TLS 連接。 Microsoft Azure 建議一律啟用 [ **強制執行 SSL** 連線] 設定，以增強安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

瀏覽您適用於 PostgreSQL 的 Azure 資料庫伺服器，然後按一下 [連接安全性]****。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線]**** 設定。 然後按一下 [ **儲存**]。

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="連接安全性-停用強制使用 TLS/SSL":::

您可以檢視 [概觀]**** 頁面來查看 [SSL 強制執行狀態]**** 指標，以確認此設定。

### <a name="using-azure-cli"></a>使用 Azure CLI

您可以在 Azure CLI 中分別使用 `Enabled` 或 `Disabled` 值，來啟用或停用 **sssl-enforcement** 參數。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確定您的應用程式或架構支援 TLS 連接

某些針對其資料庫服務使用於 postgresql 的應用程式架構，在安裝期間預設不會啟用 TLS。 如果您的于 postgresql 伺服器強制執行 TLS 連線，但應用程式未針對 TLS 進行設定，則應用程式可能無法連線到您的資料庫伺服器。 請參閱您應用程式的檔，以瞭解如何啟用 TLS 連接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要憑證驗證以進行 TLS 連接的應用程式

在某些情況下，應用程式需要由信任的憑證授權單位單位產生的本機憑證檔案 (CA) 憑證檔案，才能安全地連接。 要連接到適用於 PostgreSQL 的 Azure 資料庫伺服器的憑證位於 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。 

請參閱下列連結以取得主權雲端中伺服器的憑證： [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、 [Azure 中國](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)和 [Azure 德國](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)。

### <a name="connect-using-psql"></a>使用 psql 連接

下列範例示範如何使用 psql 命令列公用程式連接到您的于 postgresql 伺服器。 使用 `sslmode=verify-full` 連接字串設定來強制執行 TLS/SSL 憑證驗證。 將本機憑證檔案路徑傳遞至 `sslrootcert` 參數。

下列命令是 psql 連接字串的範例：

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 確認傳遞給的值 `sslrootcert` 符合您儲存之憑證的檔案路徑。

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的 TLS 強制

適用於 PostgreSQL 的 Azure 資料庫-單一伺服器支援使用傳輸層安全性 (TLS) 來連接至資料庫伺服器的用戶端加密。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器與用戶端應用程式之間的安全網路連線，讓您遵守合規性需求。

### <a name="tls-settings"></a>TLS 設定

適用於 PostgreSQL 的 Azure 資料庫單一伺服器可讓您針對用戶端連線強制使用 TLS 版本。 若要強制使用 TLS 版本，請使用 **最小的 tls 版本** 選項設定。 此選項設定允許下列值：

|  最小的 TLS 設定             | 支援的用戶端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (預設)  | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 和更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 和更新版本          |
| TLS1_2                           | TLS 1.2 版和更高版本           |


例如，將此最低的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許來自使用 TLS 1.0、1.1 和 1.2 + 之用戶端的連接。 或者，將此設定為1.2，表示您只允許使用 TLS 1.2 + 的用戶端連線，以及 TLS 1.0 和 TLS 1.1 的所有連線將會遭到拒絕。

> [!Note] 
> 根據預設，適用於 PostgreSQL 的 Azure 資料庫不會強制執行 (設定) 的最低 TLS 版本 `TLSEnforcementDisabled` 。
>
> 一旦您強制執行最低的 TLS 版本，您之後就無法再停用最低版本強制執行。

若要瞭解如何設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的 TLS 設定，請參閱如何設定 [tls 設定](howto-tls-configurations.md)。

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>適用於 PostgreSQL 的 Azure 資料庫單一伺服器的加密支援

做為 SSL/TLS 通訊的一部分，系統會驗證加密套件，而且只允許支援加密，以與資料庫 serer 通訊。 加密套件驗證是在 [閘道層](concepts-connectivity-architecture.md#connectivity-architecture) 級進行控制，而不是在節點本身上明確控制。 如果加密套件不符合下列其中一個套件，則會拒絕傳入用戶端連接。

### <a name="cipher-suite-supported"></a>支援的密碼套件

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>後續步驟

[針對適用於 PostgreSQL 的 Azure 資料庫查看連接程式庫中的](concepts-connection-libraries.md)各種應用程式連線選項。

- 瞭解如何 [設定 TLS](howto-tls-configurations.md)