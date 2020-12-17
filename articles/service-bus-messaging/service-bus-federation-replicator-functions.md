---
title: 訊息複寫工作和應用程式-Azure 服務匯流排 |Microsoft Docs
description: 本文概述如何使用 Azure Functions 建立訊息複寫工作和應用程式
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657364"
---
# <a name="message-replication-tasks-and-applications"></a>訊息複寫工作和應用程式

如「訊息複寫」 [和「跨區域同盟」一](service-bus-federation-overview.md) 文所述，在「服務匯流排」實體和「服務匯流排」之間，以及在「服務匯流排」和「其他」訊息來源與目標之間的訊息序列複寫通常會仰賴 Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) 是可調整且可靠的執行環境，可用於設定和執行無伺服器應用程式，包括 [訊息複寫和同盟](service-bus-federation-overview.md) 工作。

在此總覽中，您將瞭解 Azure Functions 這類應用程式的內建功能、有關您可以針對轉換工作進行調整和修改的程式碼區塊，以及如何設定 Azure Functions 應用程式，使其能與服務匯流排和其他 Azure 訊息服務整合在最理想的情況下。 針對許多詳細資料，本文將指向 Azure Functions 檔。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
