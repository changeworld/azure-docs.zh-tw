---
title: 設定 azure 系統管理存取的 Azure 角色
titleSuffix: Azure Cognitive Search
description: 角色型存取控制 (RBAC) 在 Azure 入口網站中，用於控制和委派 Azure 認知搜尋管理的管理工作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: c01806194156fb43524e2db5f4c9bf636186de95
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009200"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>設定 Azure 角色以取得 Azure 認知搜尋的系統管理存取權

Azure 特別為透過入口網站或 Resource Manager API 管理的所有服務提供[全域角色型授權模型](../role-based-access-control/role-assignments-portal.md)。 「擁有者」、「參與者」和「讀取者」角色可針對指派給各角色的 Active Directory 使用者、群組和安全性主體，決定*服務管理*層級。 

> [!Note]
> 沒有以角色為基礎的存取控制 (RBAC) 來保護部分的索引或檔子集。 針對搜尋結果的身分識別型存取，您可以建立安全性篩選，依身分識別修剪結果、移除要求者不應具備存取權的文件。 如需詳細資訊，請參閱[安全性篩選](search-security-trimming-for-azure-search.md)和[使用 Active Directory 保護安全](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="management-tasks-by-role"></a>依角色管理工作

針對 Azure 認知搜尋，角色會與支援下列管理工作的許可權層級相關聯：

| 角色 | Task |
| --- | --- |
| 擁有者 |建立或刪除服務或服務上的任何物件，包括 api 索引鍵、索引、索引子、索引子資料來源和索引子排程。<p>檢視服務狀態，包括計數和儲存體大小。<p>新增或刪除角色成員資格 (只有「擁有者」可以管理角色成員資格)。<p>訂用帳戶管理員和服務擁有者在擁有者角色具有自動成員資格。 |
| 參與者 |與擁有者相同的存取層級，減去 Azure 角色管理。 例如，參與者可以建立或刪除物件，或檢視和重新產生 [API 金鑰](search-security-api-keys.md)，但不能修改角色成員資格。 |
| [搜尋服務參與者內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | 等同於參與者角色。 |
| 讀取者 |檢視服務的基本資訊和計量。 此角色的成員無法檢視索引、索引子、資料來源，或索引鍵資訊。  |

角色不會授與服務端點的存取權限。 搜尋服務作業 (例如索引管理、索引母體擴展，以及搜尋資料查詢) 可透過 api-key 而非角色來控制。 如需詳細資訊，請參閱[管理 API 金鑰](search-security-api-keys.md)。

## <a name="permissions-table"></a>許可權資料表

下表摘要說明 Azure 認知搜尋中允許的作業，以及哪些金鑰可解除鎖定特定操作。

| 作業 | 權限 |
|-----------|-------------------------|
| 建立服務 | Azure 訂用帳戶持有者 |
| 調整服務規模 | 資源上的系統管理金鑰、RBAC 擁有者或參與者  |
| 刪除服務 | 資源上的系統管理金鑰、RBAC 擁有者或參與者 |
| 在服務上建立、修改、刪除物件： <br>索引和元件部分 (包括分析器定義、評分設定檔、CORS 選項) 、索引子、資料來源、同義字、建議工具 | 資源上的系統管理金鑰、RBAC 擁有者或參與者 |
| 查詢索引 | 管理或查詢金鑰 (RBAC 不適用) |
| 查詢系統資訊，例如傳回統計資料、計數和物件清單 | 管理金鑰、資源的 RBAC (擁有者、參與者、讀者) |
| 管理管理金鑰 | 管理金鑰、資源的 RBAC 擁有者或參與者 |
| 管理查詢金鑰 |  管理金鑰、資源的 RBAC 擁有者或參與者  |

## <a name="see-also"></a>另請參閱

+ [使用 Powershell 管理](search-manage-powershell.md) 
+ [Azure 認知搜尋中的效能和優化](search-performance-optimization.md)
+ [在 Azure 入口網站中開始使用以角色為基礎的存取控制](../role-based-access-control/overview.md)。