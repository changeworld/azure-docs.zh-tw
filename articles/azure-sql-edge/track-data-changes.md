---
title: 在 Azure SQL Edge (預覽) 中追蹤資料變更
description: 了解如何在 Azure SQL Edge (預覽) 中進行變更追蹤和異動資料擷取
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594447"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>在 Azure SQL Edge (預覽) 中追蹤資料變更

Azure SQL Edge 支援兩種 SQL Server 功能，以追蹤資料庫中的資料變更：[變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking)和[異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)。 這些功能可協助應用程式判斷對資料庫中使用者資料表所做的 DML 變更 (插入、更新和刪除作業)。 可在同一個資料庫上啟用異動資料擷取和變更追蹤；無需特殊考量。

在資料庫中查詢已經變更之資料的能力，是讓某些應用程式具有效率的重要需求。 一般而言，若要判斷資料變更，應用程式開發人員必須使用觸發程序、時間戳記資料行和其他資料表的組合，在其應用程式中實作自訂追蹤方法。 不過，建立這些應用程式通常需要實作大量工作、導致結構描述更新，而且經常會產生很高的效能負擔。 在 IoT 解決方案的案例中，如果需要定期將資料從邊緣移至雲端或資料中心，變更追蹤可能非常有用。 這可讓您快速且有效地查詢上一次同步處理的差異變更，並將這些變更上傳至雲端或資料中心目標。 如需其他有關使用變更追蹤和異動資料擷取的優點詳細資訊，請參閱[使用異動資料擷取或變更追蹤的優點](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)。 

若要了解變更追蹤與異動資料擷取之間的功能差異，請參閱[異動資料擷取與變更追蹤之間的功能差異](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>異動資料擷取

若要了解異動資料擷取運作方式的詳細資訊，請參閱[關於異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

若要了解如何啟用或停用異動資料擷取，請參閱[啟用和停用異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

若要管理及監視異動資料擷取，請參閱[管理和監視異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

若要了解如何查詢和使用已變更的資料，請參閱[使用異動資料](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>變更追蹤

若要了解變更追蹤運作方式的詳細資料，請參閱[關於變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)。

若要了解如何啟用或停用變更追蹤，請參閱[啟用和停用變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

若要管理、監視及管理變更追蹤，請參閱[管理和監視變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

若要了解如何查詢和使用已變更的資料，請參閱[使用異動資料](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>時態表

除了支援 SQL Server 的變更追蹤和異動資料擷取功能之外，Azure SQL Edge 也支援 SQL Server 的時態表功能。 時態表 (也稱為系統建立版本的時態表) 是以資料庫功能呈現，其帶入內建支援，提供儲存在資料表中任何時間點資料的相關資訊，而不是只有在目前時間為正確的資料。

系統建立版本的時態表是一種使用者資料表，其設計目的是為了保留資料變更的完整歷程記錄，並允許簡易的時間點分析。 由於每個資料列的有效期間是由系統 (也就是資料庫引擎) 所管理，因此這種類型的時態表稱為系統設定版本的時態表。

每個時態表皆有兩個明確定義的資料行，分別各具 datetime2 資料類型。 這些資料行稱為期間資料行。 這些期間資料行由系統專用，用來在每次修改資料列時記錄每個資料列的有效期間。

除了這些期間資料行以外，時態表也包含使用鏡像結構描述的另一個資料表參考。 系統會使用此資料表，在每次時態表中的資料列進行更新或刪除時，自動儲存資料列的先前版本。 此額外資料表稱為歷程記錄資料表，而儲存目前 (實際) 資料列版本的主資料表稱為目前資料表，或簡稱為時態表。 建立時態表時，使用者可以指定現有的歷程記錄資料表 (必須與結構描述相容)，或讓系統建立預設歷程記錄資料表。

如需時態表的詳細資訊，請參閱[時態表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)。

## <a name="see-also"></a>另請參閱

- [Azure SQL Edge (預覽) 中的資料串流](stream-data.md)
- [Azure SQL Edge (預覽) 中採用 ONNX 的機器學習和 AI ](onnx-overview.md)
- [設定複寫至 Azure SQL Edge (預覽) ](configure-replication.md)
- [在 Azure SQL Edge (預覽) 中備份和還原資料庫](backup-restore.md)



