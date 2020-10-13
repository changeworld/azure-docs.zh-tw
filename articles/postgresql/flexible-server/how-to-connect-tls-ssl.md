---
title: 在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中使用 TLS/SSL 進行加密的連接
description: 有關如何在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中使用 TLS/SSL 連線的指示和資訊。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934243"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中使用傳輸層安全性加密的連線能力

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援使用傳輸層安全性 (TLS) （先前稱為安全通訊端層 (SSL) ）將用戶端應用程式連接到于 postgresql 服務。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器與用戶端應用程式之間的加密網路連線，讓您遵守合規性需求。

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援使用傳輸層安全性 (TLS 1.2 +) 進行加密的連線，且 TLS 1.0 和 TLS 1.1 的所有連入連線都會遭到拒絕。 針對所有具彈性的伺服器，TLS 連線的強制執行都已啟用，而且您無法停用 TLS/SSL 以連接到有彈性的伺服器。

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>需要 TLS/SSL 連線的憑證驗證的應用程式
在某些情況下，應用程式需要由信任的憑證授權單位單位產生的本機憑證檔案 (CA) 憑證檔案，才能安全地連接。 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器使用 *DigiCert 的全域根 CA*。 下載此憑證，以透過 SSL 從 [DigiCert 全域根 CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 進行通訊，然後將憑證檔案儲存到您的慣用位置。 例如，本教學課程會使用 `c:\ssl`。


### <a name="connect-using-psql"></a>使用 psql 連接
如果您使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的 VNet 中的資源連線到您的伺服器。 您可以建立虛擬機器，並新增至使用您彈性伺服器建立的 VNet。

如果您使用公用存取 (允許的 IP 位址)建立彈性伺服器，可以將本機 IP 位址新增至伺服器上的防火牆規則清單。

下列範例示範如何使用 psql 命令列介面連接到您的伺服器。 使用 `sslmode=verify-full` 連接字串設定來強制執行 TLS/SSL 憑證驗證。 將本機憑證檔案路徑傳遞至 `sslrootcert` 參數。

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> 確認傳遞給的值 `sslrootcert` 符合您儲存之憑證的檔案路徑。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確定您的應用程式或架構支援 TLS 連接

某些針對其資料庫服務使用於 postgresql 的應用程式架構，在安裝期間預設不會啟用 TLS。 您的于 postgresql 伺服器會強制執行 TLS 連線，但如果未針對 TLS 設定應用程式，則應用程式可能無法連線到您的資料庫伺服器。 請參閱您應用程式的檔，以瞭解如何啟用 TLS 連接。

## <a name="next-steps"></a>後續步驟
- [使用 Azure CLI 建立及管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器虛擬網路](./how-to-manage-virtual-network-cli.md)。
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中的網路功能](./concepts-networking.md)
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器防火牆規則](./concepts-networking.md#public-access-allowed-ip-addresses)
