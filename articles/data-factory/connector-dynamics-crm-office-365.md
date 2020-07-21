---
title: 複製 Dynamics 中的資料（Common Data Service）
description: 瞭解如何使用 data factory 管線中的複製活動，將資料從 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 （Common Data Service）複製到支援的接收資料存放區，或從支援的來源資料存放區複製到 Dynamics CRM 或 Dynamics 365。
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
ms.date: 06/10/2020
ms.openlocfilehash: 54aac9fda42a867ab66d631279efbca4f812b01a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497605"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Dynamics 365 (Common Data Service) 複製資料以及複製資料至 Dynamics 365
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，將資料從 Microsoft Dynamics 365 和 Microsoft Dynamics CRM 複製到其中。 它是以[複製活動總覽](copy-activity-overview.md)一文為基礎，提供複製活動的一般總覽。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源和接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Dynamics 365 (Common Data Service) 或 Dynamics CRM 複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Dynamics 365 (Common Data Service) 或 Dynamics CRM。 如需複製活動支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

這個 Dynamics 連接器支援線上和內部部署的 Dynamics 版本7到9。 具體而言：

- 第7版對應至 Dynamics CRM 2015。
- 第8版對應至 Dynamics CRM 2016 和早期版本的 Dynamics 365。
- 第9版會對應到較新版本的 Dynamics 365。

請參閱下表中的 Dynamics 版本和產品支援的驗證類型和設定。

| Dynamics 版本 | 驗證類型 | 已連結的服務範例 |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 線上版 <br/><br/> Dynamics CRM Online | Azure Active Directory （Azure AD）服務主體 <br/><br/> Office 365 | [Dynamics online 和 Azure AD 服務-主體或 Office 365 驗證](#dynamics-365-and-dynamics-crm-online) |
| 使用網際網路對向部署的 Dynamics 365 內部部署（IFD） <br/><br/> 搭配 IFD 的 Dynamics CRM 2016 內部部署版 <br/><br/> 搭配 IFD 的 Dynamics CRM 2015 內部部署版 | IFD | [具有 IFD 和 IFD 驗證的 Dynamics 內部部署](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

特別針對 Dynamics 365 而言，支援下列應用程式類型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

此連接器不支援其他應用程式類型，例如財務、營運和人才。

這個 Dynamics connector 建置於[DYNAMICS XRM 工具](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)之上。

>[!TIP]
>若要從 Dynamics 365 財務和營運複製資料，您可以使用[DYNAMICS AX 連接器](connector-dynamics-ax.md)。

## <a name="prerequisites"></a>先決條件

若要將此連接器與 Azure AD 服務主體驗證搭配使用，您必須在 Common Data Service 或 Dynamics 中設定伺服器對伺服器（S2S）驗證。 如需詳細步驟，請參閱[這篇文章](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Dynamics 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Dynamics 連結服務所支援的屬性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM online

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是 |
| deploymentType | Dynamics 執行個體的部署類型。 Dynamics Online 的值必須是 "Online"。 | 是 |
| serviceUri | Dynamics 實例的服務 URL。 例如 https://www.crmdynamics.com。 | 是 |
| authenticationType | 連線到 Dynamics 伺服器時所使用的驗證類型。 有效值為 "AADServicePrincipal" 和 "Office365"。 | 是 |
| servicePrincipalId | Azure AD 應用程式的用戶端識別碼。 | 當驗證為 "AADServicePrincipal" 時為 [是] |
| servicePrincipalCredentialType | 用於服務主體驗證的認證類型。 有效值為 "ServicePrincipalKey" 和 "ServicePrincipalCert"。 | 當驗證為 "AADServicePrincipal" 時為 [是] |
| servicePrincipalCredential | 服務主體認證。 <br/><br/>當您使用 "ServicePrincipalKey" 做為認證類型時， `servicePrincipalCredential` 可以是在已連結的服務部署時，Azure Data Factory 加密的字串。 或者，它可以是 Azure Key Vault 中的秘密參考。 <br/><br/>當您使用 "ServicePrincipalCert" 做為認證時， `servicePrincipalCredential` 必須是 Azure Key Vault 中憑證的參考。 | 當驗證為 "AADServicePrincipal" 時為 [是] |
| 使用者名稱 | 要連接到 Dynamics 的使用者名稱。 | 當驗證為 "Office365" 時為 [是] |
| 密碼 | 您指定為使用者名稱的使用者帳戶密碼。 將此欄位標記為 "SecureString"，以安全地將它儲存在 Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 當驗證為 "Office365" 時為 [是] |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果未指定任何值，屬性會使用預設的 Azure integration runtime。 | 針對來源為否，如果來源連結服務沒有整合執行時間，則為 [是] |

>[!NOTE]
>Dynamics 連接器先前使用了選擇性的**organizationName**屬性來識別您的 Dynamics CRM 或 dynamics 365 online 實例。 雖然該屬性仍然有效，但我們建議您改為指定新的**serviceUri**屬性，以取得更佳的實例探索效能。

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>範例：使用 Azure AD 服務的 Dynamics online-主體和金鑰驗證

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://www.crmdynamics.com",  
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
            "serviceUri": "https://www.crmdynamics.com", 
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
            "serviceUri": "https://www.crmdynamics.com",
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

相較于 Dynamics online 的其他屬性是**主機名稱**和**埠**。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是。 |
| deploymentType | Dynamics 執行個體的部署類型。 若為具有 IFD 的 Dynamics 內部部署，此值必須為 "OnPremisesWithIfd"。| 是。 |
| hostName | 內部部署 Dynamics 伺服器的主機名稱。 | 是。 |
| 連接埠 | 內部部署 Dynamics 伺服器的連接埠。 | 否。 預設值為443。 |
| organizationName | Dynamics 執行個體的組織名稱。 | 是。 |
| authenticationType | 連線到 Dynamics 伺服器時所使用的驗證類型。 如果是搭配 IFD 的 Dynamics 內部部署版，請指定 "Ifd"。 | 是。 |
| 使用者名稱 | 要連接到 Dynamics 的使用者名稱。 | 是。 |
| 密碼 | 您為 username 指定之使用者帳戶的密碼。 您可以將此欄位標記為 "SecureString"，以安全地將它儲存在 Data Factory 中。 或者，您可以在 Key Vault 中儲存密碼，然後在複製活動進行資料複製時，從該處提取。 請參閱[在金鑰保存庫中儲存認證](store-credentials-in-key-vault.md)深入了解。 | 是。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果未指定任何值，屬性會使用預設的 Azure integration runtime。 | [否] 用於 [來源]，[是] 表示接收。 |

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

若要將資料從和複製到 Dynamics，支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為 "DynamicsEntity"、"DynamicsCrmEntity" 或 "CommonDataServiceForAppsEntity"。 |是 |
| entityName | 要擷取之實體的邏輯名稱。 | 若活動來源指定為 "query"，則為否，而針對接收則為 [是] |

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

若要從 Dynamics 複製資料，複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為 "DynamicsSource"、"DynamicsCrmSource" 或 "CommonDataServiceForAppsSource"。 | 是 |
| 查詢 | FetchXML 是在 Dynamics online 和內部部署中使用的專屬查詢語言。 請參閱下列範例。 若要深入瞭解，請參閱[使用 FetchXML 建立查詢](https://msdn.microsoft.com/library/gg328332.aspx)。 | 如果 `entityName` 在資料集中指定了，則為 No |

>[!NOTE]
>即使您在 FetchXML 查詢中設定的資料行投影未包含 PK 資料行，還是一律會複製 PK 資料行。

> [!IMPORTANT]
>- 當您從 Dynamics 複製資料時，從 Dynamics 到 sink 的明確資料行對應是選擇性的。 但我們強烈建議使用對應，以確保具有決定性的複製結果。
>- 當 Data Factory 在撰寫 UI 中匯入架構時，它會推斷架構。 其方式是將 Dynamics 查詢結果的前幾個資料列取樣，以初始化來源資料行清單。 在這種情況下，會省略頂端資料列中沒有值的資料行。 如果沒有明確對應，相同的行為也適用于複製執行。 您可以在複製執行時間期間接受的對應中，檢查並新增更多資料行。

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

若要將資料複製到 Dynamics，[複製活動**接收**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為 "DynamicsSink"、"DynamicsCrmSink" 或 "CommonDataServiceForAppsSink"。 | 是。 |
| writeBehavior | 作業的寫入行為。 此值必須是 "Upsert"。 | 是 |
| alternateKeyName | 在您的實體上定義以執行 upsert 的替代索引鍵名稱。 | 否。 |
| writeBatchSize | 每個批次中寫入 Dynamics 的資料列計數。 | 否。 預設值是 10。 |
| ignoreNullValues | 在寫入作業期間，是否要從索引鍵欄位以外的輸入資料忽略 null 值。<br/><br/>有效值為**TRUE**和**FALSE**：<ul><li>**TRUE**：當您執行 upsert 或更新作業時，將目的地物件中的資料保持不變。 執行插入作業時，插入已定義的預設值。</li><li>**FALSE**：執行 upsert 或更新作業時，將目的地物件中的資料更新為 null 值。 執行插入作業時，插入 null 值。</li></ul> | 否。 預設值為**FALSE**。 |

>[!NOTE]
>適用于 Dynamics 接收器的接收**writeBatchSize**和複製活動**[parallelCopies](copy-activity-performance-features.md#parallel-copy)** 的預設值為10。 因此，100記錄預設會同時提交至 Dynamics。

針對 Dynamics 365 online，[每個組織有兩個並行批次呼叫](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)的限制。 如果超過該限制，則會在執行第一個要求之前擲回「伺服器忙碌」例外狀況。 將**writeBatchSize**保持在10以下，以避免進行並行呼叫的節流。

**WriteBatchSize**和**parallelCopies**的最佳組合取決於您實體的架構。 架構元素包括連結至這些呼叫的資料行數目、資料列大小，以及外掛程式、工作流程或工作流程活動的數目。 根據 Dynamics 服務，預設值**writeBatchSize** （10） &times; **parallelCopies** （10）是建議的設定。 此值適用于大多數的 Dynamics 實體，但可能不會提供最佳效能。 您可以藉由調整複製活動設定中的組合來微調效能。

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

當您從 Dynamics 複製資料時，下表會顯示從 Dynamics 資料類型到 Data Factory 過渡資料類型的對應。 若要瞭解複製活動如何對應至來源架構，以及如何將資料類型對應至接收，請參閱[架構和資料類型](copy-activity-schema-and-type-mapping.md)對應。

使用下列對應表，根據您的來源 Dynamics 資料類型，在資料集結構中設定對應的 Data Factory 資料類型：

| Dynamics 資料類型 | Data Factory 過渡期資料類型 | 支援作為來源 | 支援作為接收 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 布林值 | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓（請參閱[指引](#writing-data-to-a-lookup-field)） |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | 字串 | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓（請參閱[指引](#writing-data-to-a-lookup-field)） |
| AttributeType.ManagedProperty | 布林值 | ✓ | |
| AttributeType.Memo | 字串 | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓（請參閱[指引](#writing-data-to-a-lookup-field)） |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | 字串 | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> 不支援 Dynamics 資料類型**AttributeType. attributetype.calendarrules attributetype.partylist**、 **AttributeType MultiSelectPicklist**和**AttributeType. PartyList** 。

## <a name="writing-data-to-a-lookup-field"></a>將資料寫入查閱欄位

若要將資料寫入具有多個目標（例如客戶和擁有者）的查閱欄位，請遵循此指導方針和範例：

1. 讓您的來源同時包含域值和對應的目標機構名稱。
   - 如果所有記錄都對應到相同的目標實體，請確定下列其中一個條件：
      - 您的來源資料具有儲存目標機構名稱的資料行。
      - 您已在複製活動來源中新增額外的資料行，以定義目標實體。
   - 如果不同的記錄對應至不同的目標實體，請確定您的來源資料具有儲存對應目標機構名稱的資料行。

1. 將值和實體參考資料行從來源對應到接收。 實體參考資料行必須對應到具有特殊命名模式的虛擬資料行 `{lookup_field_name}@EntityReference` 。 資料行實際上不存在 Dynamics 中。 它是用來指出此資料行是所指定使用多目標查閱欄位的 metadata 資料行。

例如，假設來源有這兩個數據行：

- **GUID**類型的**CustomerField**資料行，這是 Dynamics 中目標實體的主要金鑰值。
- **String**類型的**目標**資料行，這是目標實體的邏輯名稱。

也假設您想要將這類資料複製到**Customer**類型的 sink Dynamics 實體欄位**CustomerField** 。

在複製活動資料行對應中，對應兩個數據行，如下所示：

- **CustomerField**至**CustomerField**。 這個對應是一般欄位對應。
- 將**目標**設為**CustomerField \@ EntityReference**。 Sink 資料行是代表實體參考的虛擬資料行。 在對應中輸入這類功能變數名稱，因為它們不會藉由匯入架構來顯示。

![Dynamics 查閱欄位資料行對應](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

如果您所有的來源記錄都對應至相同的目標實體，而您的來源資料未包含目標機構名稱，以下是快捷方式：在複製活動來源中，加入額外的資料行。 使用模式將新資料行命名 `{lookup_field_name}@EntityReference` 為，將值設定為目標機構名稱，然後照常進行資料行對應。 如果您的來源和接收資料行名稱完全相同，您也可以略過明確的資料行對應，因為複製活動依預設會依名稱對應資料行。

![Dynamics 查閱-加入實體參考資料行的欄位](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Data Factory 支援作為來源和接收的資料存放區清單，請參閱支援的[資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
