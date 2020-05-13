---
title: 包含檔案
description: 包含檔案
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: c53486bf3368039f172c7a13420e2291dd9c9892
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122627"
---
1. 從 Azure 入口網站功能表或[首頁]  頁面，選取 [建立資源]  。

1. 在 [新增]  頁面中，選取 [計算]   > [函數應用程式]  。

1. 在 [**基本**] 頁面上，使用下表中所指定的函數應用程式設定：

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | 要在其中建立函式應用程式的新資源群組名稱。 |
    | **函式應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元為 `a-z` (區分大小寫)、`0-9` 以及 `-`。  |
    |**Publish**| 程式碼 | 發佈程式碼檔案或 Docker 容器的選項。 |
    | **執行階段堆疊** | 慣用語言 | 選擇支援您慣用函式程式設計語言的執行階段。 選擇 [適用于 c # 的 **.net** ] 和 [F # 函式]。 |
    |**區域**| 慣用區域 | 選擇與您接近的[區域](https://azure.microsoft.com/regions/)，或選擇與函式將會存取之其他服務接近的區域。 |

    ![基本頁面](./media/functions-premium-create/function-app-create-basics.png)

1. 選取 **[下一步：裝載]**。 在 [**裝載**] 頁面上，輸入下列設定：

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **[儲存體帳戶](../articles/storage/common/storage-account-create.md)** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    |**作業系統**| 慣用的作業系統 | 系統會根據您的執行階段堆疊選項預先選取作業系統，但您可以視需要變更設定。 |
    | **[規劃](../articles/azure-functions/functions-scale.md)** | Premium | 會定義如何將資源配置給函式應用程式的主控方案。 選取 [ **Premium**]，然後選取 [ **Windows 方案**] 和 [ **Sku 和大小**] 的預設值。 |

    ![裝載頁面](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. 選取 **[下一步：監視]**。 在 [**監視**] 頁面上，輸入下列設定：

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 預設 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 藉由展開此設定，您可以變更**新的資源名稱**，或選擇[Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置中的不同**位置**來儲存您的資料。 |

    ![[監視] 頁面](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. 選取 [檢閱 + 建立]  ，以檢閱應用程式組態選項。

1. 在 [檢閱 + 建立]  頁面中檢閱您的設定，然後選取 [建立]  來佈建和部署函式應用程式。

1. 選取入口網站右上角的 [**通知**] 圖示，並監看是否有**部署成功**訊息。

1. 選取 [前往資源]  ，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]  。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。

    ![部署通知](./media/functions-premium-create/function-app-create-notification2.png)