---
title: Azure 資料目錄開發人員範例
description: 本文提供資料目錄 REST API 可用之開發人員範例的概觀。
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7d0e27802745dda62f87e412053650907e9b812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950162"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure 資料目錄開發人員範例

開始使用資料目錄 REST API 開發 Azure 資料目錄應用。 資料目錄 REST API 是REST 架構 API，能夠以程式設計方式存取資料目錄資源，藉此註冊、加上註解，及以程式設計方式搜尋資料資產。

## <a name="samples-available-on-githubcom"></a>GitHub.com上的樣品

* [開始使用 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   入門示例演示如何使用 Azure AD 進行身份驗證以使用資料目錄 REST API 註冊、搜索和刪除資料資產。
   
* [使用服務主體開始使用 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   此示例演示如何使用資料目錄 REST API 註冊、搜索和刪除資料資產。 此示例使用服務主體身份驗證。

* [Azure 資料目錄的導入/匯出工具](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   此示例演示如何使用資料目錄 REST API 從 Azure 資料目錄提取資產並將其序列化到檔中。 它也會示範如何取用一組已序列化為 JSON 的資產，並將其推送至目錄。 它支援使用搜尋查詢匯出目錄的子集。

* [Azure 資料目錄中的批量註冊和批號](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   此示例演示如何使用資料目錄 REST API 和打開 XML 從 Excel 活頁簿批量註冊資料資產。
  
* [批量將術語表術語導入到 Azure 資料目錄中](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   這個範例會示範如何將 CSV 檔案中的詞彙匯入 ADC 字彙。

* [大量匯入到 Azure 資料目錄的關係](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   此示例演示如何以程式設計方式將關係資訊從 CSV 檔導入到資料目錄中。

* [將關係發佈到 Azure 資料目錄](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   此示例演示如何以程式設計方式將關係資訊發佈到資料目錄。
   
## <a name="next-steps"></a>後續步驟
[Azure 資料目錄 REST API 引用](/rest/api/datacatalog/)
