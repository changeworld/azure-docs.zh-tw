---
title: 使用 Azure AD 中的條件式存取來管理 Azure 管理的存取權
description: 瞭解如何在 Azure AD 中使用條件式存取來管理 Azure 管理的存取權。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137418"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用條件式存取來管理 Azure 管理的存取權

> [!CAUTION]
> 設定原則來管理 Azure 管理的存取權之前，請確定您瞭解條件式存取的運作方式。 請確定您未建立可能會封鎖您自己存取入口網站的條件。

Azure Active Directory （Azure AD）中的條件式存取會根據您指定的特定條件，控制對雲端應用程式的存取。 若要允許存取，您可以根據是否符合原則中的需求，建立允許或封鎖存取的條件式存取原則。 

通常，您會使用條件式存取來控制雲端應用程式的存取。 您也可以設定原則，以根據特定條件 (例如登入風險、位置或裝置) 來控制 Azure 管理的存取，以及強制執行各項需求 (如多重要素驗證)。

若要建立 Azure 管理的原則，您可在選擇要套用原則的應用程式時，選取 [雲端應用程式]**** 之下的 [Microsoft Azure 管理]****。

![Azure 管理的條件式存取](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

您所建立的原則會套用至所有的 Azure 管理端點，包括下列各項：

- Azure 入口網站
- Azure Resource Manager 提供者
- 傳統服務管理 Api
- Azure PowerShell
- Visual Studio 訂閱系統管理員入口網站
- Azure DevOps
- Azure Data Factory 入口網站

請注意，此原則適用於可呼叫 Azure Resource Manager API 的 Azure PowerShell。 不適用於呼叫 Microsoft Graph 的[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。


如需如何設定和使用條件式存取的詳細資訊，請參閱[Azure Active Directory 中的條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)。
