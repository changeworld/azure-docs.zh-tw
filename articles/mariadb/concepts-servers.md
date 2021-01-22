---
title: 伺服器-適用於 MariaDB 的 Azure 資料庫
description: 此主題提供使用適用於 MariaDB 的 Azure 資料庫伺服器的考量與指導方針。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abe17556d9ff62c44a33bfe4c4546a284785522e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664124"
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

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>停止/啟動適用於 MariaDB 的 Azure 資料庫 (預覽) 
適用於 MariaDB 的 Azure 資料庫讓您能夠在不使用時 **停止** 伺服器，並在繼續活動時 **啟動** 伺服器。 這基本上是為了節省資料庫伺服器的成本，而且只需在使用時支付資源費用。 對於開發/測試工作負載，以及當您只在一天中使用伺服器時，這會變得更重要。 當您停止伺服器時，所有作用中的連接都會被捨棄。 稍後，當您想要讓伺服器重新上線時，您可以使用 [Azure 入口網站](../mysql/how-to-stop-start-server.md) 或 [CLI](../mysql/how-to-stop-start-server.md)。

當伺服器處於 [ **已停止** ] 狀態時，伺服器的計算並不計費。 不過，儲存體會繼續以伺服器的存放裝置計費，以確保在重新開機伺服器時，可以使用資料檔案。

> [!IMPORTANT]
> 當您 **停止** 伺服器時，它會在延展的未來7天處於該狀態。 如果您未在這段時間內手動 **啟動** ，伺服器將會在7天結束時自動啟動。 如果您不是使用伺服器，可以選擇 **停止** 它。

當伺服器停止時，伺服器上不能執行任何管理作業。 若要變更伺服器上的任何設定，您將需要 [啟動伺服器](../mysql/how-to-stop-start-server.md)。

### <a name="limitations-of-stopstart-operation"></a>停止/啟動作業的限制
-  () 的來源和複本都不支援讀取複本設定。

## <a name="how-do-i-manage-a-server"></a>如何管理伺服器？
您可以使用 Azure 入口網站或 Azure CLI 來管理「適用於 MariaDB 的 Azure 資料庫」伺服器。

## <a name="next-steps"></a>下一步
- 如需服務的概觀，請參閱[適用於 MariaDB 的 Azure 資料庫概觀](./overview.md)
- 如需以您 **服務層級** 為依據之特定資源配額和限制的相關資訊，請參閱 [服務層級](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
