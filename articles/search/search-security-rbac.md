---
title: 設定 azure 系統管理存取的 Azure 角色
titleSuffix: Azure Cognitive Search
description: Azure 角色型存取控制 (Azure RBAC) 在 Azure 入口網站中，用來控制和委派管理 Azure 認知搜尋管理的管理工作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519494"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>設定適用于系統管理存取權的 Azure 角色 Azure 認知搜尋

Azure 特別為透過入口網站或 Resource Manager API 管理的所有服務提供[全域角色型授權模型](../role-based-access-control/role-assignments-portal.md)。 「擁有者」、「參與者」和「讀取者」角色可針對指派給各角色的 Active Directory 使用者、群組和安全性主體，決定*服務管理*層級。 

> [!Note]
> 沒有 Azure 角色型存取控制 (Azure RBAC) 來保護服務的內容。 您將使用管理員 API 金鑰或查詢 API 金鑰，來取得對服務本身的已驗證要求。 針對搜尋結果的身分識別型存取，您可以建立安全性篩選，依身分識別修剪結果、移除要求者不應具備存取權的文件。 如需詳細資訊，請參閱 [安全性篩選](search-security-trimming-for-azure-search.md)。

## <a name="management-tasks-by-role"></a>依角色管理工作

針對 Azure 認知搜尋，角色會與支援下列管理工作的許可權層級相關聯：

| 角色 | Task |
| --- | --- |
| 擁有者 |建立或刪除服務或服務上的任何物件，包括 api 索引鍵、索引、索引子、索引子資料來源和索引子排程。<p>檢視服務狀態，包括計數和儲存體大小。<p>新增或刪除角色成員資格 (只有「擁有者」可以管理角色成員資格)。<p>訂用帳戶管理員和服務擁有者在擁有者角色具有自動成員資格。 |
| 參與者 | 與擁有者相同層級的存取權，減去 Azure 角色管理。 例如，參與者可以建立或刪除物件，或檢視和重新產生 [API 金鑰](search-security-api-keys.md)，但不能修改角色成員資格。<br><br>[搜尋服務參與者](../role-based-access-control/built-in-roles.md#search-service-contributor) 相當於一般參與者內建角色。 |
| 讀取者 |View service essentials，例如服務端點、訂用帳戶、資源群組、區域、層和容量。 您也可以在 [監視] 索引標籤上，查看服務計量，例如每秒的平均查詢數。此角色的成員無法查看索引、索引子、資料來源或技能集資訊。 這包括這些物件的使用方式資料，例如服務上有多少個索引。 |

角色不會授與服務端點的存取權限。 搜尋服務作業 (例如索引管理、索引母體擴展，以及搜尋資料查詢) 可透過 api-key 而非角色來控制。 如需詳細資訊，請參閱[管理 API 金鑰](search-security-api-keys.md)。

## <a name="permissions-table"></a>許可權資料表

下表摘要說明 Azure 認知搜尋中允許的作業，以及哪些索引鍵會解除鎖定特定作業的存取。

Azure RBAC 許可權適用于入口網站作業和服務管理 (建立、刪除或變更服務或其 API 金鑰) 。 API 金鑰會在服務存在之後建立，並套用至服務上的內容作業。 此外，在入口網站中的內容相關作業（例如建立或刪除物件）中，Azure RBAC 擁有者或參與者會使用隱含的管理 API 金鑰與服務互動。

| 作業 | 控制者 |
|-----------|-------------------------|
| 建立服務 | Azure RBAC 許可權：擁有者或參與者 |
| 調整服務規模 | Azure RBAC 許可權：擁有者或參與者|
| 刪除服務 | Azure RBAC 許可權：擁有者或參與者 |
| 管理系統管理員或查詢金鑰 | Azure RBAC 許可權：擁有者或參與者|
| 在入口網站或管理 API 中查看服務資訊 | Azure RBAC 許可權：擁有者、參與者或讀者  |
| 在入口網站或管理 API 中查看物件資訊和計量 | Azure RBAC 許可權：擁有者或參與者 |
| 在服務上建立、修改、刪除物件： <br>索引和元件部分 (包括分析器定義、評分設定檔、CORS 選項) 、索引子、資料來源、同義字、建議工具 | 使用 API 時的管理金鑰，Azure RBAC 擁有者或參與者（如果使用入口網站） |
| 查詢索引 | 系統管理員或查詢金鑰（如果使用 API）、Azure RBAC 擁有者或參與者（如果使用入口網站） |
| 查詢物件的相關系統資訊，例如傳回統計資料、計數和物件清單 | 使用 API 時的管理金鑰，Azure RBAC 擁有者或參與者（如果使用入口網站） |

## <a name="next-steps"></a>後續步驟

+ [使用 Powershell 管理](search-manage-powershell.md) 
+ [Azure 認知搜尋中的效能和優化](search-performance-optimization.md)
+ [什麼是 azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)。
