---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169898"
---
| | |
|--|--|
|**`<DESTINATION>`**| 傳送記錄的目的地。 有效值為 `AppInsights` 和 `Blob`。<br/>當您使用時 `AppInsights` ，請確定 [您的函數應用程式中已啟用 Application Insights](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration)。<br/>當您將目的地設定為時 `Blob` ，會在 `azure-functions-scale-controller` `AzureWebJobsStorage` 應用程式設定中設定的預設儲存體帳戶中，建立名為的 blob 容器中的記錄。 |
|**`<VERBOSITY>`** | 指定記錄層級。 支援的值為 `None`、`Warning` 和 `Verbose`。<br/>當設定為時 `Verbose` ，縮放控制器會記錄背景工作計數變更的原因，以及參與這些決策之觸發程式的相關資訊。 詳細資訊記錄包含觸發程式警告，以及執行調整控制器之前和之後的觸發程式所使用的雜湊。 |

> [!CAUTION]
> 請勿讓調整控制器記錄保持啟用狀態。 啟用記錄，直到您收集到足夠的資料，以瞭解調整控制器的運作方式，然後將它停用。