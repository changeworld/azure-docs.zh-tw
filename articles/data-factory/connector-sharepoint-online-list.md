---
title: 使用 Azure Data Factory 從 SharePoint Online 清單複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SharePoint Online 清單複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f560a01c4ec00649157a9c43aedf0ed6cfc2e050
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871923"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SharePoint Online 清單複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

此文章概述如何使用 Azure Data Factory 中的「複製活動」，從 SharePoint Online 清單複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SharePoint Online 清單連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SharePoint Online 清單複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，此 SharePoint 清單線上連接器會使用服務主體驗證，並透過 OData 通訊協定來擷取資料。

> [!TIP]
> 此連接器支援從 SharePoint Online **清單**複製資料，而不是從檔案。 在[從 SharePoint Online 複製檔案](#copy-file-from-sharepoint-online)一節，了解如何複製檔案。

## <a name="prerequisites"></a>Prerequisites

SharePoint 清單線上連接器會使用服務主體驗證來連線到 SharePoint。 請遵循這些步驟進行設定：

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，以在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 將 SharePoint Online 網站權限授與已註冊應用程式： 

    > [!NOTE]
    > 此作業需要 SharePoint Online 網站擁有者權限。 您可以前往網站首頁來尋找擁有者 -> 按一下右上角的 [X 成員]-> 檢查擁有「擁有者」角色的成員。

    1. 開啟 SharePoint Online 網站連結，例如 `https://[your_site_url]/_layouts/15/appinv.aspx` (取代網站 URL)。
    2. 搜尋您註冊的應用程式識別碼、填入空白欄位，然後按一下 [建立]。

        - 應用程式網域：`localhost.com`
        - 重新導向 URL：`https://www.localhost.com`
        - 權限要求 XML：

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![SharePoint 授與權限](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. 針對此應用程式按一下 [允許存取]。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可以使用這些屬性來定義 SharePoint Online 清單連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SharePoint Online 清單連結服務支援的屬性：

| **屬性**        | **說明**                                              | **必要** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | 類型屬性必須設定為： **SharePointOnlineList**。  | 是          |
| siteUrl             | SharePoint Online 網站 URL，例如 `https://contoso.sharepoint.com/sites/siteName`。 | 是          |
| servicePrincipalId  | 應用程式 (用戶端) 識別碼 (已在 Azure Active Directory 中註冊的應用程式)。 | 是          |
| servicePrincipalKey | 應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是          |
| tenantId            | 您的應用程式所在的租用戶識別碼。          | 是          |
| connectVia          | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 在此文章中稍早之前的[先決條件](#prerequisites)深入了解。 如果未指定，則會使用預設的 Azure Integration Runtime。 | 否           |

**範例︰**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 下列小節提供 SAP 資料表資料集所支援的屬性清單。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **SharePointOnlineLResource**。 | 是 |
| listName | SharePoint Online 清單的名稱。 | 是 |

**範例**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。  下列小節提供 SharePoint Online 清單來源所支援的屬性清單。

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online 清單作為來源

若要從 SharePoint Online 清單複製資料，複製活動 **source** 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **SharePointOnlineListSource**。 | 是 |
| 查詢 | 用來篩選資料的自訂 OData 查詢選項。 範例： `"$top=10&$select=Title,Number"`. | 否 |
| httpRequestTimeout | HTTP 要求取得回應的逾時 (以秒為單位)。 預設值為 300 (5 分鐘)。 | 否 |

**範例**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sharepoint-online-list"></a>SharePoint Online 清單的資料類型對應

當您從 SharePoint Online 清單複製資料時，在 SharePoint Online 清單資料類型與 Azure Data Factory 過渡期資料類型之間會使用下列對應。 

| **SharePoint Online 資料類型**                 | **OData 資料類型**                                  | **Azure Data Factory 過渡期資料類型** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| 單行文字                             | Edm.String                                           | String                                   |
| 多行文字                          | Edm.String                                           | String                                   |
| 選擇 (可供選擇的功能表)                    | Edm.String                                           | String                                   |
| 數字 (1、1.0、100)                            | Edm.Double                                           | Double                                   |
| 貨幣 ($、¥、€)                              | Edm.Double                                           | Double                                   |
| 日期及時間                                   | Edm.DateTime                                         | Datetime                                 |
| 查閱 (資訊已在此網站上)       | Edm.Int32                                            | Int32                                    |
| 是/否 (核取方塊)                              | Edm.Boolean                                          | Boolean                                  |
| 個人或群組                                 | Edm.Int32                                            | Int32                                    |
| 超連結或圖片                            | Edm.String                                           | String                                   |
| 計算 (以其他資料行為基礎的計算) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | String / Double / DateTime / Boolean     |
| 附件                                      | 不支援                                        |                                          |
| 工作結果                                    | 不支援                                        |                                          |
| 外部資料                                   | 不支援                                        |                                          |
| 受控中繼資料                                | 不支援                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>從 SharePoint Online 複製檔案

您可以使用 **Web 活動**從 SharePoint Online 複製檔案，以從 SPO 驗證和抓取存取權杖，然後傳遞至後續的**複製活動**，將 **HTTP 連接器作為來源**來複製資料。

![SharePoint 複製檔案流程](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. 請遵循[先決條件](#prerequisites)一節來建立 AAD 應用程式，並將權限授與 SharePoint Online。 

2. 建立 **Web 活動**，以從 SharePoint Online 取得存取權杖：

    - **URL**：`https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`。 取代租用戶識別碼。
    - **方法**：POST
    - **標頭**：
        - 內容類型：application/x-www-form-urlencoded
    - **本文**：`grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`。 取代用戶端識別碼、用戶端密碼、租用戶識別碼和租用戶名稱。

    > [!CAUTION]
    > 在 Web 活動中，將 [安全輸出] 選項設定為 [是]，以防止以純文字記錄權杖值。 任何進一步使用此值的活動都應該將其 [安全輸入] 選項設定為 [是]。

3. 搭配 HTTP 連接器作為來源鏈結**複製活動**，以複製 SharePoint Online 檔案內容：

    - HTTP 連結服務：
        - **基底 URL**：`https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`。 取代網站 URL 和檔案的相對路徑。 範例檔案相對路徑 `/sites/site2/Shared Documents/TestBook.xlsx`。
        - **驗證類型：** 匿名 (稍後使用複製活動來源中設定的持有人權杖)
    - 資料集：選擇您想要的格式。 若要依原樣複製檔案，請選取 [二進位] 類型。
    - 複製活動來源：
        - **要求方法**：GET
        - **其他標頭**：使用下列運算式`@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`，其使用上游 Web 活動所產生的持有人權杖作為授權標頭。 取代 Web 活動名稱。
    - 如往常般設定複製活動接收器。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
