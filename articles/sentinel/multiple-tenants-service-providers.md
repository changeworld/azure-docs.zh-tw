---
title: 使用多個租使用者來 Azure Sentinel MSSP 服務提供者 |Microsoft Docs
description: 如何使用多個租使用者來 Azure Sentinel MSSP 服務提供者。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476010"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>在 Azure Sentinel 中使用多個租使用者 

如果您是受控安全性服務提供者（MSSP），而且您使用[Azure 燈塔](../lighthouse/overview.md)來管理客戶的安全性作業中心（SOC），您將能夠管理客戶的 Azure Sentinel 資源，而不需要直接從自己的 Azure 租使用者連線至客戶的租使用者。 

## <a name="prerequisites"></a>先決條件
- [將 Azure 燈塔上架](../lighthouse/how-to/onboard-customer.md)
- 若要讓此作業正常運作，您的租使用者必須至少在一個訂用帳戶上註冊到 Azure Sentinel 資源提供者。 如果您的租使用者中有已註冊的 Azure Sentinel，您就可以開始著手。 如果不是，**請從 Azure 入口網站**中選取 [訂用帳戶]，後面接著**資源提供者**。  然後，在 [ **SOC-資源提供者**] 畫面中，搜尋`Microsoft.OperationalInsights`並`Microsoft.SecurityInsights`選取 [和]，然後選取 [**註冊**]。
   ![檢查資源提供者](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>如何從其他租使用者存取 Azure Sentinel
1. 在 [**目錄 + 訂**用帳戶] 下，選取委派的目錄，以及客戶的 Azure Sentinel 的工作區所在的訂閱。

   ![產生安全性事件](media/multiple-tenants-service-providers/directory-subscription.png)

1. 開啟 Azure Sentinel。 您會看到所選訂用帳戶中的所有工作區，而且您將能夠順暢地使用它們，就像您自己租使用者中的任何工作區一樣。

> [!NOTE]
> 您將無法從受管理的工作區，在 Azure Sentinel 中部署連接器。 若要部署連接器，您必須直接登入您想要部署連接器的租使用者，並使用必要的許可權進行驗證。





## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何順暢地管理多個 Azure Sentinel 租使用者。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

