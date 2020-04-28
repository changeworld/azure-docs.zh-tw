---
title: Azure 資料目錄開發人員範例
description: 本文提供資料目錄 REST API 可用之開發人員範例的概觀。
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7d0e27802745dda62f87e412053650907e9b812c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68950162"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure 資料目錄開發人員範例

使用資料目錄 REST API 開始開發 Azure 資料目錄應用程式。 資料目錄 REST API 是REST 架構 API，能夠以程式設計方式存取資料目錄資源，藉此註冊、加上註解，及以程式設計方式搜尋資料資產。

## <a name="samples-available-on-githubcom"></a>GitHub.com 上可用的範例

* [開始使用 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   「開始使用」範例會示範如何向 Azure AD 進行驗證，以透過資料目錄 REST API 來註冊、搜尋及刪除資料資產。
   
* [使用服務主體開始使用 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   這個範例會示範如何使用資料目錄 REST API 來註冊、搜尋及刪除資料資產。 這個範例會使用服務主體驗證。

* [Azure 資料目錄的匯入/匯出工具](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   此範例示範如何使用資料目錄 REST API 從 Azure 資料目錄提取資產，並將其序列化為檔案。 它也會示範如何取用一組已序列化為 JSON 的資產，並將其推送至目錄。 它支援使用搜尋查詢匯出目錄的子集。

* [大量註冊並在 Azure 資料目錄中標注](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   這個範例會示範如何使用資料目錄 REST API 和 Open XML，從 Excel 活頁簿大量註冊資料資產。
  
* [將詞彙大量匯入 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   這個範例會示範如何將 CSV 檔案中的詞彙匯入 ADC 字彙。

* [將關聯性大量匯入 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   這個範例會示範如何以程式設計方式，將 CSV 檔案中的關聯性資訊匯入至資料目錄。

* [將關聯性發佈到 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   這個範例會示範如何以程式設計方式將關聯性資訊發行至資料目錄。
   
## <a name="next-steps"></a>後續步驟
[Azure 資料目錄 REST API 參考](/rest/api/datacatalog/)
