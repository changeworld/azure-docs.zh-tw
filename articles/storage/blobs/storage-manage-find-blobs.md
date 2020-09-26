---
title: 使用 Blob 索引 (預覽) 管理和尋找 Azure Blob 儲存體上的資料
description: 瞭解如何使用 blob 索引標記來分類、管理和查詢 blob 物件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.custom: references_regions
ms.openlocfilehash: 70d0e31809227d5e27f8f2b22a7703d5a8ccca3c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275059"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-preview"></a>使用 Blob 索引 (預覽版來管理和尋找 Azure Blob 資料) 

當資料集變得更大且更大時，在資料中尋找特定物件可能會很困難且挫折。 Blob 索引會使用索引鍵/值索引標籤屬性來提供資料管理和探索功能，可讓您在單一容器內或在儲存體帳戶中的所有容器之間分類和尋找物件。 之後，隨著資料變更的需求，可以透過更新其索引標籤，並與目前的容器組織保持在一起，來動態分類物件。 利用 blob 索引可將 blob 資料和相關聯的索引屬性合併在相同的服務上，以簡化開發工作;使用原生功能，可讓您建立有效率且可調整的應用程式。

Blob 索引可讓您：

- 使用索引鍵/值索引標籤，以資料管理動態分類 blob
- 在單一容器或整個儲存體帳戶中快速尋找特定標記的 blob
- 根據索引標記的評估來指定 blob Api 的條件式行為
- 利用 blob 平臺功能（例如[生命週期管理](storage-lifecycle-management-concepts.md)）上的 advanced 控制項索引標記

請考慮您的儲存體帳戶在許多不同的應用程式撰寫和存取的情況下，有數百萬個 blob。 您想要尋找單一專案中的所有相關資料，但不確定範圍中的資料可以分散到多個具有不同 blob 命名慣例的容器。 不過，您知道您的應用程式會根據其個別專案和識別描述，上傳標記的所有資料。 您可以直接使用作為探索準則，而不是搜尋數百萬個 blob 以及比較名稱和屬性 `Project = Contoso` 。 Blob 索引會篩選整個儲存體帳戶中的所有容器，以快速尋找並傳回一組 50 blob `Project = Contoso` 。

若要開始使用 blob 索引的範例，請參閱 [利用 blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引標記和資料管理

容器和 blob 名稱前置詞是您儲存資料的一維分類。 Blob 索引現在可讓您針對所有的 blob 資料類型進行多重維度分類 [， (區塊、附加或頁面) 加上 ](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 套用的屬性標記。 這種多維分類會以原生方式編制索引，並可讓您快速查詢及尋找您的資料。

請考慮您儲存體帳戶中的下列五個 blob：

- *container1/transaction.csv*
- *container2/campaign.docx*
- *相片/bannerphoto.png*
- *封存/已完成/2019review.pdf*
- *logs/2020/01/01/logfile.txt*


這些 blob 目前使用 *容器/虛擬資料夾/blob 名稱*的前置詞來分隔。 使用 blob 索引時，您可以在這五個 blob 上設定的索引標記屬性，將 `Project = Contoso` 它們分類在一起，同時維持其目前的前置片語織。 這樣就不需要藉由公開使用儲存平臺的多維度索引來篩選和尋找資料的能力來移動資料。

## <a name="setting-blob-index-tags"></a>設定 blob 索引標記

Blob 索引標記是索引鍵/值屬性，可套用至儲存體帳戶內的新或現有物件。 您可以使用 PutBlob、PutBlockList 或 CopyBlob 作業以及選擇性的 x--標記標頭，在上傳過程中指定索引標籤。 如果您的儲存體帳戶中已經有 blob，則可以使用格式化的 XML 檔來呼叫 SetBlobTags，並在要求本文中指定 blob 索引標記屬性。

請考慮下列可設定的範例標記

您可以在 blob 上套用單一標記，以描述資料完成處理的時間。

> "processedDate" = ' 2020-01-01 '

您可以在 blob 上套用多個標記，以更具描述性的資料。

> "Project" = ' Contoso '  
> 「分類」 = ' True '  
> 「狀態」 = 「未處理」  
> "Priority" = ' 01 '

若要修改現有的索引標記屬性，您必須先取出現有的標記屬性、修改標記屬性，然後以 SetBlobTags 作業取代。 若要從 blob 中移除所有索引標籤，請呼叫未指定標記屬性的 SetBlobTags 作業。 Blob 索引標記是 blob 資料內容的子資源，因此 SetBlobTags 不會修改任何基礎內容，也不會變更 blob 上次修改時間或 eTag (實體標記) 。 您可以為所有目前的基底 blob 和先前的版本建立或修改索引標記;不過，快照集或已虛刪除的 blob 上的標記無法修改。

下列限制適用于 blob 索引標記：
- 每個 blob 最多可以有10個 blob 索引標記
- 標記索引鍵必須介於1到128個字元之間
- 標記值的長度必須介於0到256個字元之間
- 標記索引鍵和值會區分大小寫
- 標記索引鍵和值只支援字串資料類型;任何數位、日期、時間或特殊字元都會儲存為字串
- 標記索引鍵和值必須遵守下列命名規則：
  - 字母數位字元： a-z、a-z、0-9
  - 特殊字元：空格、加號、減號、句號、冒號、等於、底線、正斜線

## <a name="getting-and-listing-blob-index-tags"></a>取得和列出 blob 索引標記

Blob 索引標記會連同 blob 資料一起儲存為子資源，而且可以獨立于基礎 blob 資料內容中進行抓取。 一旦設定之後，您就可以使用 GetBlobTags 作業立即抓取或審核單一 blob 的 blob 索引標記。 具有參數的 ListBlobs 作業 `include:tags` 也會傳回容器內的所有 blob 及其套用的 blob 索引標記。

對於至少有1個 blob 索引標記的任何 blob，會在 ListBlobs、GetBlob 和 GetBlobProperties 作業中傳回 x-毫秒-標記計數，指出存在於 blob 的 blob 索引標記計數。

## <a name="finding-data-using-blob-index-tags"></a>使用 blob 索引標記尋找資料

在 blob 上設定 blob 索引標記時，索引引擎會將這些索引鍵/值屬性公開至多維度索引。 當您的索引標籤存在於 blob 上而且可以立即抓取時，可能需要一些時間，才會使用重新整理的索引標記屬性來更新 blob 索引。 更新 blob 索引之後，您現在可以利用 blob 儲存體所提供的原生查詢和探索功能。

FindBlobsByTags 作業可讓您取得已篩選的一組 blob，其索引標記符合指定的 blob 索引查詢運算式。 Blob 索引支援在儲存體帳戶內的所有容器之間進行篩選，或您可以將篩選範圍限定為單一容器。 因為所有 blob 索引標記索引鍵和值都是字串，所以支援的關聯式運算子會對索引標記值使用詞典編纂排序。

下列準則適用于 blob 索引篩選：
- 標記索引鍵應該以雙引號括住 ( ") 
- 標記值和容器名稱應以單引號括住 ( ' ) 
- @ 字元只可用於篩選特定的容器名稱 (亦即 @container = ' 容器名稱 ' ) 
- 篩選準則會套用到字串上的詞典編纂排序
- 相同索引鍵上的相同側邊範圍作業無效 (例如 "Rank" > ' 10 ' 和 "Rank" >= ' 15 ' ) 
- 使用 REST 建立篩選運算式時，字元應該是以 URI 編碼

下表顯示所有適用于 FindBlobsByTags 的有效運算子：

|  運算子  |  說明  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | 「狀態」 = 「進行中」 |
|     >      |  大於 | "Date" > ' 2018-06-18 ' |
|     >=     |  大於或等於 | "Priority" >= ' 5 ' |
|     <      |  小於   | "Age" < ' 32 ' |
|     <=     |  小於或等於  | "Company" <= ' Contoso ' |
|    AND     |  邏輯 and  | "Rank" >= ' 010 ' 和 "Rank" < ' 100 ' |
| @container | 將範圍設為特定容器 | @container = ' videofiles ' 和 "status" = ' done ' |

> [!NOTE]
> 在標記上設定和查詢時，請熟悉字典順序。
> - 數位會在字母之前排序。 數位會根據第一個數位來排序。
> - 大寫字母排序在小寫字母之前。
> - 符號不是標準的。 某些符號會在數值之前排序。 其他符號會在字母之前或之後排序。

## <a name="conditional-blob-operations-with-blob-index-tags"></a>具有 blob 索引標記的條件式 blob 作業
在 REST 2019-10-10 版和更新版本中，大部分的 [blob 服務 api](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) 現在支援條件式標頭（x-------------------------如果符合指定的 blob 索引條件， 如果不符合條件，您將會得到 `error 412: The condition specified using HTTP conditional header(s) is not met` 。

如果-Match，則 x------------------------------match 和 ) 的 (。  如果要求中提供多個條件式標頭，它們必須評估為 true，作業才會成功。  所有條件式標頭都會有效地與邏輯 AND 合併。

下表顯示條件式作業的所有有效運算子：

|  運算子  |  說明  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | 「狀態」 = 「進行中」 |
|     <>     |   不等於   | 「狀態」  <>  「完成」  |
|     >      |  大於 | "Date" > ' 2018-06-18 ' |
|     >=     |  大於或等於 | "Priority" >= ' 5 ' |
|     <      |  小於   | "Age" < ' 32 ' |
|     <=     |  小於或等於  | "Company" <= ' Contoso ' |
|    AND     |  邏輯 and  | "Rank" >= ' 010 ' 和 "Rank" < ' 100 ' |
|     或者     | 邏輯 or   | "Status" = ' Done ' 或 "Priority" >= ' 05 ' |

> [!NOTE]
> 有兩個額外的運算子，不等於和邏輯 or，在 blob 作業的條件式 x--if-tag 標頭中，但不存在於 FindBlobsByTags 作業中。

## <a name="platform-integrations-with-blob-index-tags"></a>使用 blob 索引標記的平臺整合

Blob 索引的標記不僅可協助您分類、管理及搜尋 blob 資料，還可提供與其他 Blob 服務功能的整合，例如 [生命週期管理](storage-lifecycle-management-concepts.md)。

### <a name="lifecycle-management"></a>生命週期管理

使用新的 blobIndexMatch 做為生命週期管理中的規則篩選準則，您可以將資料移至較低層級，或根據套用至 blob 的索引標記來刪除資料。 這可讓您更精細地在規則中，並只在符合指定的標記準則時移動或刪除資料。

您可以將 blob 索引比對設定為生命週期規則中所設定的獨立篩選器，以對已標記的資料套用動作。 或者，您可以結合前置詞比對和 blob 索引相符，以符合更特定的資料集。 將多個篩選套用至生命週期規則會視為邏輯 AND 運算，因此只有在所有篩選準則相符時，才會套用動作。

下列範例生命週期管理規則只適用于容器 ' videofiles ' 中的區塊 blob，以及只有當資料符合的 blob 索引標記準則時，才會封存儲存體 ```"Status" = 'Processed' AND "Source" == 'RAW'``` 。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
![Azure 入口網站中生命週期管理的 Blob 索引符合規則範例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>許可權與授權

您可以使用下列其中一種方法來授權存取 blob 索引：

- 使用以角色為基礎的存取控制 (RBAC) 將許可權授與 Azure Active Directory (Azure AD) 安全性主體。 Microsoft 建議使用 Azure AD，以獲得更高的安全性與易用性。 如需搭配使用 Azure AD 與 blob 作業的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](../common/storage-auth-aad.md)。
- 使用共用存取簽章 (SAS) 來委派 blob 索引的存取權。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。
- 使用帳戶存取金鑰來授權具有共用金鑰的作業。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引標記是 blob 資料的子資源。 具有許可權的使用者或可讀取或寫入 blob 的 SAS 權杖，可能無法存取 blob 索引標記。

### <a name="role-based-access-control"></a>角色型存取控制
使用 Azure AD 身分 [識別](../common/storage-auth-aad.md) 的呼叫端可能會被授與下列許可權，以在 blob 索引標記上操作。

|   Blob 作業  |  RBAC 動作   |
|--------------------|----------------|
| 依標記尋找 Blob | Microsoft. 儲存體/storageAccounts/blobServices/容器/blob/篩選/動作 |
| 設定 Blob 標記      | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/write |
| 取得 Blob 標記      | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/read |

需要其他與基礎 blob 資料分開的許可權，才能在標記上運作。 儲存體 Blob 資料擁有者角色將會被授與這三個許可權。 儲存體 Blob 資料讀取器將依標記授與唯一尋找 Blob，並取得 Blob 標記許可權。

### <a name="sas-permissions"></a>SAS 許可權
使用 [共用存取簽章 (SAS) ](../common/storage-sas-overview.md) 的呼叫端可能會被授與範圍許可權，以在 blob 標記上操作。

#### <a name="blob-sas"></a>Blob SAS
您可以在 Blob 服務 SAS 中授與下列許可權，以允許存取 blob 索引標記。 Blob 的讀取和寫入權限不足以允許讀取或寫入其索引標記。

|  權限  |  URI 符號  | 允許的作業 |
|--------------|--------------|--------------------|
|  索引標記  |      t      | 取得和設定 blob 的 blob 索引標記 |

#### <a name="container-sas"></a>容器 SAS
您可以在 Container service SAS 中授與下列許可權，以允許對 blob 標記進行篩選。  Blob 清單許可權不足以允許依索引標記篩選 blob。

|  權限  |  URI 符號  | 允許的作業 |
|--------------|--------------|--------------------|
| 索引標記   |      f      | 尋找具有 blob 索引標記的 Blob |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>在中繼資料和 blob 索引標記之間進行選擇
Blob 索引標記和中繼資料都提供將任意使用者定義索引鍵/值屬性與 blob 資源一起儲存的功能。 兩者都可以直接抓取和設定，而不需要傳回或改變 blob 的內容。 您可以利用中繼資料和索引標記。

不過，只有 blob 索引標記會自動編制索引，並由原生 blob 服務進行查詢。 除非您使用不同的服務（例如 [Azure 搜尋](../../search/search-blob-ai-integration.md)服務），否則無法以原生方式編制中繼資料的索引和查詢。 Blob 索引標記也有額外的讀取/篩選和寫入權限，與基礎 blob 資料分開。 中繼資料會利用與 blob 相同的許可權，並在 GetBlob 或 GetBlobProperties 作業中以 HTTP 標頭傳回。 Blob 索引標記會在待用時使用 [Microsoft 管理的金鑰](../common/storage-service-encryption.md) 進行加密，而中繼資料則會使用針對 blob 資料指定的相同加密金鑰進行待用加密。

下表摘要說明中繼資料與 blob 索引標記之間的差異：

|              |   中繼資料   |   Blob 索引標記  |
|--------------|--------------|--------------------|
| **限制**      | 沒有數值限制;總共 8 KB;不區分大小寫 | 每個 blob 最多10個標記;每個標記768個位元組;區分大小寫 |
| **更新**    | 封存層不允許;SetBlobMetadata 會取代所有現有的中繼資料;SetBlobMetadata 變更 blob 的上次修改時間 | 允許所有存取層;SetBlobTags 會取代所有現有的標記;SetBlobTags 不會變更 blob 的上次修改時間 |
| **Storage**     | 與 blob 資料儲存 | 子資源至 blob 資料 |
| **& 查詢編制索引** | N/A 原生;必須使用不同的服務，例如 Azure 搜尋服務 | 是，blob 儲存體內建的原生索引和查詢功能 |
| **加密** | 使用相同的加密金鑰（用於 blob 資料）進行靜態加密 | 使用 Microsoft 管理的加密金鑰進行靜態加密 |
| **定價** | 中繼資料的大小包含在 blob 的儲存成本中 | 每個索引標籤的固定成本 |
| **標頭回應** | 在 GetBlob 和 GetBlobProperties 中傳回為標頭的中繼資料 | TagCount 傳回于 GetBlob 或 GetBlobProperties;只在 GetBlobTags 和 ListBlobs 中傳回的標記 |
| **權限**  | Blob 資料的讀取或寫入權限延伸至中繼資料 | 需要其他許可權才能讀取/篩選或寫入標記 |
| **命名** | 中繼資料名稱必須遵守 c # 識別碼的命名規則 | Blob 索引標記支援更廣泛的英數位元字元 |

## <a name="pricing"></a>定價
Blob 索引定價目前處於公開預覽狀態，可能會變更為正式運作。 客戶需支付儲存體帳戶內的 blob 索引標籤總數（每月平均）。 編制索引引擎不會產生任何費用。 SetBlobTags、GetBlobTags 和 FindBlobsByTags 的要求會根據其各自的作業類型計費。 [若要深入瞭解，請參閱封鎖 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>區域可用性和儲存體帳戶支援

Blob 索引目前僅適用于一般用途 v2 (GPv2) 使用階層命名空間的帳戶 (HNS) 停用。 一般用途 (GPV1) 帳戶不受支援，但您可以將任何 GPv1 帳戶升級到 GPv2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

在公開預覽中，blob 索引目前僅適用于下列選取區域：
- 加拿大中部
- 加拿大東部
- 法國中部
- 法國南部

若要開始使用，請參閱 [利用 blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 請參閱本文的「條件」一節。 若要註冊預覽，請先註冊您的訂用帳戶，然後才可以在儲存體帳戶上使用 blob 索引。

### <a name="register-your-subscription-preview"></a> (預覽版註冊您的訂用帳戶) 
因為 blob 索引只處於公開預覽狀態，所以您必須註冊您的訂用帳戶，才能使用此功能。 若要提交要求，請執行下列 PowerShell 或 CLI 命令。

#### <a name="register-by-using-powershell"></a>使用 PowerShell 進行註冊
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 註冊
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a> (預覽) 的條件和已知問題
本節說明目前 blob 索引的公開預覽中的已知問題和條件。 如同大部分的預覽，這項功能不應該用於生產工作負載，直到它達到 GA，因為行為可能會變更。

- 針對預覽版本，您必須先註冊您的訂用帳戶，才能在預覽區域中使用儲存體帳戶的 blob 索引。
- 預覽版目前僅支援 GPv2 帳戶。 Blob 索引目前不支援 blob、BlockBlobStorage 和 HNS 啟用的 DataLake Gen2 帳戶。 GPv1 帳戶將不受支援。
- 上傳具有索引標記的分頁 blob 目前不會保存標記。 您必須在上傳分頁 blob 之後設定標記。
- 當篩選的範圍設定為單一容器時， @container 只有在篩選條件運算式中的所有索引標籤都是 (索引鍵 = 值) 的相等檢查時，才能傳遞。
- 搭配 AND 條件使用範圍運算子時，您只能指定相同的索引標記索引鍵名稱 (Age > ' 013 ' 和 Age < ' 100 ' ) 。
- 目前不支援版本控制和 blob 索引。 Blob 索引標記會保留給版本，但目前不會傳遞至 blob 索引引擎。
- 目前不支援帳戶容錯移轉。 在容錯移轉之後，blob 索引可能無法正確更新。
- 生命週期管理目前僅支援具有 blob 索引相符的相等檢查。
- CopyBlob 不會從來源 blob 將 blob 索引標記複製到新的目的地 blob。 您可以在複製作業期間，指定要套用至目的地 blob 的標記。
- CopyBlob (Async copy) 從另一個儲存體帳戶，且目的地 blob 上套用的標記目前會導致 blob 索引引擎不會傳回篩選集中的 blob 及其標記。 建議您在過渡期間使用 CopyBlob from URL (同步複製) 。
- 標記會在快照集建立時保存;不過，目前不支援升階快照集，而且可能會導致空的標記集。

## <a name="faq"></a>常見問題集

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Blob 索引是否可協助我篩選和查詢 blob 內的內容？
否，blob 索引標記可協助您尋找所需的 blob。 如果您需要在 blob 內搜尋，請使用查詢加速或 Azure 搜尋服務。

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>有關 blob 索引標記值是否有任何特殊考慮？
Blob 索引標記只支援字串資料類型，而且查詢會以字典順序來傳回結果。 對於數位，建議將數位填補零。 針對日期和時間，建議將儲存為符合 ISO 8601 規範的格式。

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Blob 索引標記和 Azure Resource Manager 標記是否相關？
否，Resource Manager 標記可協助組織控制平面資源，例如訂用帳戶、資源群組和儲存體帳戶。 Blob 索引標記可提供資料平面資源的物件管理和探索，例如儲存體帳戶內的 blob。

## <a name="next-steps"></a>後續步驟

如需如何利用 blob 索引的範例，請參閱 [利用 blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

深入瞭解 [生命週期管理](storage-lifecycle-management-concepts.md) ，並設定 blob 索引相符的規則。

