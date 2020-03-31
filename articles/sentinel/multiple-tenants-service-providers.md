---
title: 與多個租戶一起為 MSSP 服務提供者使用 Azure 哨兵*微軟文檔
description: 如何使用多個租戶到 MSSP 服務提供者的 Azure Sentinel。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476010"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>在 Azure 哨兵中處理多個租戶 

如果您是託管安全服務提供者 （MSSP），並且使用[Azure 燈塔](../lighthouse/overview.md)來管理客戶的安全操作中心 （SOC），則可以管理客戶的 Azure Sentinel 資源，而無需直接從您自己的 Azure 租戶連接到客戶的租戶。 

## <a name="prerequisites"></a>Prerequisites
- [板載 Azure 燈塔](../lighthouse/how-to/onboard-customer.md)
- 要正常工作，租戶必須在至少一個訂閱上註冊到 Azure Sentinel 資來源提供者。 如果租戶中已註冊了 Azure Sentinel，則已準備好開始。 如果沒有，請從 Azure 門戶中選擇 **"訂閱"，** 後跟**資來源提供者**。  然後，從**SOC -資來源提供者**螢幕中，搜索並選擇`Microsoft.OperationalInsights``Microsoft.SecurityInsights`和 ，然後選擇"**註冊**"。
   ![檢查資來源提供者](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>如何從其他租戶訪問 Azure 哨兵
1. 在 **"目錄 + 訂閱**"下，選擇委派的目錄以及客戶的 Azure Sentinel 工作區所在的訂閱。

   ![產生安全性事件](media/multiple-tenants-service-providers/directory-subscription.png)

1. 打開 Azure 哨兵。 您將看到所選訂閱中的所有工作區，並且您將能夠像您自己的租戶中的任何工作區一樣無縫地使用它們。

> [!NOTE]
> 您將無法從託管工作區中部署 Azure Sentinel 中的連接器。 要部署連接器，必須直接登錄到要部署連接器的租戶，並在那裡使用所需的許可權進行身份驗證。





## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何無縫管理多個 Azure Sentinel 租戶。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

