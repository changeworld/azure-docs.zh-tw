---
title: 使用 Azure AD 中的條件訪問管理對 Azure 管理的訪問
description: 瞭解如何在 Azure AD 中使用條件訪問來管理對 Azure 管理的訪問。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137418"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用條件訪問管理對 Azure 管理的訪問

> [!CAUTION]
> 在設置策略來管理對 Azure 管理的訪問之前，請確保瞭解條件訪問的工作原理。 請確定您未建立可能會封鎖您自己存取入口網站的條件。

Azure 活動目錄 （Azure AD） 中的條件訪問根據指定的特定條件控制對雲應用的訪問。 要允許訪問，請創建條件訪問策略，根據是否滿足策略中的要求來允許或阻止訪問。 

通常，您可以使用條件訪問來控制對雲應用的訪問。 您也可以設定原則，以根據特定條件 (例如登入風險、位置或裝置) 來控制 Azure 管理的存取，以及強制執行各項需求 (如多重要素驗證)。

若要建立 Azure 管理的原則，您可在選擇要套用原則的應用程式時，選取 [雲端應用程式]**** 之下的 [Microsoft Azure 管理]****。

![Azure 管理的條件式存取](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

您創建的策略適用于所有 Azure 管理終結點，包括：

- Azure 入口網站
- Azure 資源管理器提供程式
- 經典服務管理 API
- Azure PowerShell
- 視覺化工作室訂閱管理員門戶
- Azure DevOps
- Azure 資料工廠門戶

請注意，此原則適用於可呼叫 Azure Resource Manager API 的 Azure PowerShell。 不適用於呼叫 Microsoft Graph 的[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。


有關如何設置和使用條件訪問的詳細資訊，請參閱[Azure 活動目錄中的條件訪問](../active-directory/active-directory-conditional-access-azure-portal.md)。
