---
title: 在 Azure API for FHIR 中設定資料庫設定
description: 本文說明如何在中設定資料庫設定 Azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870958"
---
# <a name="configure-database-settings"></a>設定資料庫設定 

Azure API for FHIR 使用資料庫來儲存其資料。 基礎資料庫的效能取決於服務布建期間選取的要求單位（RU）數目，或服務布建之後，在資料庫設定中所選的數量。

Azure API for FHIR 在設定基礎資料庫的效能時，會從 Cosmos DB 中借用 ru 的概念（請參閱[Azure Cosmos DB 中的要求單位](https://docs.microsoft.com/azure/cosmos-db/request-units)）。 

您必須布建輸送量，以確保您的資料庫隨時都能使用足夠的系統資源。 您的應用程式所需的 ru 數目取決於您所執行的作業。 作業的範圍可以從簡單的讀取和寫入到更複雜的查詢。 

> [!NOTE]
> 當不同的作業使用不同的 RU 數目時，我們會傳迴響應標頭中每個 API 呼叫所耗用的實際 RU 數目。 如此一來，您就可以分析應用程式所使用的 ru 數目。

## <a name="update-throughput"></a>更新輸送量
若要在 Azure 入口網站中變更此設定，請流覽至您的 Azure API for FHIR，然後開啟 [資料庫] 分頁。 接下來，根據您的效能需求，將布建的輸送量變更為所需的值。 您最多可以將值變更為 10000 RU/秒。 如果您需要較高的值，請聯絡 Azure 支援。

> [!NOTE] 
> 較高的值表示較高的 Azure API for FHIR 輸送量和較高的服務成本。

![Config Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何更新 Azure API for FHIR 的 ru。 接下來，部署完全受控的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)