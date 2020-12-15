---
title: Synapse 工作區存取控制概觀
description: 本文說明用來控制 Synapse 工作區的存取權，以及所包含的資源和程式碼成品的機制。
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 36c4ce34d6b0c4df119d5531cda725605cfd0bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523712"
---
# <a name="synapse-access-control"></a>Synapse 存取控制 

本文概述可用來控制 Synapse 計算資源和資料存取權的機制。  

## <a name="overview"></a>總覽

Synapse 提供完整且更細微的存取控制系統，整合了： 
- **Azure 角色** 以進行資源管理，並可存取儲存體中的資料、 
- **Synapse 角色** 可管理程式碼即時存取和執行， 
- **SQL 角色**，讓資料平面存取 SQL 集區中的資料，以及 
- 適用於原始程式碼控制的 **Git 權限**，包括持續整合和部署支援。  

Synapse 角色會提供可在不同範圍套用的權限集合。 這種細微程度可讓您輕鬆地將適當的存取權授與管理員、開發人員、安全性人員和操作員，以計算資源和資料。

您可以使用與人員作業角色一致的安全性群組來簡化存取控制。  您只需要從適當的安全性群組新增和移除使用者，以管理存取權。

## <a name="access-control-elements"></a>存取控制元素

### <a name="creating-and-managing-synapse-resources"></a>建立和管理 Synapse 資源

Azure 角色可用來控制管理： 
- 專用 SQL 集區、 
- Apache Spark 集區和 
- 整合執行階段。 

若要建立這些資源，您必須是資源群組的 Azure 擁有者或參與者。  若要管理這些資源，您必須是資源群組或個別資源的 Azure 擁有者或參與者。 

### <a name="developing-and-executing-code-in-synapse"></a>在 Synapse 中開發和執行程式碼 

Synapse 支援兩種開發模型。

- **Synapse 即時開發**。  您會在 Synapse Studio 中開發和偵錯程式碼，然後 **發佈** 以儲存並執行。  Synapse 服務是程式碼編輯和執行的實際來源。  若關閉 Synapse Studio，任何未發佈的工作都會遺失。  
- **啟用 Git 的開發**。 您會在 Synapse Studio 中開發和偵錯程式碼，並 **認可** Git 存放庫工作分支的變更。 來自一或多個分支的工作已整合到共同作業分支中，您從此位置 **將其發佈至服務**。  Git 存放庫是程式碼編輯的實際來源，而服務是執行的實際來源。 您必須先將變更認可至 Git 存放庫，或在關閉 Synapse Studio 之前發佈至服務。 [深入了解](https://go.microsoft.com/fwlink/?linkid=2150100)透過 Git 使用 Synapse Analytics。

在這兩種開發模型中，具有 Synapse Studio 存取權的任何使用者都可以建立程式碼成品。  不過，您需要額外的權限才能將成品發佈至服務、讀取已發佈的成品、將變更認可至 Git、執行程式碼，以及存取受認證保護的連結資料。

### <a name="synapse-roles"></a>Synapse 角色

Synapse 角色用來控制 Synapse 服務的存取權，讓您能夠： 
- 列出已發佈的程式碼成品、 
- 發佈程式碼成品、連結的服務和認證定義、
- 執行程式碼或使用 Synapse 計算資源的管線、
- 執行程式碼或管線，以存取受認證保護的連結資料、
- 檢視與已發佈之程式碼成品相關聯的輸出、
- 監視計算資源狀態，並檢視執行階段記錄。

您可以在工作區範圍或更細微的範圍指派 Synapse 角色，以限制授與特定 Synapse 資源的權限。

### <a name="git-permissions"></a>Git 權限

在 Git 模式中使用啟用 Git 的開發時，您的 Git 權限會控制是否可以讀取和認可程式碼成品的變更，包括已連結的服務和認證定義。   
   
### <a name="accessing-data-in-sql"></a>存取 SQL 中的資料

使用專用和無伺服器 SQL 集區時，會使用 SQL 權限來控制資料平面存取。 

系統會指派工作區的建立者為工作區的 Active Directory 管理員。  建立之後，此角色可以指派給不同的使用者或 Azure 入口網站中的安全性群組。

**無伺服器 SQL 集區**：系統會授與 Synapse 管理員無伺服器 SQL 集區「內建」的 `db_owner` (`DBO`) 權限。 若要將無伺服器 SQL 集區的存取權授與其他使用者，Synapse 管理員需要在每個無伺服器集區上執行 SQL 指令碼。  

**專用 SQL 集區**：系統會將 Active Directory 管理員權限授與工作區和工作區 MSI 的建立者；  除非自動授與，否則系統不會授與存取專用 SQL 集區的權限。 若要將專用 SQL 集區的存取權授與其他使用者或群組，Active Directory 管理員必須針對每個專用的 SQL 集區執行 SQL 指令碼。

如需在 SQL 集區中授與 SQL 權限的 SQL 指令碼範例，請參閱[如何設定 Synapse 存取控制](./how-to-set-up-access-control.md)。  

 ### <a name="accessing-system-managed-data-in-storage"></a>存取儲存體中的受控資料

無伺服器 SQL 集區和 Apache Spark 資料表會將其資料儲存在與工作區相關聯的 ADLS Gen2 容器中。  系統必須授與使用者和工作區 MSI 此 ADLS Gen2 儲存體容器之儲存體 Blob 資料參與者存取權。  

## <a name="using-security-groups-as-a-best-practice"></a>使用安全性群組作為最佳做法

若要簡化管理存取控制，您可以使用安全性群組將角色指派給個人和群組。 您可以建立安全性群組，以鏡像方式處理組織中需要存取 Synapse 資源或成品的角色或工作函式。  然後，可以將一或多個 Azure 角色、Synapse 角色、SQL 權限或 Git 權限指派給這些角色型安全性群組。 使用妥善選擇的安全性群組，將所需的權限新增至適當的安全性群組，即可輕鬆地將其指派給使用者。 

>[!Note]
>如果使用安全性群組來管理存取權，則 Azure Active Directory 會出現額外的延遲，之後變更才會生效。 

## <a name="access-control-enforcement-in-synapse-studio"></a>Synapse Studio 中的存取控制強制執行

根據您的權限和目前的模式，Synapse Studio 的行為會有所不同：
- **Synapse 即時模式：** 如果您沒有必要的權限，Synapse Studio 不會讓您看到已發佈的內容，也不會讓您發佈內容或採取其他動作。  在某些情況下，您無法建立不能使用或無法儲存的程式碼成品。 
- **Git 模式：** 如果您有可讓您將變更認可到最新分支的 Git 權限，則即使沒有將變更發佈至即時服務的權限，系統仍會允許認可動作。  

在某些情況下，即使您沒有發佈或認可的權限，也可以建立程式碼成品。  這可讓您執行程式碼 (透過必要的執行權限)。[深入了解](./synapse-workspace-understand-what-role-you-need.md)一般工作所需的角色。 

如果已停用 Synapse Studio 中的功能，工具提示會指出所需的權限。  使用 [Synapse RBAC 角色指南](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them)查詢所需的角色，以提供缺少的權限。


## <a name="next-steps"></a>後續步驟

- 深入了解 [Synapse RBAC](./synapse-workspace-synapse-rbac.md)
- 深入了解 [Synapse RBAC 角色](./synapse-workspace-synapse-rbac-roles.md)
- 了解[如何使用安全性群組來設定 Synapse 工作區的存取控制](./how-to-set-up-access-control.md)。
- 了解[如何檢閱 Synapse RBAC 角色指派](./how-to-review-synapse-rbac-role-assignments.md)
- 了解[如何管理 Synapse RBAC 角色指派](./how-to-manage-synapse-rbac-role-assignments.md)