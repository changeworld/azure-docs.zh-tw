---
title: 伺服器-適用於 MariaDB 的 Azure 資料庫
description: 此主題提供使用適用於 MariaDB 的 Azure 資料庫伺服器的考量與指導方針。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527787"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的伺服器概念
此文章提供使用適用於 MariaDB 的 Azure 資料庫伺服器的考量與指導方針。

## <a name="what-is-an-azure-database-for-mariadb-server"></a>什麼是適用於 MariaDB 的 Azure 資料庫伺服器？

適用於 MariaDB 的 Azure 資料庫伺服器是多個資料庫的中央管理點。 這與您可能已在內部部署領域中熟悉的 MariaDB 伺服器建構相同。 具體來說，適用於 MariaDB 的 Azure 資料庫服務會受到管理、提供效能保證，以及公開伺服器層級的存取與功能。

適用於 MariaDB 伺服器的 Azure 資料庫：

- 建立於 Azure 訂用帳戶內。
- 是資料庫的父資源。
- 可為資料庫提供命名空間。
- 是具有強式存留期語意 (刪除伺服器) 的容器，而且會刪除自主資料庫。
- 在一個區域中共置資源。
- 提供用來存取伺服器和資料庫的連接端點。
- 提供適用於其資料庫的管理原則範圍︰登入、防火牆、使用者、角色、設定等等。
- 可在 MariaDB 引擎第 10.2 版中找到。 如需詳細資訊，請參閱[支援之適用於 MariaDB 的 Azure 資料庫的資料庫版本](./concepts-supported-versions.md)。

在適用於 MariaDB 的 Azure 資料庫伺服器內，您可以建立一或多個資料庫。 您可以選擇在每部伺服器上建立單一資料庫以使用所有資源，或建立多個資料庫來共用資源。 定價結構是依據伺服器，以定價層、虛擬核心及儲存體 (GB) 的設定為基礎來形成的。 如需詳細資訊，請參閱[定價層](./concepts-pricing-tiers.md)。

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>如何保護適用於 MariaDB 的 Azure 資料庫伺服器？

下列項目有助於確保對資料庫的安全存取。

|||
| :--| :--|
| **驗證與授權** | 適用於 MariaDB 的 Azure 資料庫支援原生 MySQL 驗證。 您可以利用伺服器的管理員登入來連接和驗證伺服器。 |
| **通訊協定** | 此服務支援 MySQL 所使用的訊息架構通訊協定。 |
| **TCP/IP** | TCP/IP 和 Unix 網域通訊端上支援此通訊協定。 |
| **防火牆** | 為了協助保護您的資料，防火牆規則會防止對您的資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限為止。 請參閱[適用於 MariaDB 的 Azure 資料庫伺服器防火牆規則](./concepts-firewall-rules.md)。 |
| **SSL** | 此服務支援在您的應用程式和資料庫伺服器之間強制執行 SSL 連接。 請參閱[在您的應用程式中設定 SSL 連線以安全地連線至適用於 MariaDB 的 Azure 資料庫](./howto-configure-ssl.md)。 |

## <a name="how-do-i-manage-a-server"></a>如何管理伺服器？
您可以使用 Azure 入口網站或 Azure CLI 來管理「適用於 MariaDB 的 Azure 資料庫」伺服器。

## <a name="next-steps"></a>後續步驟
- 如需服務的概觀，請參閱[適用於 MariaDB 的 Azure 資料庫概觀](./overview.md)
- 如需以您**服務層級**為依據之特定資源配額和限制的相關資訊，請參閱[服務層級](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
