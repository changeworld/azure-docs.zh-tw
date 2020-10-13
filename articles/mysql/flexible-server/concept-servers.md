---
title: 伺服器概念-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本主題提供使用適用於 MySQL 的 Azure 資料庫彈性伺服器的考慮和指導方針
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7217817e9add6214c2da8362a2769cad0c2cf330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934402"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 的伺服器概念

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文提供使用適用於 MySQL 的 Azure 資料庫彈性伺服器的考慮和指導方針。

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>什麼是適用於 MySQL 的 Azure 資料庫彈性的伺服器？

適用於 MySQL 的 Azure 資料庫彈性的伺服器是執行社區版 MySQL 的完全受控資料庫服務。 一般而言，此服務是設計來根據使用者需求提供彈性和設定自訂。 這與您可能已在內部部署領域中熟悉的 MySQL 伺服器建構相同。 具體來說，彈性的伺服器是受控的，提供現成的效能、更好的伺服器管理能力和控制，並公開伺服器層級的存取和功能。

適用於 MySQL 的 Azure 資料庫彈性的伺服器：

- 建立於 Azure 訂用帳戶內。
- 是資料庫的父資源。
- 允許透過伺服器參數公開 MySQL 設定， (伺服器參數概念) 的連結。
- 執行自動備份，並支援時間點還原。
- 可為資料庫提供命名空間。
- 是具有強式存留期語意 (刪除伺服器) 的容器，而且會刪除自主資料庫。
- 在一個區域中共置資源。
- 支援客戶提供的伺服器維護排程
- 能夠在區域冗余設定中部署具彈性的伺服器，以改善高可用性
- 提供資料庫伺服器存取的虛擬網路整合
- 提供方法來節省成本，方法是在不使用時暫停彈性伺服器
- 提供適用於其資料庫的管理原則範圍︰登入、防火牆、使用者、角色、設定等等。
- 目前支援 MySQL 5.7 版。 如需詳細資訊，請參閱 [支援的適用於 MySQL 的 Azure 資料庫引擎版本](./concepts-supported-versions.md)。

在適用於 MySQL 的 Azure 資料庫彈性的伺服器內，您可以建立一個或多個資料庫。 您可以選擇在每部伺服器上建立單一資料庫以使用所有資源，或建立多個資料庫來共用資源。 價格是根據每一伺服器來結構化，以計算層、虛擬核心和儲存體 (GB) 的設定為基礎。 如需詳細資訊，請參閱 [計算和儲存](./concepts-compute-storage.md)。

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>停止/啟動適用於 MySQL 的 Azure 資料庫彈性的伺服器

適用於 MySQL 的 Azure 資料庫彈性的伺服器可讓您在不使用時 **停止** 伺服器，並在繼續活動時 **啟動** 伺服器。 這基本上是為了節省資料庫伺服器的成本，而且只需在使用時支付資源費用。 對於開發/測試工作負載，以及當您只在一天中使用伺服器時，這會變得更重要。 當您停止伺服器時，所有作用中的連接都會被捨棄。 稍後，當您想要讓伺服器重新上線時，您可以使用 [Azure 入口網站](how-to-stop-start-server-portal.md) 或 CLI。

當伺服器處於 [ **已停止** ] 狀態時，伺服器的計算並不計費。 不過，儲存體會繼續以伺服器的存放裝置計費，以確保在重新開機伺服器時，可以使用資料檔案。

> [!IMPORTANT]
> 當您 **停止** 伺服器時，它會在延展的未來7天處於該狀態。 如果您未在這段時間內手動 **啟動** ，伺服器將會在7天結束時自動啟動。 如果您不是使用伺服器，可以選擇 **停止** 它。

當伺服器停止時，伺服器上不能執行任何管理作業。 若要變更伺服器上的任何設定，您將需要 [啟動伺服器](how-to-stop-start-server-portal.md)。 請參閱 [「停止/開始」限制](./concepts-limitations.md#stopstart-operation)。

## <a name="how-do-i-manage-a-server"></a>如何管理伺服器？

您可以使用 [Azure 入口網站](./quickstart-create-server-portal.md) 或 [Azure CLI](./quickstart-create-server-cli.md)來管理適用於 MySQL 的 Azure 資料庫彈性的伺服器。

## <a name="next-steps"></a>後續步驟

-   瞭解 [Create Server](./quickstart-create-server-portal.md)
-   深入瞭解 [監視和警示](./how-to-alert-on-metric.md)

