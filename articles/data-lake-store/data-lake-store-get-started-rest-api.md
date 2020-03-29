---
title: REST API：Azure Data Lake Storage Gen1 上的帳戶管理作業 | Microsoft Docs
description: 使用 Azure Data Lake Storage Gen1 與 WebHDFS REST API 在 Data Lake Storage Gen1 中執行帳戶管理作業
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877099"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>使用 REST API 對 Azure Data Lake Storage Gen1 進行帳戶管理作業
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

在此文章中，您會了解如何使用 REST API 在 Azure Data Lake Storage Gen1 上執行帳戶管理作業。 帳戶管理操作包括創建資料存儲第 1 代帳戶、刪除資料存儲庫第 1 代帳戶等。有關如何使用 REST API 對資料存儲庫 1 執行檔案系統操作的說明，請參閱使用[REST API 對資料存儲庫第 1 代的檔案系統操作](data-lake-store-data-operations-rest-api.md)。

## <a name="prerequisites"></a>Prerequisites
* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **[cURL](https://curl.haxx.se/)**. 此文章使用 cURL 示範如何對 Data Lake Storage Gen1 進行 REST API 呼叫。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 驗證？
您可以使用兩種方法來使用 Azure Active Directory 進行驗證。

* 如需有關讓應用程式進行使用者驗證 (互動式) 的詳細資訊，請參閱[使用 .NET SDK 向 Data Lake Storage Gen1 進行使用者驗證](data-lake-store-end-user-authenticate-rest-api.md)。
* 如需有關讓應用程式進行服務對服務驗證 (非互動式) 的詳細資訊，請參閱[使用 .NET SDK 向 Data Lake Storage Gen1 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-rest-api.md)。


## <a name="create-a-data-lake-storage-gen1-account"></a>建立 Data Lake Storage Gen1 帳戶
這項作業以在 [這裡](https://docs.microsoft.com/rest/api/datalakestore/accounts/create)定義的 REST API 呼叫為基礎。

使用下列 cURL 命令。 ** \<將存儲根1 名稱>** 替換為數據湖存儲第 1 代名稱。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上述命令中，以您稍早擷取的授權權杖取代 \<`REDACTED`\>。 此命令的要求承載包含在提供給上方 `-d` 參數的 **input.json** 檔案中。 input.json 檔案的內容如下列程式碼片段所示︰

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>刪除 Data Lake Storage Gen1 帳戶
這項作業以在 [這裡](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete)定義的 REST API 呼叫為基礎。

使用下列 cURL 命令刪除 Data Lake Storage Gen1 帳戶。 ** \<將存儲基因 1 名稱>** 替換為數據湖存儲第 1 代帳戶名稱。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

您應該會看到如下列程式碼片段的輸出：

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>後續步驟
* [使用 REST API 在 Data Lake Storage Gen1 上進行檔案系統作業](data-lake-store-data-operations-rest-api.md)。

## <a name="see-also"></a>另請參閱
* [Azure Data Lake Storage Gen1 REST API 參考](https://docs.microsoft.com/rest/api/datalakestore/)
* [與 Azure Data Lake Storage Gen1 相容的開放原始碼巨量資料應用程式](data-lake-store-compatible-oss-other-applications.md)

