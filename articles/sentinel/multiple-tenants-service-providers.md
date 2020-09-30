---
title: 以受控安全性服務提供者的 Azure Sentinel 管理多個租使用者 |Microsoft Docs
description: 如何使用 Azure Lighthouse 將 Azure Sentinel 中的多個租使用者上架及管理為受控安全性服務提供者 (MSSP) 。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578136"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>以 MSSP 的形式管理 Azure Sentinel 中的多個租使用者

如果您是受控安全性服務提供者 (MSSP) 且您使用 [Azure Lighthouse](../lighthouse/overview.md) 為您的客戶提供安全性作業中心 (SOC) 服務，則您可以直接從自己的 Azure 租使用者管理客戶的 Azure Sentinel 資源，而不需要連線到客戶的租使用者。 

## <a name="prerequisites"></a>必要條件

- [上架 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- 若要讓此作業正常運作，您的租使用者 (MSSP 租使用者) 必須至少在一個訂用帳戶上註冊 Azure Sentinel 資源提供者。 此外，每個客戶的租使用者都必須註冊資源提供者。 如果您已在租使用者中註冊 Azure Sentinel，而您的客戶在其上，則您已準備好開始使用。 若要確認註冊，請執行下列步驟：

    1. 從 Azure 入口網站選取 [ **訂閱** ]，然後從功能表中選取相關的訂用帳戶。

    1. 從訂用帳戶畫面的導覽功能表中，選取 [ **設定**] 下的 [ **資源提供者**]。

    1. 從訂用帳戶 ***名稱* |資源提供者** 畫面上，搜尋並選取 [ *OperationalInsights* ] 和 [ *SecurityInsights*]，然後檢查 [ **狀態** ] 資料行。 如果提供者的狀態為 *NotRegistered*，請選取 [ **註冊**]。
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="檢查資源提供者":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>如何存取受控租使用者中的 Azure Sentinel

1. 在 [ **目錄 + 訂**用帳戶] 下，選取委派的目錄 (Directory = tenant) ，以及客戶 Azure Sentinel 工作區所在的訂用帳戶。

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="檢查資源提供者":::

1. 開啟 Azure Sentinel。 您將會在選取的訂用帳戶中看到所有工作區，而且您可以順暢地使用它們，就像您自己租使用者中的任何工作區一樣。

> [!NOTE]
> 您將無法從受控工作區內的 Azure Sentinel 部署連接器。 若要部署連接器，您必須直接登入您想要部署連接器的租使用者，並使用必要的許可權進行驗證。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何順暢地管理多個 Azure Sentinel 租使用者。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

