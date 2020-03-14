---
title: SSL 連線能力-適用於 MariaDB 的 Azure 資料庫
description: 用來設定適用於 MariaDB 的 Azure 資料庫與相關聯應用程式以適當使用 SSL 連線的資訊
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c03a56176a6e2cc995e74017b60747541fc843bb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371625"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的 SSL 連線能力
適用於 MariaDB 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將資料庫伺服器連接至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。

## <a name="default-settings"></a>預設設定
根據預設，資料庫服務應該會設定為在連線到 MariaDB 時需要 SSL 連接。  建議盡可能地避免停用 SSL 選項。

透過 Azure 入口網站與 CLI 佈建新的適用於 MariaDB 的 Azure 資料庫伺服器時，預設會強制執行 SSL 連線。

Azure 入口網站中會顯示多種程式設計語言的連接字串。 這些連接字串包含連接到您資料庫所需的 SSL 參數。 在 Azure 入口網站中選取您的伺服器。 在下 [設定] 標題之下，選取 [連接字串]。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

若要了解如何在開發應用程式時啟用或停用 SSL 連接，請參閱[如何設定 SSL](howto-configure-ssl.md)。

## <a name="tls-connectivity-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的 TLS 連線

適用於 MariaDB 的 Azure 資料庫支援使用傳輸層安全性（TLS）連接到您的資料庫伺服器之用戶端的加密。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器和用戶端應用程式之間的安全網路連線，讓您遵守合規性需求。

### <a name="tls-settings"></a>TLS 設定

適用於 MariaDB 的 Azure 資料庫提供強制執行用戶端連線 TLS 版本的功能。 若要使用 TLS 選項，請使用 [**最小 tls 版本**] 選項設定。 此選項設定允許下列值：

|  最小 TLS 設定             | 支援的 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled （預設值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 和更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本          |
| TLS1_2                           | TLS 1.2 版和更新版本           |


例如，將此最小的 TLS 設定版本設定為 TLS 1.0，表示您的伺服器將允許使用 TLS 1.0、1.1 和 1.2 + 的用戶端進行連接。 或者，將此值設定為1.2，表示您只允許使用 TLS 1.2 的用戶端進行連線，而且所有具有 TLS 1.0 和 TLS 1.1 的連線都將遭到拒絕。

> [!Note] 
> 適用於 MariaDB 的 Azure 資料庫預設為停用所有新伺服器的 TLS。
>
> 目前，適用于適用于 mariadb 的 TLS 版本支援 byAzure 資料庫為 TLS 1.0、1.1 和1.2。

若要瞭解如何設定適用於 MariaDB 的 Azure 資料庫的 TLS 設定，請參閱 how [to CONFIGURE tls 設定](howto-tls-configurations.md)。

## <a name="next-steps"></a>後續步驟
- 深入了解[伺服器防火牆規則](concepts-firewall-rules.md)
- 了解如何[設定 SSL](howto-configure-ssl.md)。
- 瞭解如何[設定 TLS](howto-tls-configurations.md)。