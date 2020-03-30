---
title: 為 Azure 管理訪問設置 RBAC 角色
titleSuffix: Azure Cognitive Search
description: Azure 門戶中的基於角色的管理控制 （RBAC），用於控制和委派 Azure 認知搜索管理的管理工作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112549"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>設置 RBAC 角色，以便管理訪問 Azure 認知搜索

Azure 特別為透過入口網站或 Resource Manager API 管理的所有服務提供[全域角色型授權模型](../role-based-access-control/role-assignments-portal.md)。 「擁有者」、「參與者」和「讀取者」角色可針對指派給各角色的 Active Directory 使用者、群組和安全性主體，決定*服務管理*層級。 

> [!Note]
> 沒有角色型存取控制項來保護部分索引或文件子集。 針對搜尋結果的身分識別型存取，您可以建立安全性篩選，依身分識別修剪結果、移除要求者不應具備存取權的文件。 如需詳細資訊，請參閱[安全性篩選](search-security-trimming-for-azure-search.md)和[使用 Active Directory 保護安全](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="management-tasks-by-role"></a>依角色管理工作

對於 Azure 認知搜索，角色與支援以下管理工作的權限等級相關聯：

| 角色 | Task |
| --- | --- |
| 擁有者 |建立或刪除服務或服務上的任何物件，包括 api 索引鍵、索引、索引子、索引子資料來源和索引子排程。<p>檢視服務狀態，包括計數和儲存體大小。<p>新增或刪除角色成員資格 (只有「擁有者」可以管理角色成員資格)。<p>訂用帳戶管理員和服務擁有者在擁有者角色具有自動成員資格。 |
| 參與者 |與「擁有者」相同層級的存取權，減去 RBAC 角色管理。 例如，參與者可以建立或刪除物件，或檢視和重新產生 [API 金鑰](search-security-api-keys.md)，但不能修改角色成員資格。 |
| [搜尋服務參與者內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | 等同於參與者角色。 |
| 讀取者 |檢視服務的基本資訊和計量。 此角色的成員無法檢視索引、索引子、資料來源，或索引鍵資訊。  |

角色不會授與服務端點的存取權限。 搜尋服務作業 (例如索引管理、索引母體擴展，以及搜尋資料查詢) 可透過 api-key 而非角色來控制。 如需詳細資訊，請參閱[管理 API 金鑰](search-security-api-keys.md)。

## <a name="see-also"></a>另請參閱

+ [使用 Powershell 管理](search-manage-powershell.md) 
+ [Azure 認知搜索的性能和優化](search-performance-optimization.md)
+ [開始在 Azure 門戶中使用基於角色的存取控制](../role-based-access-control/overview.md)。