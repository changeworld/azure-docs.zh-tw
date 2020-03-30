---
title: Azure 監視器活頁簿存取控制
description: 使用基於角色的存取控制簡化複雜報告，使用預構建和自訂參數化活頁簿
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658416"
---
# <a name="access-control"></a>存取控制

活頁簿中的存取控制是指兩件事：

* 讀取活頁簿中資料所需的存取權限。 此訪問由活頁簿中使用的資源上的標準[Azure 角色](https://docs.microsoft.com/azure/role-based-access-control/overview)控制。 活頁簿不指定或配置對這些資源的訪問。 使用者通常使用[監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)在這些資源上的角色訪問這些資源。

* 保存活頁簿所需的存取權限

    - 保存私有`("My")`活頁簿不需要額外的許可權。 所有使用者都可以保存專用活頁簿，只有他們才能看到這些活頁簿。
    - 保存共用活頁簿需要資源組中的寫入權限才能保存活頁簿。 這些特權通常由[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色指定，但也可以通過*活頁簿參與者*角色設置。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>具有活頁簿相關許可權的標準角色

[監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)包括監視工具（包括活頁簿）用於從資源讀取資料的標準/讀取權限。

[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)包括各種`/write`監視工具用於保存專案（包括`workbooks/write`保存共用活頁簿的許可權）的一般許可權。
"活頁簿參與者"將"活頁簿/寫入"許可權添加到物件以保存共用活頁簿。
使用者無需特權即可保存只有他們才能看到的私人活頁簿。

對於基於角色的自訂存取控制：

添加`microsoft.insights/workbooks/write`以保存共用的活頁簿。 有關詳細資訊，請參閱[活頁簿參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色。

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
