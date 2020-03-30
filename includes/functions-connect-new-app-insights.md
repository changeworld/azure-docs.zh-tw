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
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132602"
---
函式可讓您輕鬆地從 [Azure 入口網站]中，將 Application Insights 整合新增至函式應用程式。

1. 在[門戶][Azure 門戶]中`Function Apps`，在頁面頂部的搜索欄中鍵入，選擇函數應用，然後在視窗頂部選擇**未配置應用程式見解**的橫幅。 如果您沒有看到此橫幅，則你的應用已經啟用了應用程式見解。

    ![從入口網站啟用 Application Insights](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 使用資料表 (圖片下方) 中指定的設定，建立 Application Insights 資源。

   ![建立 Application Insights 資源](media/functions-connect-new-app-insights/ai-general.png)

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 唯一的應用程式名稱 | 最簡單的方式是使用與您函式應用程式一樣的名稱，而這必須是您訂用帳戶中唯一的名稱。 | 
    | **位置** | 西歐 | 可能的話，請使用與函式應用程式相同的[區域](https://azure.microsoft.com/regions/)，或該區域附近的區域。 |

1. 選取 [確定]****。 Application Insights 資源會建立在函式應用程式所在的資源群組和訂用帳戶。 建立資源之後，請關閉 Application Insights 視窗。

1. 回到您的函式應用程式中，選取 [應用程式設定]****，然後向下捲動至 [應用程式設定]****。 如果您看到名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定，表示 Azure 中執行的函式應用程式已啟用 Application Insights 整合。

[Azure 門戶]: https://portal.azure.com
