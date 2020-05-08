---
title: 使用 Blob 索引來管理和尋找 Azure Blob 儲存體上的資料（預覽）
description: 瞭解如何使用 Blob 索引標記來分類、管理和查詢，以探索 Blob 物件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f1a4d9af8a1b1095527078dd790e80ef45a5ee9a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722891"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>使用 Blob 索引來管理和尋找 Azure Blob 儲存體上的資料（預覽）

當資料集變得更大且更大時，尋找資料中的特定物件可能會很難且令人沮喪。 Blob 索引會使用機碼值索引標記屬性來提供資料管理和探索功能，讓您能夠分類和尋找單一容器或儲存體帳戶中所有容器內的物件。 之後，隨著資料變更的需求，物件可以藉由更新其索引標記來動態重新分類，同時與目前的容器組織保持原狀。 利用 Blob 索引可以將 blob 資料和相關聯的索引屬性合併到相同的服務上，藉此簡化開發工作;可讓您使用原生功能建立有效率且可調整的應用程式。 

Blob 索引可讓您：

- 使用索引鍵/值索引標記來動態分類 blob 以進行資料管理
- 在單一容器或整個儲存體帳戶中快速尋找特定標記的 blob
- 根據索引標記的評估，指定 blob Api 的條件式行為
- 利用 blob 平臺功能（例如[生命週期管理](storage-lifecycle-management-concepts.md)）上 advanced 控制項的索引標籤

假設您的儲存體帳戶中有數百萬個 blob 是由許多不同的應用程式所撰寫和存取的。 您想要從單一專案尋找所有相關的資料，但您不確定資料的範圍為何，因為可以使用不同的 blob 命名慣例，將資料分散到多個容器。 不過，您知道您的應用程式會根據其各自的專案和識別描述，上傳所有具有標記的資料。 除了搜尋數百萬個 blob 並比較名稱和屬性，您可以只使用`Project = Contoso`做為您的探索準則。 Blob 索引會篩選整個儲存體帳戶的所有容器，以快速尋找並只傳回一組 50 blob `Project = Contoso`。 

若要開始使用如何使用 Blob 索引的範例，請參閱[利用 Blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引標記和資料管理

容器和 blob 名稱前置詞是儲存資料的一維分類。 Blob 索引現在允許所有[blob 資料類型（區塊、附加或分頁）](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)的多重維度分類，並套用屬性標記。 這種多維度分類是以原生方式編制索引，可讓您快速查詢及尋找資料。

請考慮儲存體帳戶中的下列五個 blob：
>
> container1/transaction .csv  
> 為 storage2 container2/行銷活動 .docx  
> 相片/bannerphoto .png  
> 封存/已完成/2019review .pdf  
> logs/2020/01/01/logfile .txt  
>

這些 blob 目前是使用容器/虛擬資料夾/blob 名稱的前置詞來分隔。 使用 Blob 索引，您可以在這五個 blob 上`Project = Contoso`設定的索引標記屬性，以將它們分類在一起，同時維持其目前的前置片語織。 如此一來，您就不需要藉由公開使用儲存平臺的多維度索引來篩選和尋找資料，來移動資料。

## <a name="setting-blob-index-tags"></a>設定 Blob 索引標記

Blob 索引標籤是機碼值屬性，可套用至儲存體帳戶內的新或現有物件。 您可以使用 PutBlob、PutBlockList 或 CopyBlob 作業和選擇性的 x-ms 標記標頭，在上傳程式期間指定索引標記。 如果您的儲存體帳戶中已有 blob，您可以使用格式化的 XML 檔來呼叫 SetBlobTags，並在要求主體中指定 blob 索引標記屬性。 

請考慮下列可設定的範例標記

您可以在 blob 上套用單一標記來描述資料完成處理的時間。
>
> "processedDate" = ' 2020-01-01 '
>
您可以在 blob 上套用多個標記，以更具描述性的資料。
>
> "Project" = ' Contoso '  
> 「已分類」 = ' True '  
> 「狀態」 = 「未處理」  
> 「優先順序」 = ' 01 ' 
>

若要修改現有的索引標記屬性，您必須先取出現有的標記屬性、修改標記屬性，然後將取代為 SetBlobTags 作業。 若要從 blob 中移除所有索引標籤，請呼叫 SetBlobTags 作業，但不指定任何標記屬性。 Blob 索引標記是 blob 資料內容的子資源，因此 SetBlobTags 不會修改任何基礎內容，也不會變更 blob 的上次修改時間。

下列限制適用于 Blob 索引標籤：
- 每個 blob 最多可以有10個 blob 索引標籤
- 標記索引鍵的長度必須介於1到128個字元之間
- 標記值必須介於0到256個字元之間
- 標記索引鍵和值區分大小寫
- 標記索引鍵和值僅支援字串資料類型;任何數位或特殊字元將儲存為字串
- 標記索引鍵和值必須遵守下列命名規則：
  - 英數位元： a-z、a-z、0-9
  - 特殊字元：空格、加號、減號、句號、冒號、等於、底線、正斜線

## <a name="getting-and-listing-blob-index-tags"></a>取得和列出 Blob 索引標記

Blob 索引標籤會連同 blob 資料一起儲存為子資源，而且可以獨立于基礎 blob 資料內容中抓取。 一旦設定之後，就可以直接使用 GetBlobTags 作業來抓取或檢查單一 blob 的 blob 索引標籤。 具有`include:tags`參數的 ListBlobs 作業也會傳回容器內的所有 blob 及其套用的 blob 索引標記。 

對於至少有1個 blob 索引標籤的任何 blob，ListBlobs、GetBlob 和 GetBlobProperties 作業會傳回 x 毫秒-標記計數，指出 blob 上存在的 blob 索引標記計數。

## <a name="finding-data-using-blob-index-tags"></a>使用 Blob 索引標記尋找資料

在 blob 上設定 blob 索引標籤時，索引引擎會將這些索引鍵/值屬性公開成多維度索引。 當您的索引標籤存在於 blob 上，而且可以立即抓取時，可能需要一些時間，才會使用重新整理的索引標記屬性來更新 blob 索引。 更新 blob 索引之後，您現在可以利用 blob 儲存體所提供的原生查詢和探索功能。

FindBlobsByTags 作業可讓您取得已篩選的一組 blob，其索引標籤符合給定的 blob 索引查詢運算式。 Blob 索引支援在您儲存體帳戶內的所有容器之間進行篩選，或您可以將篩選範圍限定為單一容器。 因為所有 blob 索引標籤索引鍵和值都是字串，支援的關聯式運算子會針對索引標記值使用詞典編纂排序。

下列準則適用于 blob 索引篩選：
-   標記索引鍵應該以雙引號括住（"）
-   標記值和容器名稱應以單引號括住（'）
-   只有在特定容器名稱（亦即@container = ' 容器 '）上進行篩選時，才允許使用 @ 字元
- 篩選準則會在字串上套用詞典編纂排序
-   相同索引鍵上的相同側邊範圍作業無效（也就是「排名」 > ' 10 ' 和「Rank」 >= ' 15 '）
- 使用 REST 建立篩選運算式時，字元應該以 URI 編碼

下表顯示 FindBlobsByTags 的所有有效運算子：

|  運算子  |  描述  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | "Status" = ' 進行中 ' | 
|     >      |  大於 |  "Date" > ' 2018-06-18 ' |
|     >=     |  大於或等於 | 「優先順序」 >= ' 5 ' | 
|     <      |  小於    | 「Age」 < ' 32 ' |
|     <=     |  小於或等於  | "Company" <= ' Contoso ' |
|    AND     |  邏輯 and  | 「Rank」 >= ' 010 ' 和「Rank」 < ' 100 ' |
| @container |  將範圍設為特定容器   | @container= ' videofiles ' 和「狀態」 = 「完成」 |

## <a name="conditional-blob-operations-with-blob-index-tags"></a>具有 Blob 索引標記的條件式 Blob 作業
在 REST 2019-10-10 和更新版本中，大部分的[blob 服務 api](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)現在都支援條件式標頭，也就是 x 毫秒----------------------如果符合指定的 blob 索引條件 如果不符合條件，則會取得`error 412: The condition specified using HTTP conditional header(s) is not met`。

X 毫秒-if 標記標頭可以與其他現有的 HTTP 條件式標頭（If-match、If-None-Match 等等）結合。  如果要求中提供多個條件式標頭，則必須評估 true，作業才會成功。  所有條件式標頭會與邏輯 AND 有效率地結合。 

下表顯示條件式作業的所有有效運算子：

|  運算子  |  描述  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | "Status" = ' 進行中 ' |
|     <>     |   不等於   | 「狀態」  <>  「完成」  | 
|     >      |  大於 |  "Date" > ' 2018-06-18 ' |
|     >=     |  大於或等於 | 「優先順序」 >= ' 5 ' | 
|     <      |  小於    | 「Age」 < ' 32 ' |
|     <=     |  小於或等於  | "Company" <= ' Contoso ' |
|    AND     |  邏輯 and  | 「Rank」 >= ' 010 ' 和「Rank」 < ' 100 ' |
|     OR     |  邏輯 or   | 「狀態」 = 「完成」或「優先順序」 >= ' 05 ' |

> [!NOTE]
> 有兩個額外的運算子（不等於和邏輯 or）可用於 blob 作業的條件式 x 毫秒-if 標記標頭，但不存在於 FindBlobsByTags 作業中。

## <a name="platform-integrations-with-blob-index-tags"></a>使用 Blob 索引標記的平臺整合

Blob 索引的標籤不僅可協助您分類、管理及搜尋您的 blob 資料，還能提供與其他 Blob 服務功能的整合，例如[生命週期管理](storage-lifecycle-management-concepts.md)。 

### <a name="lifecycle-management"></a>生命週期管理

使用新的 blobIndexMatch 做為生命週期管理中的規則篩選器，您可以將資料移至較少層，或根據套用至 blob 的索引標籤來刪除資料。 這可讓您更精細地在規則中，而且只會在符合指定標記準則的情況下移動或刪除資料。

您可以將 blob 索引比對設定為在生命週期規則中設定的獨立篩選器，以對已標記的資料套用動作。 或者，您可以結合前置詞相符項和 blob 索引比對，以符合更特定的資料集。 將多個篩選套用至生命週期規則會視為邏輯和作業，因此只有在所有篩選準則都相符時，動作才會套用。 

下列範例生命週期管理規則適用于容器 ' videofiles ' 中的區塊 blob 和層 blob，只有在資料符合的 blob 索引標記準則時，才會```"Status" = 'Processed' AND "Source" == 'RAW'```封存儲存體。

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

## <a name="permissions-and-authorization"></a>許可權和授權

您可以使用下列其中一種方法來授權 Blob 索引的存取權：

- 藉由使用角色型存取控制（RBAC）將許可權授與 Azure Active Directory （Azure AD）安全性主體。 Microsoft 建議使用 Azure AD 以獲得更佳的安全性和易用性。 如需有關使用 Azure AD 搭配 blob 作業的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 blob 和佇列](../common/storage-auth-aad.md)。
- 藉由使用共用存取簽章（SAS）來委派 blob 索引的存取權。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。
- 藉由使用帳戶存取金鑰來授權具有共用金鑰的作業。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引標記是 blob 資料的子資源。 具有許可權的使用者或要讀取或寫入 blob 的 SAS 權杖，可能無法存取 blob 索引標記。 

### <a name="role-based-access-control"></a>角色型存取控制 
使用 AAD 身分[識別](../common/storage-auth-aad.md)的呼叫端可能會被授與下列許可權，以在 blob 索引標籤上操作。 

|   Blob 作業   |  RBAC 動作   |
|---------------------|----------------|
| 依標記尋找 Blob  | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/篩選 |
| 設定 Blob 標記         | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/標記/寫入 | 
| 取得 Blob 標記         | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/標記/讀取 |

需要與基礎 blob 資料分開的其他許可權，才能對標記執行操作。 「儲存體 Blob 資料參與者」角色將會被授與這三個許可權。 儲存體 Blob 資料讀取器將會被授與唯一「依標籤尋找 Blob」和「取得 Blob 標記」許可權。

### <a name="sas-permissions"></a>SAS 許可權 
使用[共用存取簽章（SAS）](../common/storage-sas-overview.md)的呼叫端可能會被授與已設定範圍的許可權，以在 blob 標記上運作。
#### <a name="blob-sas"></a>Blob SAS
您可以在 Blob 服務 SAS 中授與下列許可權，以允許存取 Blob 索引標記。 僅 Blob 讀取和寫入權限不足以允許讀取或寫入其索引標記。

|  權限  |  URI 符號  | 允許的作業 |
|--------------|--------------|--------------------|
|  索引標記  |      t         | 取得和設定 blob 的 blob 索引標記 |

#### <a name="container-sas"></a>容器 SAS
您可以在 Container service SAS 中授與下列許可權，以允許對 blob 標記進行篩選。  Blob 清單許可權不足以允許根據索引標籤來篩選 blob。

|  權限  |  URI 符號  | 允許的作業 |
|--------------|--------------|--------------------|
| 索引標記     |      f       | 尋找 blob 索引標記的 Blob | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>選擇中繼資料和 Blob 索引標記 
Blob 索引標記和中繼資料都提供將任意使用者定義的索引鍵/值屬性連同 Blob 資源一起儲存的功能。 兩者都可以直接抓取和設定，而不需要傳回或更改 blob 的內容。 您可以同時使用中繼資料和索引標記。

不過，只有 Blob 索引標記會自動編制索引，並由原生 Blob 服務進行查詢。 中繼資料無法以原生方式編制索引和查詢，除非您使用不同的服務，例如[Azure 搜尋服務](../../search/search-blob-ai-integration.md)。 Blob 索引標籤也有額外的讀取/篩選和寫入權限，與基礎 blob 資料分開。 中繼資料會利用與 blob 相同的許可權，並在 GetBlob 或 GetBlobProperties 作業中當做 HTTP 標頭傳回。 Blob 索引標記會使用[Microsoft 管理的金鑰](../common/storage-service-encryption.md)在待用時加密，而中繼資料則會使用針對 blob 資料所指定的相同加密金鑰進行待用加密。 

下表摘要說明中繼資料和 Blob 索引標記之間的差異：

|              |   中繼資料   |   Blob 索引標記  |
|--------------|--------------|--------------------|
| **限制**         | 無數值限制;總共 8 KB;不區分大小寫 | 每個 blob 最多10個標記;每個標記768個位元組;區分大小寫 |
| **更新**      | 封存層不允許;SetBlobMetadata 會取代所有現有的中繼資料;SetBlobMetadata 會變更 blob 的上次修改時間 | 允許所有存取層;SetBlobTags 會取代所有現有的標記;SetBlobTags 不會變更 blob 的上次修改時間 |
| **Storage**        | 與 blob 資料一起儲存 |  Blob 資料的子資源 | 
| **編制索引 & 查詢** | N/A 原生;必須使用個別的服務，例如 Azure 搜尋服務 | 是，blob 儲存體內建的原生索引和查詢功能 |
| [加密]**** | 待用時使用相同的加密金鑰加密 blob 資料 |  使用 Microsoft 管理的加密金鑰進行待用加密 |
| **定價**   | 中繼資料的大小會包含在 blob 的儲存體成本中 |    已修正每個索引標記的成本 | 
| **標頭回應** | 在 GetBlob 和 GetBlobProperties 中傳回做為標頭的中繼資料 | 在 GetBlob 或 GetBlobProperties 中傳回的 TagCount;只有在 GetBlobTags 和 ListBlobs 中傳回的標記 |
| **權限**  |    Blob 資料的讀取或寫入權限延伸至中繼資料 |    需要其他許可權才能讀取/篩選或寫入標記 |

## <a name="pricing"></a>定價
Blob 索引定價目前為公開預覽狀態，且可能會變更為正式運作。 客戶需支付儲存體帳戶內的 blob 索引標記總數（以當月為平均值）。 編制索引引擎不會產生任何費用。 SetBlobTags、GetBlobTags 和 FindBlobsByTags 的要求會根據其各自的作業類型收費。 [若要深入瞭解，請參閱區塊 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>區域可用性和儲存體帳戶支援

Blob 索引目前僅適用于一般用途 v2 （GPv2）帳戶。 在 Azure 入口網站中，您可以將現有的一般用途（GPv1）帳戶升級至 GPv2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

在公開預覽中，Blob 索引目前僅適用于下列選取區域：
- 法國中部
- 法國南部

若要開始使用，請參閱[利用 Blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 請參閱本文的條件一節。 若要註冊預覽，請參閱本文的註冊您的訂用帳戶一節。 您必須先註冊您的訂用帳戶，才能在您的儲存體帳戶上使用 Blob 索引。

### <a name="register-your-subscription-preview"></a>註冊您的訂用帳戶（預覽）
因為 Blob 索引僅限公開預覽，所以您必須先註冊您的訂用帳戶，才能使用此功能。 若要提交要求，請執行下列 PowerShell 或 CLI 命令。

#### <a name="register-by-using-powershell"></a>使用 PowerShell 進行註冊
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 進行註冊
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>條件和已知問題（預覽）
本節說明 Blob 索引目前公開預覽版中的已知問題和條件。 如同大部分的預覽，這項功能不應用於生產環境工作負載，直到它達到 GA，因為行為可能會改變。

-   針對預覽版本，您必須先註冊您的訂用帳戶，才能在預覽區域中針對您的儲存體帳戶使用 Blob 索引。
-   預覽中目前僅支援 GPv2 帳戶。 Blob、BlockBlobStorage 及 HNS 已啟用 DataLake Gen2 帳戶目前不支援 Blob 索引。
-   使用索引標籤上傳分頁 blob 目前不會保存標記。 您必須在上傳分頁 blob 後設定標記。
-   當篩選的範圍設定為單一容器時， @container只有在篩選條件運算式中的所有索引標籤都是相等檢查（key = value）時，才能傳遞。 
-   使用範圍運算子搭配和條件時，您只能指定相同的索引標籤索引鍵名稱（Age > ' 013 '，而 Age < ' 100 '）。
-   目前不支援版本控制和 Blob 索引。 Blob 索引標記會保留給版本，但目前不會傳遞至 blob 索引引擎。
-   目前不支援帳戶容錯移轉。 在容錯移轉之後，blob 索引可能無法正確更新。
-   生命週期管理目前僅支援 Blob 索引相符的等號檢查。
-   CopyBlob 不會將 blob 索引標記從來源 blob 複製到新的目的地 blob。 您可以指定要在複製作業期間套用至目的地 blob 的標記。 
-   標記會在建立快照集時保存;不過，目前不支援升級快照集，而且可能會導致設定空的標記。

## <a name="faq"></a>常見問題集

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Blob 索引可以協助我篩選和查詢 blob 內的內容嗎？ 
否，Blob 索引標籤可協助您找出所需的 blob。 如果您需要在 blob 內搜尋，請使用 [查詢加速] 或 [Azure 搜尋服務]。

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Blob 索引標籤和 Azure Resource Manager 標記是否相關？
否，Azure Resource Manager 標籤可協助組織控制平面資源，例如訂用帳戶、資源群組和儲存體帳戶。 Blob 索引標籤可在資料平面資源（例如儲存體帳戶內的 blob）上提供物件管理和探索。

## <a name="next-steps"></a>後續步驟

請參閱如何使用 Blob 索引的範例。 請參閱[利用 Blob 索引來管理和尋找資料](storage-blob-index-how-to.md)

