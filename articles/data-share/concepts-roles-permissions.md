---
title: Azure Data Share 的角色和需求
description: 瞭解使用 Azure 資料共用共用和接收資料所需的許可權。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265502"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和需求 

本文介紹了使用 Azure 資料共用服務共用和接收資料所需的角色和許可權。 

## <a name="roles-and-requirements"></a>角色和需求

使用 Azure 資料共用服務，無需在資料提供程式和消費者之間交換憑據即可共用資料。 Azure 資料共用服務使用託管標識（以前稱為 MSI）對 Azure 資料存儲進行身份驗證。 

需要授予 Azure 資料共用資源的託管標識對 Azure 資料存儲的存取權限。 然後，Azure 資料共用服務使用此託管標識讀取和寫入資料以進行基於快照的共用，並為就地共用建立符號連結。 

要共用或接收來自 Azure 資料存儲的資料，使用者至少需要以下許可權。 基於 SQL 的共用需要額外的許可權。
* 寫入 Azure 資料存儲的許可權。 通常，此許可權存在於 **"參與者"** 角色中。
* 在 Azure 資料存儲中創建角色指派的許可權。 通常，創建角色指派的許可權存在於 Microsoft**的"擁有者"** 角色、使用者訪問管理員角色或自訂角色中。 如果資料共用資源的託管標識已被授予對 Azure 資料存儲的存取權限，則不需要此許可權。 有關所需角色，請參閱下表。

以下是分配給資料共用資源的託管標識的角色摘要：

| |  |  |
|---|---|---|
|**資料存儲類型**|**資料提供程式來源資料存儲**|**資料消費者目標資料存儲**|
|Azure Blob 儲存體| 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure Data Lake Gen1 | 擁有者 | 不支援
|Azure Data Lake Gen2 | 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure SQL Server | SQL DB 參與者 | SQL DB 參與者
|Azure 資料資源管理器群集 | 參與者 | 參與者
|

對於基於 SQL 的共用，需要從 SQL 資料庫中與 Azure 資料共用資源同名的外部提供程式創建 SQL 使用者。 下面是 SQL 使用者所需的許可權摘要。

| |  |  |
|---|---|---|
|**SQL 資料庫類型**|**資料提供程式 SQL 使用者許可權**|**資料消費者 SQL 使用者許可權**|
|Azure SQL Database | db_datareader | db_datareader，db_datawriter，db_ddladmin
|Azure Synapse Analytics (先前為 SQL DW) | db_datareader | db_datareader，db_datawriter，db_ddladmin
|


### <a name="data-provider"></a>資料提供者 
要在 Azure 資料共用中添加資料集，需要授予提供程式資料共用資源的託管標識對源 Azure 資料存儲的存取權限。 例如，在存儲帳戶中，資料共用資源的託管標識被授予存儲 Blob 資料讀取器角色。 

當使用者通過 Azure 門戶添加資料集且使用者具有適當的許可權時，Azure 資料共用服務會自動完成此操作。 例如，使用者是 Azure 資料存儲的擁有者，或者是具有 Microsoft 的自訂角色的成員。 

或者，使用者可以讓 Azure 資料存儲的擁有者手動將資料共用資源的託管標識添加到 Azure 資料存儲中。 此操作只需執行每個資料共用資源一次。

要為資料共用資源的託管標識創建角色指派，請按照以下步驟操作：

1. 導航到 Azure 資料存儲。
1. 選擇**存取控制 （IAM）。**
1. 選取 [新增角色指派]****。
1. 在 *"角色*"下，選擇上面的角色指派表中的角色（例如，對於存儲帳戶，選擇*存儲 Blob 資料讀取器*）。
1. 在 *"選擇*"下，鍵入 Azure 資料共用資源的名稱。
1. 按一下 [儲存]**。

對於基於 SQL 的源，除了上述步驟外，還需要從 SQL 資料庫中與 Azure 資料共用資源同名的外部提供程式創建 SQL 使用者。 需要授予此使用者*db_datareader*許可權。 在[共用資料](share-your-data.md)教程中可以找到示例腳本以及基於 SQL 的共用的其他先決條件。 

### <a name="data-consumer"></a>資料消費者
要接收資料，需要授予消費者資料共用資源的託管標識對目標 Azure 資料存儲的存取權限。 例如，在存儲帳戶中，資料共用資源的託管標識被授予存儲 Blob 資料參與者角色。 

如果使用者通過 Azure 門戶指定目標資料存儲且使用者具有適當的許可權，則 Azure 資料共用服務會自動完成此操作。 例如，使用者是 Azure 資料存儲的擁有者，或者是具有 Microsoft 的自訂角色的成員。 

或者，使用者可以讓 Azure 資料存儲的擁有者手動將資料共用資源的託管標識添加到 Azure 資料存儲中。 此操作只需執行每個資料共用資源一次。

要手動為資料共用資源的託管標識創建角色指派，請按照以下步驟操作：

1. 導航到 Azure 資料存儲。
1. 選擇**存取控制 （IAM）。**
1. 選取 [新增角色指派]****。
1. 在 *"角色*"下，選擇上面的角色指派表中的角色（例如，對於存儲帳戶，選擇*存儲 Blob 資料讀取器*）。
1. 在 *"選擇*"下，鍵入 Azure 資料共用資源的名稱。
1. 按一下 [儲存]**。

對於基於 SQL 的目標，除了上述步驟外，還需要從 SQL 資料庫中與 Azure 資料共用資源同名的外部提供程式創建 SQL 使用者。 需要授予此使用者*db_datareader、db_datawriter、db_ddladmin*許可權。 示例腳本以及基於 SQL 的共用的其他先決條件可以在[接受和接收資料](subscribe-to-data-share.md)教程中找到。 

如果使用 REST API 共用資料，則需要手動創建這些角色指派。 

要瞭解有關如何添加角色指派的詳細資訊，請參閱[本文檔](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。 

## <a name="resource-provider-registration"></a>資源供應商註冊 

要首次在 Azure 租戶中查看 Azure 資料共用邀請，可能需要手動將 Microsoft.DataShare 資來源提供者註冊到 Azure 訂閱中。 按照以下步驟將 Microsoft.DataShare 資來源提供者註冊到 Azure 訂閱中。 您需要對 Azure 訂閱*的參與者*存取權限來註冊資來源提供者。

1. 在 Azure 門戶中，導航到**訂閱**。
1. 選擇用於 Azure 資料共用的訂閱。
1. 按一下**資來源提供者**。
1. 搜索微軟.資料共用。
1. 按一下 [註冊]****

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)

