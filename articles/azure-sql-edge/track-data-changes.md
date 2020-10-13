---
title: 在 Azure SQL Edge 中追蹤資料變更
description: 瞭解 Azure SQL Edge 中的變更追蹤和變更資料捕捉。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f7d5d5f74a816bf745faf5decf761cd453f40123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900074"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>在 Azure SQL Edge 中追蹤資料變更

Azure SQL Edge 支援兩項 SQL Server 功能，可追蹤資料庫中資料的變更： [變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) 和 [變更資料捕捉](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)。 這些功能可讓應用程式判斷資料修改語言變更， (對資料庫中使用者資料表所做的插入、更新和刪除作業) 。 您可以在相同的資料庫上啟用變更資料捕獲和變更追蹤。 不需要進行任何特殊考量。

在資料庫中查詢已經變更之資料的能力，是讓某些應用程式具有效率的重要需求。 一般而言，若要判斷資料變更，應用程式開發人員必須使用觸發程序、時間戳記資料行和其他資料表的組合，在其應用程式中實作自訂追蹤方法。 不過，建立這些應用程式通常需要實作大量工作、導致結構描述更新，而且經常會產生很高的效能負擔。

在 IoT 解決方案的案例中，您需要定期將資料從邊緣移至雲端或資料中心，變更追蹤可能會非常有用。 使用者可以快速且有效地查詢上一次同步處理的變更，並將這些變更上傳至雲端或資料中心目標。 如需其他詳細資訊，請參閱 [使用變更資料捕獲或變更追蹤的優點](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)。 

這兩項功能並不相同。 如需詳細資訊，請參閱 [變更資料捕獲與變更追蹤之間的功能差異](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>變更資料擷取

若要瞭解此功能如何運作的詳細資料，請參閱 [關於變更資料捕捉](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

若要瞭解如何啟用或停用這項功能，請參閱 [啟用和停用變更資料捕捉](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)。

若要管理和監視這項功能，請參閱 [管理和監視變更資料捕捉](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)。

若要瞭解如何查詢和使用已變更的資料，請參閱使用 [變更資料](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)。

## <a name="change-tracking"></a>變更追蹤

若要瞭解這項功能如何運作的詳細資訊，請參閱 [關於變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)。

若要瞭解如何啟用或停用這項功能，請參閱 [啟用和停用變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)。

若要管理、監視及管理這項功能，請參閱 [管理和監視變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)。

若要瞭解如何查詢和使用已變更的資料，請參閱使用 [變更資料](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)。

## <a name="temporal-tables"></a>暫存資料表

Azure SQL Edge 也支援 SQL Server 的時態表功能。 這項功能 (也稱為 *系統建立版本的時態表* ，) 提供內建支援，可在任何時間點提供資料表中儲存之資料的相關資訊。 這項功能並不只是提供有關目前時間點正確的資料。

系統建立版本的時態表是一種使用者資料表，其設計目的是為了保留資料變更的完整歷程記錄，並可讓您輕鬆地進行時間點分析。 這種類型的時態表稱為系統建立版本的時態表，因為每個資料列的有效期間是由系統所管理 (也就是資料庫引擎) 。

每個時態表都有兩個明確定義的資料行，每個資料行都有 `datetime2` 資料類型。 這些資料行稱為 *期間* 資料行。 系統會以獨佔方式使用這些期間資料行，以便在每次修改資料列時記錄每個資料列的有效期間。

除了這些期間資料行以外，時態表也包含使用鏡像結構描述的另一個資料表參考。 系統會使用此資料表，在每次時態表中的資料列進行更新或刪除時，自動儲存資料列的先前版本。 此額外資料表稱為歷程 *記錄* 資料表，而儲存目前 (實際) 資料列版本的主資料表稱為 *目前* 資料表，或簡稱為時態表。 在建立時態表期間，使用者可以指定現有的歷程記錄資料表， (必須符合架構) 的規範，或是讓系統建立預設的歷程記錄資料表。

如需詳細資訊，請參閱 [時態表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)。

## <a name="next-steps"></a>後續步驟

- [Azure SQL Edge 中的資料串流 ](stream-data.md)
- [Azure SQL Edge 中使用 ONNX 的機器學習和 AI ](onnx-overview.md)
- [設定複寫至 Azure SQL Edge](configure-replication.md)
- [Azure SQL Edge 中的備份和還原資料庫](backup-restore.md)



