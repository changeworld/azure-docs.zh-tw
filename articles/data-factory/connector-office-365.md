---
title: 使用 Azure Data Factory 從 Office 365 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Office 365 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: dda761e12abe7ec866ad9426982563b6f629f6b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513304"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Office 365 複製到 Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 與[Microsoft Graph 資料連線](https://docs.microsoft.com/graph/data-connect-concept-overview)整合，可讓您以可擴充的方式將 Office 365 租使用者中豐富的組織資料帶入 Azure，並建立分析應用程式，並根據這些重要的資料資產來提取深入解析。 與 Privileged Access Management 整合可針對 Office 365 中的重要策劃資料提供安全的存取控制。  如需有關 Microsoft Graph 資料連線的總覽，請參閱[此連結](https://docs.microsoft.com/graph/data-connect-concept-overview)，如需授權資訊，請參閱[此連結](https://docs.microsoft.com/graph/data-connect-policies#licensing)。

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Office 365 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能
ADF Office 365 連接器和 Microsoft Graph 資料連線可從 Exchange 電子郵件啟用的信箱，大規模內嵌不同類型的資料集，包括通訊錄連絡人、行事曆事件、電子郵件訊息、使用者資訊、信箱設定等等。  請參閱[這裡](https://docs.microsoft.com/graph/data-connect-datasets)，以查看可用資料集的完整清單。

目前，您只能在單一複製活動中，**將資料從 Office 365 複製到 JSON 格式的[Azure Blob 儲存體](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) ** （輸入 setOfObjects）。 如果您想要將 Office 365 載入其他類型的資料存放區，或以其他格式載入，可以將第一個複製活動與後續的複製活動鏈結，進一步將資料載入任何[支援的 ADF 目的地存放區](copy-activity-overview.md#supported-data-stores-and-formats) (請參閱「支援的資料存放區和格式」資料表中的「支援作為接收器」)。

>[!IMPORTANT]
>- 包含資料處理站和接收資料存放區的 Azure 訂用帳戶必須與 Office 365 租用戶位於相同的 Azure Active Directory (Azure AD) 租用戶下。
>- 請確定用於複製活動的 Azure Integration Runtime 地區以及目的地與 Office 365 租用戶使用者的信箱所在區域相同。 若要了解如何判斷 Azure IR 位置，請參閱[這裡](concepts-integration-runtime.md#integration-runtime-location)。 如需支援的 Office 區域和對應的 Azure 區域清單，請參閱[以下資料表](https://docs.microsoft.com/graph/data-connect-datasets#regions)。
>- 服務主體驗證是唯一支援 Azure Blob 儲存體、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 作為目的地存放區的驗證機制。

## <a name="prerequisites"></a>必要條件

若要將資料從 Office 365 複製到 Azure，您必須完成下列必要步驟：

- 您的 Office 365 租用戶系統管理員必須完成上架動作，如[此處](https://docs.microsoft.com/graph/data-connect-get-started)所述。
- 在 Azure Active Directory 中建立和設定 Azure AD Web 應用程式。  如需指示，請參閱[建立 Azure AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。
- 請記下以下的值，您可以使用這些值來定義 Office 365 的連結服務：
    - 租用戶識別碼。 如需相關指示，請參閱[取得租用戶識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。
    - 應用程式識別碼和驗證金鑰。  如需相關指示，請參閱[取得應用程式識別碼和驗證金鑰](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。
- 新增使用者身分識別，該使用者會以 Azure AD Web 應用程式擁有者身分進行資料存取要求 (從 [Azure AD Web 應用程式] > [設定] > [擁有者] > [新增擁有者])。 
    - 使用者身分識別必須位於要從中取得資料的 Office 365 組織中，而且不可以是來賓使用者。

## <a name="approving-new-data-access-requests"></a>核准新的資料存取要求

如果這是您第一次要求此內容 (結合正在存取的資料資料表、正在載入資料的目的地帳戶，以及進行資料存取要求的使用者身分識別) 的資料，您會看到複製活動狀態為「進行中」，且僅在您按一下 [[動作] 底下的 [詳細資料] 連結](copy-activity-overview.md#monitoring)時，會看到狀態為 “RequestingConsent”。  資料存取核准者群組的成員必須先在 Privileged Access Management 中核准該要求，資料擷取才能繼續。

請參閱[這裡](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal)了解核准者如何核准資料存取要求，然後參考[這裡](https://docs.microsoft.com/graph/data-connect-pam)，了解 Privileged Access Management 整體整合的相關說明，包括如何設定資料存取核准者群組。

## <a name="policy-validation"></a>原則驗證

如果建立 ADF 作為受控應用程式的一部分，並且對管理資源群組內的資源進行 Azure 原則指派，則 ADF 會檢查每個複製活動執行，以確定原則指派已強制執行。 請參閱[這裡](https://docs.microsoft.com/graph/data-connect-policies#policies)取得支援的原則清單。

## <a name="getting-started"></a>開始使用

>[!TIP]
>如需使用 Office 365 連接器的逐步解說，請參閱[從 Office 365 載入資料](load-office-365-data.md)文章。

您可以使用下列其中一個工具或 SDK，以建立內含複製活動的管線。 選取連結以移至逐步指示教學課程，以建立內含複製活動的管線。 

- [Azure 入口網站](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager 範本](quickstart-create-data-factory-resource-manager-template.md)。 

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Office 365 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 Office 365 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為：**Office365** | Yes |
| office365TenantId | Office 365 帳戶所屬的 Azure 租用戶識別碼。 | Yes |
| servicePrincipalTenantId | 指定您 Azure AD Web 應用程式所在的租用戶資訊。 | Yes |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 | Yes |
| connectVia | 用來連線到資料存放區的整合執行階段。  如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 |

>[!NOTE]
> **office365TenantId** 與 **servicePrincipalTenantId** 之間的差異以及要提供的對應值：
>- 如果您是企業開發人員，負責針對自己組織使用的 Office 365 資料開發應用程式，則您應該對這兩個屬性提供相同的租用戶識別碼，也就是貴組織的 AAD 租用戶識別碼。
>- 如果您是為客戶開發應用程式的 ISV 開發人員，則 office365TenantId 將會是您客戶的 (應用程式安裝程式) AAD 租用戶識別碼，而 servicePrincipalTenantId 將為貴公司的 AAD 租用戶識別碼。

**範例︰**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Office 365 資料集所支援的屬性清單。

若要從 Office 365 複製資料，以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為：**Office365Table** | 是 |
| tableName | 擷取自 Office 365 的資料集名稱。 如需可供擷取的 Office 365 資料集清單，請參閱[這裡](https://docs.microsoft.com/graph/data-connect-datasets#datasets)。 | Yes |

如果您 `dateFilterColumn` 在資料集中設定了、、和，則仍會依 `startTime` `endTime` `userScopeFilterUri` 其支援，但建議您在 [活動來源] 中使用新模型。

**範例**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Office 365 來源所支援的屬性清單。

### <a name="office-365-as-source"></a>Office 365 作為來源

若要從 Office 365 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為： **Office365Source** | Yes |
| allowedGroups | 群組選取述詞。  使用此屬性可選取最多10個使用者群組，資料將會抓取到其中。  如果未指定任何群組，則會傳回整個組織的資料。 | No |
| userScopeFilterUri | `allowedGroups`未指定屬性時，您可以使用套用在整個租使用者上的述詞運算式，篩選要從 Office 365 解壓縮的特定資料列。 述詞格式應符合 Microsoft Graph Api 的查詢格式，例如 `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` 。 | No |
| dateFilterColumn | 日期時間篩選資料行的名稱。 使用此屬性來限制用來解壓縮 Office 365 資料的時間範圍。 | 如果資料集有一或多個日期時間資料行，則為 Yes。 如需需要此日期時間篩選的資料集清單，請參閱[這裡](https://docs.microsoft.com/graph/data-connect-filtering#filtering)。 |
| startTime | 要做為篩選依據的開始日期時間值。 | 如果 `dateFilterColumn` 已指定，則為是 |
| EndTime | 要做為篩選依據的結束日期時間值。 | 如果 `dateFilterColumn` 已指定，則為是 |
| outputColumns | 要複製到接收的資料行陣列。 | 否 |

**範例︰**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
            "source": {
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
