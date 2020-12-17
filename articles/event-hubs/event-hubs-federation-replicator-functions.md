---
title: 事件複寫工作和應用程式-Azure 事件中樞 |Microsoft Docs
description: 本文概述如何使用 Azure Functions 建立事件複寫工作和應用程式
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663600"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>使用 Azure Functions 的事件複寫工作和應用程式

> [!TIP]
> 對於您需要考慮事件的承載，並加以轉換、匯總、擴充或減少的所有具狀態複寫工作，請使用 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) ，而不是 Azure Functions。

如 [事件複寫和跨區域同盟](event-hubs-federation-overview.md) 一文所述，事件中樞與事件中樞之間，以及事件中樞與其他事件串流來源之間的事件資料流程，以及仰賴在 Azure Functions 上的事件串流之間的無狀態複寫。

[Azure Functions](../azure-functions/functions-overview.md) 是可調整且可靠的執行環境，可用於設定和執行無伺服器應用程式，包括事件複寫和同盟工作。

在此總覽中，您將瞭解 Azure Functions 這類應用程式的內建功能、您可以針對轉換工作進行調整和修改的程式碼區塊，以及如何設定 Azure Functions 應用程式，讓它在理想情況下與事件中樞和其他 Azure 訊息服務整合。 針對許多詳細資料，本文將指向 Azure Functions 檔。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









