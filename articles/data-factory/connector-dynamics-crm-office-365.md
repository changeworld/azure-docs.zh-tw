---
title: 複製 Dynamics (Common Data Service) 中的資料
description: 瞭解如何使用 data factory 管線中的複製活動，將資料從 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 (Common Data Service) 複製到支援的接收資料存放區，或從支援的來源資料存放區複製到 Dynamics CRM 或 Dynamics 365。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/23/2020
ms.openlocfilehash: 204399186ae229324f9dc478e0ef58a173060013
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638171"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Dynamics 365 (Common Data Service) 複製資料以及複製資料至 Dynamics 365
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，將資料從 Microsoft Dynamics 365 和 Microsoft Dynamics CRM 複製到其中，以及將資料複製到其中。 它是以「 [複製活動」總覽](copy-activity-overview.md) 文章為基礎，提供複製活動的一般總覽。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此連接器：

- 使用支援的[來源和接收矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Dynamics 365 (Common Data Service) 或 Dynamics CRM 複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Dynamics 365 (Common Data Service) 或 Dynamics CRM。 如需複製活動作為來源和接收所支援的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats) 資料表。

此 Dynamics 連接器支援線上和內部部署的 Dynamics 7 到9版。 具體而言：

- 第7版對應至 Dynamics CRM 2015。
- 第8版對應至 Dynamics CRM 2016 和早期版本的 Dynamics 365。
- 第9版對應到較新版本的 Dynamics 365。

請參閱下表，以瞭解 Dynamics 版本和產品支援的驗證類型和設定。

| Dynamics 版本 | 驗證類型 | 已連結的服務範例 |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 線上版 <br/><br/> Dynamics CRM Online | Azure Active Directory (Azure AD) 服務主體 <br/><br/> Office 365 | [Dynamics online 和 Azure AD 服務-主體或 Office 365 驗證](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 內部部署與網際網路對向部署 (IFD)  <br/><br/> 搭配 IFD 的 Dynamics CRM 2016 內部部署版 <br/><br/> 搭配 IFD 的 Dynamics CRM 2015 內部部署版 | IFD | [使用 IFD 和 IFD 驗證的 Dynamics 內部部署](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

特別針對 Dynamics 365 而言，支援下列應用程式類型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

此連接器不支援其他應用程式類型，例如財務、營運和人才。

此 Dynamics 連接器是以 [DYNAMICS XRM 工具](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)為基礎。

>[!TIP]
>若要從 Dynamics 365 財務和營運複製資料，您可以使用 [DYNAMICS AX 連接器](connector-dynamics-ax.md)。

## <a name="prerequisites"></a>必要條件

若要搭配 Azure AD 服務主體驗證使用此連接器，您必須在 Common Data Service 或 Dynamics 中設定伺服器對伺服器 (S2S) 驗證。 如需詳細步驟，請參閱 [這篇文章](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) 。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Dynamics 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Dynamics 連結服務所支援的屬性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 與 Dynamics CRM online

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是 |
| deploymentType | Dynamics 執行個體的部署類型。 Dynamics Online 的值必須為 "Online"。 | 是 |
| serviceUri | 您的 Dynamics 實例的服務 URL，也就是您從瀏覽器存取的相同 URL。 例如 "HTTPs:// \<organization-name> . crm [x] .com"。 | 是 |
| authenticationType | 連線到 Dynamics 伺服器時所使用的驗證類型。 有效的值為 ">aadserviceprincipal" 和 "Office365"。 | 是 |
| servicePrincipalId | Azure AD 應用程式的用戶端識別碼。 | 當驗證為 ">aadserviceprincipal" 時為 [是] |
| servicePrincipalCredentialType | 要用於服務主體驗證的認證類型。 有效的值為 "ServicePrincipalKey" 和 "ServicePrincipalCert"。 | 當驗證為 ">aadserviceprincipal" 時為 [是] |
| servicePrincipalCredential | 服務主體認證。 <br/><br/>當您使用 "ServicePrincipalKey" 做為認證類型時， `servicePrincipalCredential` 可以是 Azure Data Factory 在連結的服務部署時加密的字串。 或者，它可以是 Azure Key Vault 中的秘密參考。 <br/><br/>當您使用 "ServicePrincipalCert" 做為認證時， `servicePrincipalCredential` 必須是 Azure Key Vault 中的憑證參考。 | 當驗證為 ">aadserviceprincipal" 時為 [是] |
| username | 要連接到 Dynamics 的使用者名稱。 | 當驗證為 "Office365" 時為 [是] |
| 密碼 | 您指定做為使用者名稱的使用者帳戶密碼。 使用 "SecureString" 將此欄位標示為安全地儲存在 Data Factory 中，或 [參考儲存在 Azure Key Vault 中的密碼](store-credentials-in-key-vault.md)。 | 當驗證為 "Office365" 時為 [是] |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果未指定任何值，則屬性會使用預設的 Azure integration runtime。 | 如果來源連結服務沒有整合執行時間，則為來源的 [否] 和 [是] |

>[!NOTE]
>Dynamics connector 先前使用選擇性的 **organizationName** 屬性來識別您的 Dynamics CRM 或 dynamics 365 online 實例。 雖然該屬性仍可運作，但建議您改為指定新的 **serviceUri** 屬性，以取得更佳的實例探索效能。

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>範例：使用 Azure AD 服務-主體和金鑰驗證的 Dynamics online

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>範例：使用 Azure AD 服務的 Dynamics online-主體和憑證驗證

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>範例：使用 Office 365 驗證的 Dynamics online

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>搭配 IFD 的 Dynamics 365 和 Dynamics CRM 內部部署版

與 Dynamics online 比較的其他屬性是 **主機名稱** 和 **埠** 。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是。 |
| deploymentType | Dynamics 執行個體的部署類型。 在具有 IFD 的 Dynamics 內部部署中，此值必須是 "OnPremisesWithIfd"。| 是。 |
| hostName | 內部部署 Dynamics 伺服器的主機名稱。 | 是。 |
| 連接埠 | 內部部署 Dynamics 伺服器的連接埠。 | 否。 預設值為443。 |
| organizationName | Dynamics 執行個體的組織名稱。 | 是。 |
| authenticationType | 連線到 Dynamics 伺服器時所使用的驗證類型。 如果是搭配 IFD 的 Dynamics 內部部署版，請指定 "Ifd"。 | 是。 |
| username | 要連接到 Dynamics 的使用者名稱。 | 是。 |
| 密碼 | 您為使用者名稱指定之使用者帳戶的密碼。 您可以使用 "SecureString" 將此欄位標示為安全地儲存在 Data Factory 中。 或者，您可以將密碼儲存在 Key Vault 中，並讓複製活動在資料複製時從該處提取。 請參閱[在金鑰保存庫中儲存認證](store-credentials-in-key-vault.md)深入了解。 | 是。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果未指定任何值，則屬性會使用預設的 Azure integration runtime。 | 來源為否，接收則為 yes。 |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>範例：使用 IFD 驗證之搭配 IFD 的 Dynamics 內部部署版

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Dynamics 資料集所支援的屬性清單。

若要從 Dynamics 複製資料，以及將資料複製到 Dynamics，支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 ">dynamicsentity"、"DynamicsCrmEntity" 或 "CommonDataServiceForAppsEntity"。 |是 |
| entityName | 要擷取之實體的邏輯名稱。 | 如果活動來源指定為 "query"，則為 No 來源，如果是接收，則為 yes |

#### <a name="example"></a>範例

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Dynamics 來源和接收類型所支援的屬性清單。

### <a name="dynamics-as-a-source-type"></a>Dynamics 作為來源類型

若要從 Dynamics 複製資料，複製活動 **來源** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 ">dynamicssource"、"DynamicsCrmSource" 或 "CommonDataServiceForAppsSource"。 | 是 |
| 查詢 | FetchXML 是在 Dynamics online 和內部部署中使用的專屬查詢語言。 請參閱下列範例。 若要深入瞭解，請參閱 [使用 FetchXML 建立查詢](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8))。 | 如果 `entityName` 在資料集中指定，則為 No |

>[!NOTE]
>即使您在 FetchXML 查詢中設定的資料行投影未包含 PK 資料行，還是一律會複製 PK 資料行。

> [!IMPORTANT]
>- 從 Dynamics 複製資料時，從 Dynamics 到 sink 的明確資料行對應是選擇性的。 但我們強烈建議您進行對應，以確保有決定性的複製結果。
>- 當 Data Factory 在撰寫 UI 中匯入架構時，它會推斷架構。 其運作方式是從 Dynamics 查詢結果取樣前幾個資料列，以初始化來源資料行清單。 在這種情況下，會省略頂端資料列中沒有值的資料行。 如果沒有明確的對應，則相同的行為也適用于複製執行。 您可以在對應中檢查並新增更多資料行，這些資料行會在複製執行時間中接受。

#### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>範例 FetchXML 查詢

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics 作為接收類型

若要將資料複製到 Dynamics，複製活動 **接收** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 ">dynamicssink"、"DynamicsCrmSink" 或 "CommonDataServiceForAppsSink"。 | 是。 |
| writeBehavior | 作業的寫入行為。 值必須是 "Upsert"。 | 是 |
| alternateKeyName | 在您的實體上定義的替代索引鍵名稱，可進行 upsert。 | 否。 |
| writeBatchSize | 每個批次中寫入 Dynamics 的資料列計數。 | 否。 預設值是 10。 |
| ignoreNullValues | 在寫入作業期間，是否忽略索引鍵欄位以外的輸入資料中的 null 值。<br/><br/>有效的值為 **TRUE** 和 **FALSE** ：<ul><li>**TRUE** ：當您執行 upsert 或更新作業時，將目的地物件中的資料保留不變。 執行插入作業時，插入已定義的預設值。</li><li>**FALSE** ：當您執行 upsert 或更新作業時，將目的地物件中的資料更新為 null 值。 當您進行插入作業時，插入 null 值。</li></ul> | 否。 預設值為 **FALSE** 。 |

>[!NOTE]
>接收 **writeBatchSize** 和 Dynamics 接收器的複製活動 **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** 預設值為10。 因此，預設會將100記錄以並行方式提交至 Dynamics。

針對 Dynamics 365 online， [每個組織都有兩個並行批次呼叫](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations)的限制。 如果超出該限制，則在執行第一個要求之前，會擲回「伺服器忙碌」例外狀況。 將 **writeBatchSize** 保持在10以下，以避免這類並行呼叫的節流。

**WriteBatchSize** 和 **parallelCopies** 的最佳組合取決於您實體的架構。 架構元素包括連接到這些呼叫的資料行數目、資料列大小，以及外掛程式、工作流程或工作流程活動的數目。 **WriteBatchSize** (10) &times; **parallelCopies** (10) 預設設定為根據 Dynamics 服務的建議。 此值適用于大部分的 Dynamics 實體，但它可能無法提供最佳效能。 您可以藉由調整複製活動設定中的組合來微調效能。

#### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics 的資料類型對應

當您從 Dynamics 複製資料時，下表會顯示從 Dynamics 資料類型到 Data Factory 過渡期資料類型的對應。 若要瞭解複製活動如何對應至來源架構，以及資料類型如何對應至接收，請參閱 [架構和資料類型](copy-activity-schema-and-type-mapping.md)對應。

使用下列對應表，根據您的來源 Dynamics 資料類型，在資料集結構中設定對應的 Data Factory 資料類型：

| Dynamics 資料類型 | Data Factory 過渡期資料類型 | 支援作為來源 | 支援作為接收 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ (參閱 [指引](#writing-data-to-a-lookup-field))  |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | 小數位數 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ (參閱 [指引](#writing-data-to-a-lookup-field))  |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | 小數位數 | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ (參閱 [指引](#writing-data-to-a-lookup-field))  |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> 不支援 Dynamics 資料類型 **AttributeType. attributetype.calendarrules attributetype.partylist** 、 **AttributeType. MultiSelectPicklist** 和 **AttributeType. PartyList** 。

## <a name="writing-data-to-a-lookup-field"></a>將資料寫入查閱欄位

若要將資料寫入具有多個目標（例如客戶和擁有者）的查閱欄位，請遵循下列指導方針和範例：

1. 讓您的來源同時包含域值和對應的目標機構名稱。
   - 如果所有記錄都對應到相同的目標實體，請確定下列其中一個條件：
      - 您的來源資料具有儲存目標機構名稱的資料行。
      - 您已在複製活動來源中新增額外的資料行，以定義目標實體。
   - 如果不同的記錄對應至不同的目標實體，請確定您的來源資料具有儲存對應目標機構名稱的資料行。

1. 將值和實體參考資料行從來源對應到接收。 實體參考資料行必須對應至具有特殊命名模式的虛擬資料行 `{lookup_field_name}@EntityReference` 。 資料行實際上不存在 Dynamics 中。 它是用來指出此資料行是指定之使用多目標查閱欄位的中繼資料資料行。

例如，假設來源有下列兩個數據行：

- **GUID** 類型的 **CustomerField** 資料行，這是 Dynamics 中目標實體的主鍵值。
- **字串** 類型的 **目標** 資料行，這是目標實體的邏輯名稱。

此外，假設您想要將此類資料複製到 type **Customer** 的 sink Dynamics Entity field **CustomerField** 。

在 [複製活動資料行對應] 中，對應兩個數據行，如下所示：

- **CustomerField** 至 **CustomerField** 。 此對應是一般欄位對應。
- **目標設定** 為 **CustomerField \@ EntityReference** 。 接收資料行是代表實體參考的虛擬資料行。 在對應中輸入這類功能變數名稱，因為它們不會藉由匯入架構來顯示。

![Dynamics 查閱-欄位資料行對應](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

如果您的所有來源記錄都對應到相同的目標實體，而且您的來源資料未包含目標機構名稱，以下是快捷方式：在複製活動來源中，加入額外的資料行。 使用模式來命名新的資料行 `{lookup_field_name}@EntityReference` ，將值設定為目標機構名稱，然後照常進行資料行對應。 如果您的來源和接收資料行名稱相同，您也可以略過明確的資料行對應，因為依預設，複製活動會依名稱對應資料行。

![Dynamics 查閱欄位加入實體參考資料行](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解有關屬性的詳細資料，請參閱 [查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需資料存放區的清單，Data Factory 中的複製活動支援作為來源和接收，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。