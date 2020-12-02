---
title: '在專用的 SQL 集區上啟用 Synapse 工作區功能 (先前為 SQL DW) '
description: 本檔說明客戶如何在工作區中存取和使用其現有的 SQL DW 獨立實例。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f3c40e4c7b00a5c78872a60af25e3b19fe08f324
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466343"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>在現有的專用 SQL 集區上啟用 Synapse 工作區功能 (先前為 SQL DW) 

所有 SQL 資料倉儲客戶現在都可以透過 Synapse Studio 和工作區，存取和使用現有的專用 SQL 集區 (先前的 SQL DW) 實例，而不會影響自動化、連接或工具。 本文說明現有的 Azure Synapse Analytics 客戶如何利用現在可透過 Synapse 工作區和 Studio 取得的新功能豐富功能，來建立及擴充其現有的分析解決方案。   

## <a name="experience"></a>體驗
 
現在 Synapse 工作區已正式推出，DW 入口網站總覽區段中有提供新的功能，可讓您為現有的專用 SQL 集區建立 Synapse 工作區， (先前的 SQL DW) 實例。 這項新功能可讓您將裝載現有資料倉儲實例的邏輯伺服器，連接到新的 Synapse 工作區。 連線可確保在該伺服器上裝載的所有資料倉儲都可從工作區和 Studio 存取，並可與 Synapse 合作夥伴服務搭配使用 (無伺服器 SQL 集區、Apache Spark 集區和 ADF) 。 當布建步驟已完成，而且已建立新建立的工作區的連線時，您就可以開始存取和使用您的資源。  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="連線的 Synapse 工作區":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>使用 Synapse workspace 和 Studio 功能來存取和使用專用的 SQL 集區 (先前的 SQL DW) 
 
使用已啟用 Synapse 工作區功能的專用 SQL DW (先前的 SQL DW) 時，將適用下列資訊： 
- **SQL 功能** 啟用 Synapse 工作區功能之後，所有 SQL 功能都會保留在邏輯 SQL server 中。 啟用工作區之後，仍然可以透過 SQL 資源提供者存取伺服器。 所有管理功能都可以透過工作區起始，而作業將會在裝載 SQL 集區的邏輯 SQL Server 上進行。 啟用工作區時，不會中斷或中斷任何現有的自動化、工具或連接。  
- **資源移動**  在啟用 Synapse 工作區功能的伺服器上起始資源移動時，伺服器和工作區之間的連結將會中斷，而且您將無法再從工作區存取現有的專用 SQL 集區 (先前的 SQL DW) 實例。 為確保會保留連線，建議將這兩個資源保留在相同的訂用帳戶和資源群組中。 
- **監視** 透過 Synapse Studio 在已啟用專用 SQL 集區的工作區中提交的 SQL 要求 (先前為 SQL DW) 可在監視器中樞內查看。 針對所有其他監視活動，您可以移至 Azure 入口網站的專用 SQL 集區 (先前的 SQL DW) 監視。 
- 如上面所述的 **安全性** 與 **存取控制**，sql server 的所有管理功能和專用的 sql 集區 (先前的 sql DW) 實例將繼續位於邏輯 SQL server 上。 這些功能包括防火牆規則管理、設定伺服器的 Azure AD 系統管理員，以及您專用 SQL 集區中資料的所有存取控制 (先前為 SQL DW) 。 您必須採取下列步驟，以確保您專用的 SQL 集區 (先前的 SQL DW) 可供存取，且可透過 Synapse 工作區使用。 工作區角色成員資格不會將專用 SQL 集區中的資料許可權提供給使用者 (先前為 SQL DW) 實例。 遵循您一般的 [sql 驗證](sql-data-warehouse-authentication.md) 原則，以確保使用者可以在邏輯伺服器上 (先前的 sql DW) 實例來存取專用的 sql 集區。 

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 已連線的工作區 Synapse Studio 會根據使用者在 Azure 中擁有的許可權，顯示專用集區的名稱。 如果使用者沒有 SQL 集區的許可權，將無法存取集區下的物件。 

- **網路安全性** 如果您在現有的專用 SQL 集區上啟用的 Synapse 工作區 (先前的 SQL DW) 已啟用資料滲透保護。 建立從工作區到邏輯 SQL server 的受控私人端點連線。 核准私人端點連線要求，以允許伺服器和工作區之間的通訊。
- **Studio****Data hub** 中的 sql 集區已啟用專用的 sql 集區 (先前的 sql DW) 可透過資料中樞的工具提示來識別。 
- **建立新的專用 sql 集區 (先前的 SQL DW)** 啟用工作區功能之後，您可以透過 Synapse 工作區和 Studio 建立新的專用 SQL 集區，並在邏輯 SQL server 上進行新的集區布建。 當布建完成時，新的資源會出現在入口網站和 Studio 中。      

## <a name="next-steps"></a>後續步驟
在現有的專用 SQL 集區上啟用 [Synapse 工作區功能](workspace-connected-create.md) (先前為 SQL DW) 