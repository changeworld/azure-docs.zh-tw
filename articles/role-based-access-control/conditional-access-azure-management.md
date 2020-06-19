---
title: 使用 Azure AD 中的條件式存取來管理 Azure 管理的存取權
description: 了解如何使用 Azure AD 中的條件式存取來管理 Azure 管理的存取權。
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
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758770"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用條件式存取來管理 Azure 管理的存取權

> [!CAUTION]
> 在設定原則來管理 Azure 管理的存取權之前，務必了解條件式存取的運作方式。 請確定您未建立可能會封鎖您自己存取入口網站的條件。

Azure Active Directory (Azure AD) 中的條件式存取會根據您指定的特定條件，控制雲端應用程式的存取權。 若要允許存取，您可以建立條件式存取原則，以根據原則中的需求是否相符來允許或封鎖存取。 

一般而言，您可以使用條件式存取來控制雲端應用程式的存取權。 您也可以設定原則，以根據特定條件 (例如登入風險、位置或裝置) 來控制 Azure 管理的存取，以及強制執行各項需求 (如多重要素驗證)。

若要建立 Azure 管理的原則，您可在選擇要套用原則的應用程式時，選取 [雲端應用程式] 之下的 [Microsoft Azure 管理]。

![Azure 管理的條件式存取](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

您建立的原則會套用至所有 Azure 管理端點，包括下列各項：

- Azure 入口網站
- Azure Resource Manager 提供者
- 傳統服務管理 API
- Azure PowerShell
- Visual Studio 訂閱管理員入口網站
- Azure DevOps
- Azure Data Factory 入口網站

請注意，此原則適用於可呼叫 Azure Resource Manager API 的 Azure PowerShell。 不適用於呼叫 Microsoft Graph 的[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

如需如何設定範例原則以對 Microsoft Azure 管理啟用條件式存取的詳細資訊，請參閱[條件式存取：需要 Azure 管理的 MFA](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)一文。
