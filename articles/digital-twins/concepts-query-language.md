---
title: 查詢語言
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 查詢語言的基本概念。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562461"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>關於 Azure 數位 Twins 的查詢語言

回想一下，Azure 數位 Twins 的中心是對應項[**圖形**](concepts-twins-graph.md)，由**數位 Twins**和**關聯**性所構成。 您可以查詢此圖形，以取得其所包含之數位 twins 和關聯性的相關資訊。 這些查詢是以類似 SQL 的自訂查詢語言撰寫，稱為**Azure 數位 Twins 查詢語言**。

若要從用戶端應用程式將查詢提交至服務，您將使用 Azure 數位 Twins[**查詢 API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)。 這可讓開發人員撰寫查詢，並套用篩選來尋找對應項圖形中的數位 twins 集合，以及其他有關 Azure 數位 Twins 案例的資訊。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>後續步驟

瞭解如何撰寫查詢，並參閱 how [*to：查詢*](how-to-query-graph.md)對應項圖形中的用戶端程式代碼範例。
