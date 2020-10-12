---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84730975"
---
Azure Functions 可讓您輕鬆地從 [Azure 入口網站]中，將 Application Insights 整合新增至函式應用程式。

1. 在 [Azure 入口網站][Azure 入口網站]中，搜尋並選取**函式應用程式**，然後選擇您的函式應用程式。 

1. 選取視窗頂端的 [未設定 Application Insights] 橫幅。 如果您看不到此橫幅，則您的應用程式可能已經啟用 Application Insights。

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="從入口網站啟用 Application Insights":::

1. 使用下表中指定的設定，展開 [變更您的資源] 並建立 Application Insights 資源。  

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新資源名稱** | 唯一的應用程式名稱 | 最簡單的方式是使用與您函式應用程式一樣的名稱，而這必須是您訂用帳戶中唯一的名稱。 | 
    | **位置** | 西歐 | 可能的話，請使用與函式應用程式相同的[區域](https://azure.microsoft.com/regions/)，或該區域附近的區域。 |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="從入口網站啟用 Application Insights":::

1. 選取 [套用]。 

   Application Insights 資源會建立在函式應用程式所在的資源群組和訂用帳戶。 建立資源之後，請關閉 Application Insights 視窗。

1. 在您的函式應用程式中，選取 [設定] 之下的 [組態]，然後選取 [應用程式設定]。 如果您看到名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定，表示 Azure 中執行的函式應用程式已啟用 Application Insights 整合。

[Azure 入口網站]: https://portal.azure.com
