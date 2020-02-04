---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669559"
---
函式可讓您輕鬆地從 [Azure 入口網站]中，將 Application Insights 整合新增至函式應用程式。

1. 在[入口網站][Azure 入口網站]中，選取 [所有服務] > [函式應用程式]  ，然後選取函式應用程式，接著在視窗頂端選取 [Application Insights]  橫幅

    ![從入口網站啟用 Application Insights](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 使用資料表 (圖片下方) 中指定的設定，建立 Application Insights 資源。

   ![建立 Application Insights 資源](media/functions-connect-new-app-insights/ai-general.png)

    | 設定      | 建議的值  | 說明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 唯一的應用程式名稱 | 最簡單的方式是使用與您函式應用程式一樣的名稱，而這必須是您訂用帳戶中唯一的名稱。 | 
    | **位置** | 西歐 | 可能的話，請使用與函式應用程式相同的[區域](https://azure.microsoft.com/regions/)，或該區域附近的區域。 |

1. 選取 [確定]  。 Application Insights 資源會建立在函式應用程式所在的資源群組和訂用帳戶。 建立資源之後，請關閉 Application Insights 視窗。

1. 回到您的函式應用程式中，選取 [應用程式設定]  ，然後向下捲動至 [應用程式設定]  。 如果您看到名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定，表示 Azure 中執行的函式應用程式已啟用 Application Insights 整合。

[Azure 入口網站]: https://portal.azure.com
