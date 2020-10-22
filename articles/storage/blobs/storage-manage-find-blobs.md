---
title: '使用 Blob 索引標記管理及尋找 Azure Blob 資料 (預覽) '
description: 瞭解如何使用 blob 索引標記來分類、管理和查詢 blob 物件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 8f1ea67605be3aee6257c293aea3db617d885645
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370248"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>使用 Blob 索引標記管理及尋找 Azure Blob 資料 (預覽) 

當資料集變得更大時，在資料中尋找特定物件可能會很困難。 Blob 索引標記會使用索引鍵/值索引標籤屬性來提供資料管理和探索功能。 您可以在單一容器內或在儲存體帳戶中的所有容器之間分類和尋找物件。 當資料需求變更時，可以藉由更新索引標籤來動態分類物件。 物件可以與目前的容器組織就地保持在一起。

Blob 索引標記可讓您：

- 使用索引鍵/值索引標籤動態分類 blob
- 跨整個儲存體帳戶快速尋找特定標記的 blob
- 根據索引標記的評估來指定 blob Api 的條件式行為
- 在[blob 生命週期管理](storage-lifecycle-management-concepts.md)等功能上使用索引標籤來進行 advanced 控制項

請考慮您的儲存體帳戶中有數百萬個 blob 的案例，由許多不同的應用程式存取。 您想要尋找單一專案中的所有相關資料。 您不確定在範圍內的資料可以分散到多個具有不同命名慣例的容器。 不過，您的應用程式會以其專案為基礎的標記上傳所有資料。 您可以使用 `Project = Contoso` 做為探索準則，而不是搜尋數百萬個 blob 以及比較名稱和屬性。 Blob 索引會篩選整個儲存體帳戶中的所有容器，以快速尋找並傳回一組 50 blob `Project = Contoso` 。

若要開始使用 blob 索引的範例，請參閱 [使用 blob 索引標記來管理和尋找資料](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引標記和資料管理

容器和 blob 名稱前置詞是一維的分類。 Blob 索引標記允許 blob 資料類型的多維度分類 [ (區塊、附加或頁面) ](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。 多維度分類是由 Azure Blob 儲存體以原生方式編制索引，因此您可以快速找到您的資料。

請考慮您儲存體帳戶中的下列五個 blob：

- *container1/transaction.csv*
- *container2/campaign.docx*
- *相片/bannerphoto.png*
- *封存/已完成/2019review.pdf*
- *logs/2020/01/01/logfile.txt*


這些 blob 會使用 *容器/虛擬資料夾/blob 名稱*的前置詞來分隔。 您可以設定 `Project = Contoso` 這五個 blob 的索引標記屬性，將它們分類在一起，同時維持其目前的前置片語織。 加入索引標籤可讓您藉由使用索引來篩選和尋找資料，來免除移動資料的需要。

## <a name="setting-blob-index-tags"></a>設定 blob 索引標記

Blob 索引標記是索引鍵/值屬性，可套用至儲存體帳戶內的新或現有物件。 您可以使用 [Put blob](/rest/api/storageservices/put-blob)、 [put Block List](/rest/api/storageservices/put-block-list)或 [Copy blob](/rest/api/storageservices/copy-blob) 作業和選擇性標頭，在上傳過程中指定索引卷 `x-ms-tags` 標。 如果您的儲存體帳戶中已經有 blob，請呼叫 [Set Blob 標記](/rest/api/storageservices/set-blob-tags) ，並在要求本文中傳遞具有索引標記的格式化 XML 檔。

> [!IMPORTANT]
> [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者以及具有共用存取簽章的任何人都可以執行設定 blob 索引標籤，該簽章具有存取 blob 標記的許可權， (`t` SAS 許可權) 。
>
> 此外，具有許可權的 RBAC 使用者 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 可以執行這項操作。

您可以在 blob 上套用單一標記，以描述資料完成處理的時間。

> "processedDate" = ' 2020-01-01 '

您可以在 blob 上套用多個標記，以更具描述性的資料。

> "Project" = ' Contoso '  
> 「分類」 = ' True '  
> 「狀態」 = 「未處理」  
> "Priority" = ' 01 '

若要修改現有的索引標記屬性，請取出現有的標記屬性、修改標記屬性，然後以 [設定 Blob 標記](/rest/api/storageservices/set-blob-tags) 作業取代。 若要從 blob 中移除所有索引標籤，請呼叫 `Set Blob Tags` 未指定任何標記屬性的作業。 Blob 索引標記是 blob 資料內容的 subresource， `Set Blob Tags` 不會修改任何基礎內容，也不會變更 blob 的上次修改時間或 eTag。 您可以為所有目前的基底 blob 和先前的版本建立或修改索引標記。 不過，您無法修改快照集或虛刪除 blob 上的標記。

下列限制適用于 blob 索引標記：

- 每個 blob 最多可以有10個 blob 索引標記
- 標記索引鍵必須介於1到128個字元之間
- 標記值必須介於零到256個字元之間
- 標記索引鍵和值會區分大小寫
- 標記索引鍵和值只支援字串資料類型。 任何數位、日期、時間或特殊字元都會儲存為字串
- 標記索引鍵和值必須遵守下列命名規則：
  - 英數位元：
    - **a** 到 **z** (小寫字母)
    - **A** 到 **Z** (大寫字母)
    - **0** 到 **9** (數字)
  - 有效的特殊字元：空格、加號、減號、句號、冒號、等於、底線、正斜線 (` +-.:=_/`) 

## <a name="getting-and-listing-blob-index-tags"></a>取得和列出 blob 索引標記

Blob 索引標記會連同 blob 資料一起儲存為 subresource，而且可以獨立于基礎 blob 資料內容中進行取出。 您可以使用「 [取得 Blob 標記](/rest/api/storageservices/get-blob-tags) 」作業來抓取單一 blob 的 blob 索引標記。 具有參數的 [清單 blob](/rest/api/storageservices/list-blobs) 作業 `include:tags` 也會傳回容器內的所有 blob 及其 blob 索引標記。

> [!IMPORTANT]
> [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者以及具有共用存取簽章的任何人，都可以執行「取得和列出 blob 索引標記」，其具有存取 blob 標記的許可權， (`t` SAS 許可權) 。
>
> 此外，具有許可權的 RBAC 使用者 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 可以執行這項操作。

對於至少有一個 blob 索引標記的任何 blob， `x-ms-tag-count` 會傳回 [清單 blob](/rest/api/storageservices/list-blobs)、 [取得 Blob](/rest/api/storageservices/get-blob)，以及 [取得 blob 屬性](/rest/api/storageservices/get-blob-properties) 作業，指出 blob 上的索引標記計數。

## <a name="finding-data-using-blob-index-tags"></a>使用 blob 索引標記尋找資料

索引引擎會將您的索引鍵/值屬性公開至多維度索引。 設定索引標籤之後，這些標記會存在於 blob 上，並且可以立即取出。 Blob 索引可能需要一些時間才會更新。 更新 blob 索引之後，您可以使用 Blob 儲存體所提供的原生查詢和探索功能。

[ [依標記尋找 blob](/rest/api/storageservices/find-blobs-by-tags) ] 作業可讓您取得一組已篩選的 blob，其索引標記符合指定的查詢運算式。 `Find Blobs by Tags` 支援在儲存體帳戶內的所有容器之間進行篩選，或您可以將篩選範圍限定為單一容器。 由於所有索引標記索引鍵和值都是字串，因此關聯式運算子會使用詞典編纂排序。

> [!IMPORTANT]
> [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者和具有共用存取簽章的任何人都可以執行使用 blob 索引標記來尋找資料，而該簽章具有可依標記尋找 blob 的許可權 `f`)  (SAS 許可權。
>
> 此外，具有許可權的 RBAC 使用者 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 可以執行這項操作。

下列準則適用于 blob 索引篩選：

- 標記索引鍵應該以雙引號括住 ( ") 
- 標記值和容器名稱應以單引號括住 ( ' ) 
- @ 字元只可用於篩選特定的容器名稱 (例如， `@container = 'ContainerName'`) 
- 篩選準則會套用到字串上的詞典編纂排序
- 相同索引鍵上相同的側邊範圍作業無效 (例如， `"Rank" > '10' AND "Rank" >= '15'`) 
- 使用 REST 建立篩選運算式時，字元應該是以 URI 編碼

下表顯示適用于的所有有效運算子 `Find Blobs by Tags` ：

|  運算子  |  描述  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | `"Status" = 'In Progress'` |
|     >      |  大於 | `"Date" > '2018-06-18'` |
|     >=     |  大於或等於 | `"Priority" >= '5'` |
|     <      |  小於   | `"Age" < '32'` |
|     <=     |  小於或等於  | `"Company" <= 'Contoso'` |
|    AND     |  邏輯 and  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 將範圍設為特定容器 | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> 在標記上設定和查詢時，請熟悉字典順序。
>
> - 數位會在字母之前排序。 數位會根據第一個數位來排序。
> - 大寫字母排序在小寫字母之前。
> - 符號不是標準的。 某些符號會在數值之前排序。 其他符號會在字母之前或之後排序。

## <a name="conditional-blob-operations-with-blob-index-tags"></a>具有 blob 索引標記的條件式 blob 作業

在 REST 版本2019-10-10 和更新版本中，大部分的 [blob 服務 api](/rest/api/storageservices/operations-on-blobs) 現在都支援條件式標頭， `x-ms-if-tags` 如此一來，只有在符合指定的 blob 索引條件時，作業才會成功。 如果不符合條件，您將會得到 `error 412: The condition specified using HTTP conditional header(s) is not met` 。

`x-ms-if-tags`標頭可能會與其他現有的 HTTP 條件式標頭結合 (if-match （如果-None-match）等等) 。 如果要求中提供多個條件式標頭，它們必須評估為 true，作業才會成功。 所有條件式標頭都會有效地與邏輯 AND 合併。

下表顯示條件式作業的有效運算子：

|  運算子  |  描述  | 範例 |
|------------|---------------|---------|
|     =      |     等於     | `"Status" = 'In Progress'` |
|     <>     |   不等於   | `"Status" <> 'Done'` |
|     >      |  大於 | `"Date" > '2018-06-18'` |
|     >=     |  大於或等於 | `"Priority" >= '5'` |
|     <      |  小於   | `"Age" < '32'` |
|     <=     |  小於或等於  | `"Company" <= 'Contoso'` |
|    AND     |  邏輯 and  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | 邏輯 or   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Blob 作業的條件式標頭中允許有兩個額外的運算子，不等於和邏輯 or， `x-ms-if-tags` 但不存在於作業中 `Find Blobs by Tags` 。

## <a name="platform-integrations-with-blob-index-tags"></a>使用 blob 索引標記的平臺整合

Blob 索引標記不僅可協助您分類、管理及搜尋您的 blob 資料，還可提供與其他 Blob 儲存體功能的整合，例如 [生命週期管理](storage-lifecycle-management-concepts.md)。

### <a name="lifecycle-management"></a>生命週期管理

`blobIndexMatch`您可以使用做為生命週期管理中的規則篩選器，將資料移至較低層級，或根據套用至 blob 的索引標記來刪除資料。 如果 blob 符合指定的標記準則，您可以更細微地在規則中移動或刪除它們。

您可以將 blob 索引比對設定為生命週期規則中所設定的獨立篩選器，以對已標記的資料套用動作。 或者，您可以結合前置詞和 blob 索引，以符合更特定的資料集。 在生命週期規則中指定多個篩選準則會套用邏輯 AND 運算。 只有當 *所有* 篩選準則相符時，才會套用動作。

下列範例生命週期管理規則會套用至名為 *videofiles*之容器中的區塊 blob。 只有當資料符合的 blob 索引標記準則時，才會將規則分層 blob 保存到封存儲存體 `"Status" == 'Processed' AND "Source" == 'RAW'` 。

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

您可以使用下列其中一種方法來授與 blob 索引標記的存取權：

- 使用 Azure 角色型存取控制 (Azure RBAC) 將許可權授與 Azure Active Directory (Azure AD) 安全性主體。 使用 Azure AD 來獲得更高的安全性與易用性。 如需搭配使用 Azure AD 與 blob 作業的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](../common/storage-auth-aad.md)。
- 使用共用存取簽章 (SAS) 來委派 blob 索引的存取權。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。
- 使用帳戶存取金鑰來授權具有共用金鑰的作業。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引標記是 blob 資料的 subresource。 具有許可權的使用者或可讀取或寫入 blob 的 SAS 權杖，可能無法存取 blob 索引標記。

### <a name="role-based-access-control"></a>角色型存取控制

使用 Azure AD 身分 [識別](../common/storage-auth-aad.md) 的呼叫端可能會被授與下列許可權，以在 blob 索引標記上操作。

| Blob 索引標記作業                                          | Azure RBAC 動作                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [設定 Blob 標記](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/write    |
| [取得 Blob 標記](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/read     |
| [依標記尋找 Blob](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. 儲存體/storageAccounts/blobServices/容器/blob/篩選/動作 |

索引標記作業需要額外的許可權，與基礎 blob 資料分開。 [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者角色會被授與所有三個 Blob 索引標記作業的許可權。 [儲存體 Blob 資料讀取器](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)只會獲得 `Find Blobs by Tags` 和作業的許可權 `Get Blob Tags` 。

### <a name="sas-permissions"></a>SAS 許可權

使用 [共用存取簽章 (SAS) ](../common/storage-sas-overview.md) 的呼叫端可能會被授與範圍許可權，以在 blob 索引標籤上運作。

#### <a name="blob-sas"></a>Blob SAS

您可以在 blob SAS 中授與下列許可權，以允許存取 blob 索引標記。 Blob 的讀取和寫入權限不足以允許讀取或寫入其索引標記。

| 權限 | URI 符號 | 允許的作業                |
|------------|------------|-----------------------------------|
| 索引標記 |     t      | 取得和設定 blob 的索引標記 |

#### <a name="container-sas"></a>容器 SAS

您可以在容器 SAS 中授與下列許可權，以允許對 blob 標記進行篩選。 `Blob List`許可權不足以允許依索引標記篩選 blob。

| 權限 | URI 符號 | 允許的作業         |
|------------|------------|----------------------------|
| 索引標記 |     f      | 尋找具有索引標記的 blob |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>在中繼資料和 blob 索引標記之間進行選擇

Blob 索引標記和中繼資料都提供將任意使用者定義的索引鍵/值屬性與 blob 資源一起儲存的功能。 兩者都可以直接抓取和設定，而不會傳回或改變 blob 的內容。 您可以同時使用中繼資料和索引標記。

只有索引標籤會自動編制索引，並由原生 Blob 儲存體服務進行搜尋。 中繼資料無法以原生方式編制索引或搜尋。 您必須使用不同的服務，例如 [Azure 搜尋](../../search/search-blob-ai-integration.md)服務。 Blob 索引標記具有讀取、篩選和寫入的額外許可權，與基礎 blob 資料不同。 中繼資料會使用與 blob 相同的許可權，並由 [取得 blob](/rest/api/storageservices/get-blob) 和 [取得 blob 屬性](/rest/api/storageservices/get-blob-properties) 作業以 HTTP 標頭傳回。 Blob 索引標記會使用 [Microsoft 管理的金鑰](../common/storage-service-encryption.md)進行待用加密。 中繼資料會使用針對 blob 資料指定的相同加密金鑰進行待用加密。

下表摘要說明中繼資料與 blob 索引標記之間的差異：

|              |   中繼資料   |   Blob 索引標記  |
|--------------|--------------|--------------------|
| **限制**      | 沒有數值限制、8 KB 總計、不區分大小寫 | 每個 blob 最多10個標籤，每個標記768個位元組，區分大小寫 |
| **更新**    | 封存層不允許， `Set Blob Metadata` 取代所有現有的中繼資料， `Set Blob Metadata` 變更 blob 的上次修改時間 | 允許所有存取層， `Set Blob Tags` 取代所有現有的標記，而 `Set Blob Tags` 不會變更 blob 的上次修改時間 |
| **Storage**     | 與 blob 資料儲存 | Blob 資料的 Subresource |
| **& 查詢編制索引** | 必須使用不同的服務，例如 Azure 搜尋服務 | Blob 儲存體內建的索引和查詢功能 |
| **加密** | 使用相同的加密金鑰（用於 blob 資料）進行靜態加密 | 使用 Microsoft 管理的加密金鑰進行靜態加密 |
| **定價** | 中繼資料的大小包含在 blob 的儲存成本中 | 每個索引標籤的固定成本 |
| **標頭回應** | 在和中以標頭傳回的中繼資料 `Get Blob``Get Blob Properties` | 或所傳回的標記計數 `Get Blob` `Get Blob Properties` ，只能由和傳回的標記 `Get Blob Tags``List Blobs` |
| **權限**  | Blob 資料的讀取或寫入權限延伸至中繼資料 | 需要其他許可權才能讀取、篩選或寫入索引標記 |
| **命名** | 中繼資料名稱必須遵守 c # 識別碼的命名規則 | Blob 索引標記支援更廣泛的英數位元字元 |

## <a name="pricing"></a>定價

Blob 索引定價處於公開預覽狀態，可能會變更為正式運作。 您需支付儲存體帳戶內每月平均索引標籤數目的費用。 編制索引引擎不會產生任何費用。 對 `Set Blob Tags` 、和的要求 `Get Blob Tags` `Find Blobs by Tags` 會根據其各自的作業類型來收費。 [若要深入瞭解，請參閱封鎖 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>區域可用性和儲存體帳戶支援

Blob 索引標籤僅適用于一般用途 v2 (GPv2) 使用階層命名空間的帳戶 (HNS) 停用。 一般用途 (GPV1) 帳戶不受支援，但您可以將任何 GPv1 帳戶升級到 GPv2 帳戶。

Premium 儲存體帳戶不支援索引標記。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

在公開預覽中，blob 索引標記僅適用于下欄區域：

- 加拿大中部
- 加拿大東部
- 法國中部
- 法國南部

若要開始使用，請參閱 [使用 blob 索引標記來管理和尋找資料](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 您必須註冊您的訂用帳戶，才能在儲存體帳戶上使用 blob 索引預覽。 請參閱本文的「 [條件」和「已知問題](#conditions-and-known-issues) 」一節。

### <a name="register-your-subscription-preview"></a> (預覽版註冊您的訂用帳戶) 

因為 blob 索引標記只處於公開預覽狀態，所以您必須註冊您的訂用帳戶，才能使用此功能。 若要提交要求，請執行下列 PowerShell 或 CLI 命令。

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

## <a name="conditions-and-known-issues"></a>條件和已知問題

本節說明 blob 索引標記公開預覽中的已知問題和條件。 這項功能不應該用於生產工作負載，直到它達到正式運作 (GA) ，因為行為可能會變更。

- 針對預覽版本，您必須先註冊您的訂用帳戶，才能在預覽區域中使用儲存體帳戶的 blob 索引。
- 預覽版僅支援 GPv2 帳戶。 不支援已啟用 Blob、BlockBlobStorage 和 HNS 的 DataLake Gen2 帳戶。 不支援 GPv1 帳戶。
- 使用索引標籤上傳分頁 blob 不會保存標記。 在上傳分頁 blob 之後設定標記。
- 當篩選的範圍設定為單一容器時， `@container` 只有在篩選條件運算式中的所有索引標籤都是 (索引鍵 = 值) 的相等檢查時，才能傳遞。
- 在條件中使用範圍運算子時 `AND` ，您只能指定相同的索引標記索引鍵名稱 (`"Age" > '013' AND "Age" < '100'`) 。
- 不支援版本控制和 blob 索引。 Blob 索引標記會保留給版本，但不會傳遞給 blob 索引引擎。
- 不支援帳戶容錯移轉。 在容錯移轉之後，blob 索引可能無法正確更新。
- 生命週期管理僅支援具有 blob 索引相符的相等檢查。
- `Copy Blob` 不會將 blob 索引標記從來源 blob 複製到新的目的地 blob。 您可以在複製作業期間，指定要套用至目的地 blob 的標記。
- `Copy Blob` 從目的地 blob 上套用的標籤之另一個儲存體帳戶 (Async copy) ，會導致 blob 索引引擎不會傳回篩選集中的 blob 及其標記。 `Copy Blob`從 URL 使用 (同步複製) 。
- 標記會在快照集建立時保存。 但是，不支援升級快照集，而且可能會導致空的標記集。

## <a name="faq"></a>常見問題集

**Blob 索引是否可協助我篩選和查詢 blob 內的內容？**

否，如果您需要在 blob 資料內搜尋，請使用查詢加速或 Azure 搜尋服務。

**索引標記值是否有任何需求？**

Blob 索引標記只支援字串資料類型，而且查詢會以字典順序來傳回結果。 如果是數位，請將數位填補零。 若為日期和時間，請儲存為符合 ISO 8601 規範的格式。

**Blob 索引標記和 Azure Resource Manager 標記是否相關？**

否，Resource Manager 標記可協助組織控制平面資源，例如訂用帳戶、資源群組和儲存體帳戶。 索引標籤可在資料平面上提供 blob 管理和探索。

## <a name="next-steps"></a>後續步驟

如需如何使用 blob 索引的範例，請參閱 [使用 blob 索引來管理和尋找資料](storage-blob-index-how-to.md)。

深入瞭解 [生命週期管理](storage-lifecycle-management-concepts.md) ，並設定 blob 索引比對的規則。
