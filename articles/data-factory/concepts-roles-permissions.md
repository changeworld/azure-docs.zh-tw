---
title: Azure Data Factory 的角色和權限
description: 說明要建立 Data Factory 及使用子資源所需的角色和權限。
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: 70be8d8be48f2b1e1cc275c06e4abff09e3e62f6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498569"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Azure Data Factory 的角色和權限

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


本文說明要建立和管理 Azure Data Factory 資源所需的角色，以及這些角色所授與的權限。

## <a name="roles-and-requirements"></a>角色和需求

若要建立 Data Factory 實例，您用來登入 Azure 的使用者帳戶必須是「 *參與者* 」角色、「 *擁有* 者」角色的成員，或是 Azure 訂用帳戶的 *系統管理員* 。 若要檢視您在訂用帳戶中擁有的權限，請在 Azure 入口網站中選取右上角的使用者名稱，然後選取 [權限]。 如果您有多個訂用帳戶的存取權，請選取適當的訂用帳戶。 

若要建立及管理 Data factory 的子資源 (包括資料集、連結服務、管線、觸發程序和整合執行階段)，必須要符合下列需求：
- 若要在 Azure 入口網站中建立及管理子資源，您必須屬於 **資源群組** 層級或以上的 **Data Factory 參與者** 角色。
- 若要使用 PowerShell 或 SDK 來建立及管理子資源，具備資源層級或更高層級的 **參與者** 角色即已足夠。

如需將使用者新增至角色的範例指示，請參閱[新增角色](../cost-management-billing/manage/add-change-subscription-administrator.md)一文。

## <a name="set-up-permissions"></a>設定權限

在建立 Data Factory 之後，您可以讓其他使用者使用資料處理站。 若要將此存取權授與其他使用者，您必須將這些使用者新增至包含 Data Factory 之 **資源群組** 上的內建 **Data Factory 參與者** 角色。

### <a name="scope-of-the-data-factory-contributor-role"></a>Data Factory 參與者角色的範圍

**Data Factory 參與者** 角色的成員資格可讓使用者執行下列動作：
- 建立、編輯和刪除資料處理站與子資源，包括資料集、連結的服務、管線、觸發程序和整合執行階段。
- 部署 Resource Manager 範本。 Resource Manager 部署是 Data Factory 在 Azure 入口網站中所使用的部署方法。
- 管理資料處理站的 App Insights 警示。
- 建立支援票證。

如需此角色的詳細資訊，請參閱 [Data Factory 參與者角色](../role-based-access-control/built-in-roles.md#data-factory-contributor)。

### <a name="resource-manager-template-deployment"></a>Resource Manager 範本部署

位於資源群組層級以上的 **Data Factory 參與者** 角色可讓使用者部署 Resource Manager 範本。 因此，該角色的成員可以使用 Resource Manager 範本來部署資料處理站及其子資源，包括資料集、連結的服務、管線、觸發程序和整合執行階段。 此角色的成員資格不會讓使用者建立其他資源。

Azure Repos 和 GitHub 上的權限與 Data Factory 權限無關。 因此，具備存放庫權限的使用者只能是讀者角色的成員，該使用者可以編輯 Data Factory 子資源，並將變更認可至存放庫，但無法發佈這些變更。

> [!IMPORTANT]
> 使用 **Data Factory 參與者** 角色來部署 Resource Manager 範本並不會提高您的權限。 例如，如果您部署範本來建立 Azure 虛擬機器，但您沒有建立虛擬機器的權限，則部署會因為授權錯誤而失敗。

### <a name="custom-scenarios-and-custom-roles"></a>自訂案例和自訂角色

有時候，您可能需要對不同的資料處理站使用者授與不同的存取層級。 例如：
- 您可能需要使用者只有特定資料處理站權限的群組。
- 或者，您可能需要使用者只能監視而無法修改資料處理站 (一或多個) 的群組。

藉由建立自訂角色並將使用者指派給這些角色，即可實現這些自訂案例。 如需自訂角色的詳細資訊，請參閱 [Azure 中的自訂角色](..//role-based-access-control/custom-roles.md)。

以下幾個範例會示範您可以使用自訂角色實現的案例：

- 讓使用者從 Azure 入口網站在資源群組中建立、編輯或刪除任何資料處理站。

  對使用者指派資源群組層級的內建 **Data Factory 參與者** 角色。 如果您想要允許使用者存取訂閱中的任何資料處理站，請指派訂閱層級的角色。

- 讓使用者檢視 (讀取) 和監視資料處理站，但不能編輯或變更。

  對使用者指派資料處理站資源的內建 **讀取者** 角色。

- 讓使用者在 Azure 入口網站中編輯單一資料處理站。

  此案例需要指派兩個角色。

  1. 指派資料處理站層級的內建 **參與者** 角色。
  2. 建立具有 **Microsoft.Resources/deployments/** 權限的自訂角色。 在資源群組層級對使用者指派這個自訂角色。

- 讓使用者能夠在連結服務中測試連接，或在資料集中預覽資料

    建立具有下列動作許可權的自訂角色： **DataFactory/factory/getFeatureValue/read** 和 **DataFactory/factory/getDataPlaneAccess/action**。 對使用者指派資料處理站資源的自訂角色。

- 讓使用者從 PowerShell 或 SDK 更新資料處理站，而不是在 Azure 入口網站中進行。

  對使用者指派資料處理站資源的內建 **參與者** 角色。 此角色可讓使用者在 Azure 入口網站中查看資源，但使用者無法存取 [發佈] 和 [全部發佈] 按鈕。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)

- 深入了解 **Data Factory 參與者** 角色 - [Data Factory 參與者角色](../role-based-access-control/built-in-roles.md#data-factory-contributor)。
