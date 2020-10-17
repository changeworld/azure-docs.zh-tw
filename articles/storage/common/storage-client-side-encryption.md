---
title: Microsoft Azure 儲存體的用戶端 .NET 加密 | Microsoft Docs
description: Azure Storage Client Library for .NET 支援用戶端加密以及與 Azure 金鑰保存庫的整合，為您的 Azure 儲存體應用程式提供最大的安全性。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 4e8623ecb351fa99a437de70a9b74a70fb6228cd
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151139"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault 金鑰保存庫
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>概觀
[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage)支援在上傳至 Azure 儲存體之前將用戶端應用程式中的資料加密，並在下載至用戶端時解密資料。 程式庫也支援與儲存體帳戶金鑰管理的 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 整合。

如需引導您進行使用用戶端加密和 Azure 金鑰保存庫來加密 Blob 的逐步教學課程，請參閱 [在 Microsoft Azure 儲存體中使用 Azure 金鑰保存庫加密和解密 Blob](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)。

如需使用 Java 加密用戶端，請參閱 [Microsoft Azure 儲存體的用戶端 Java 加密](storage-client-side-encryption-java.md)。

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>透過信封技術進行加密和解密
加密和解密的程序採用信封技術。

### <a name="encryption-via-the-envelope-technique"></a>透過信封技術加密
透過信封技術加密的運作方式如下：

1. Azure 儲存體用戶端程式庫會產生內容加密金鑰 (CEK)，這是使用一次的對稱金鑰。
2. 使用此 CEK 加密使用者資料。
3. 然後使用金鑰加密金鑰 (KEK) 包裝 (加密) CEK。 KEK 由金鑰識別碼所識別，可以是非對稱金鑰組或對稱金鑰，且可以在本機管理或儲存在 Azure 金鑰保存庫中。
   
    儲存體用戶端程式庫本身永遠沒有 KEK 的存取權。 程式庫會叫用金鑰保存庫所提供的金鑰包裝演算法。 如有需要，使用者可以選擇使用自訂提供者來包裝/取消包裝金鑰。

4. 然後，將加密的資料上傳至 Azure 儲存體服務。 包裝的金鑰及一些其他加密中繼資料會儲存為中繼資料 (在 blob 上)，或插補加密的資料 (訊息佇列和資料表實體)。

### <a name="decryption-via-the-envelope-technique"></a>透過信封技術解密
透過信封技術解密的運作方式如下：

1. 用戶端程式庫假設使用者在本機或 Azure 金鑰保存庫中管理金鑰加密金鑰 (KEK)。 使用者不必知道用於加密的特定金鑰。 相反地，可以設定並使用金鑰解析程式，將不同的金鑰識別碼解析成金鑰。
2. 用戶端程式庫會下載加密的資料，以及儲存在服務上的任何加密資料。
3. 然後，使用金鑰加密金鑰 (KEK) 將已包裝的內容加密金鑰 (CEK) 解除包裝 (解密)。 同樣地，用戶端程式庫在此沒有 KEK 的存取權。 它只會叫用自訂或 Key Vault 提供者的解除包裝演算法。
4. 然後，使用內容加密金鑰 (CEK) 解密已加密的使用者資料。

## <a name="encryption-mechanism"></a>加密機制
儲存體用戶端程式庫會使用 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 來加密使用者資料。 具體來說，就是 [加密區塊鏈結 (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 模式搭配 AES。 每個服務的運作方式稍有不同，我們將在這裡討論每個服務。

### <a name="blobs"></a>Blob
用戶端程式庫目前僅支援整個 Blob 的加密。 針對下載，則皆支援完整與範圍下載。

在加密期間，用戶端程式庫會產生 16 位元組的隨機初始化向量 (IV)，以及 32 位元組的隨機內容加密金鑰 (CEK)，並使用這項資訊執行 blob 資料的信封加密。 然後，已包裝的 CEK 和一些其他加密中繼資料會儲存為 blob 中繼資料，並連同加密的 blob 一起儲存在服務上。

> [!WARNING]
> 如果您要為 blob 編輯或上傳您自己的中繼資料，則必須確定保留此中繼資料。 如果您上傳新的中繼資料，但缺少此中繼資料，包裝的 CEK、IV 和其他中繼資料將會遺失，而且永遠無法再擷取 blob 內容。
> 
> 

下載整個 blob 時，包裝的 CEK 會在此情況下解除包裝，並與儲存為 blob 中繼資料的 IV (一起使用，) 將解密的資料傳回給使用者。

在加密的 blob 中下載任意範圍，需要調整使用者所提供的範圍，以便取得可用於成功解密所要求範圍的少量額外資料。

所有 Blob 類型 (區塊 Blob、頁面 Blob 和附加 Blob) 都可以使用此機制進行加密/解密。

### <a name="queues"></a>佇列
因為佇列訊息可以是任何格式，用戶端程式庫會定義自訂的格式，其中包含訊息文字中的初始化向量 (IV) 和加密的內容加密金鑰 (CEK)。

在加密期間，用戶端程式庫會產生 16 位元組的隨機 IV，以及 32 位元組的隨機 CEK，並使用這項資訊執行佇列訊息文字的信封加密。 然後，已包裝的 CEK 和一些其他加密中繼資料會加入至已加密的佇列訊息。 這個修改過的訊息 (如下所示) 會儲存在服務上。

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

在解密期間，從佇列訊息中擷取已包裝的金鑰，並解除包裝。 IV 也會從佇列訊息中擷取，並與未包裝的金鑰一起用來解密佇列訊息資料。 請注意，加密中繼資料很小 (小於 500 位元組)，雖然計入佇列訊息的 64KB 限制內，但影響仍在可掌控的範圍內。 請注意，加密的訊息將會以 base64 編碼，如上述程式碼片段所示，這也會擴充傳送的訊息大小。

### <a name="tables"></a>資料表
> [!NOTE]
> 只有2.x 版的 Azure 儲存體用戶端程式庫支援表格服務。
> 
> 

用戶端程式庫支援在插入和取代作業時進行實體屬性的加密。

> [!NOTE]
> 目前不支援合併。 因為屬性子集先前可能是使用不同的金鑰加密，直接合併新的屬性並更新中繼資料會導致資料遺失。 合併可能需要額外的服務呼叫來從服務讀取預先存在的實體，或在每個屬性上都使用新的金鑰，兩者都不利用於效能。
> 
> 

資料表資料加密的運作方式如下：  

1. 使用者指定要加密的屬性。
2. 用戶端程式庫會針對每個實體產生 16 位元組的隨機初始化向量 (IV) 以及 32 位元組的隨機內容加密金鑰 (CEK)，並在個別屬性上執行信封加密，使每個屬性衍生新的 IV，藉此進行加密。 加密的屬性會儲存為二進位資料。
3. 然後，已包裝的 CEK 和一些其他加密中繼資料會儲存成額外兩個保留的屬性。 第一個保留的屬性 (_ClientEncryptionMetadata1) 是字串屬性，保存 IV、版本和已包裝的金鑰的相關資訊。 第二個保留的屬性 (_ClientEncryptionMetadata2) 二進位屬性，其保留已加密之屬性的相關資訊。 此第二個屬性 (_ClientEncryptionMetadata2) 中的資訊已自行加密。
4. 由於加密需要這些額外保留的屬性，使用者現在可能只有 250 個自訂屬性，而不是 252 個。 實體的總大小必須小於 1 MB。

請注意，只有字串屬性可以加密。 如果有其他類型的屬性需要加密，則必須轉換成字串。 加密的字串儲存在服務上作為二進位屬性，且解密後會轉換回字串。

針對資料表，除了加密原則之外，使用者必須指定要加密的屬性。 作法是指定 [EncryptProperty] 屬性 (針對衍生自 TableEntity 的 POCO 實體)，或在要求選項中指定加密解析程式。 加密解析程式是委派，接受資料分割索引鍵、資料列索引鍵和屬性名稱，然後傳回布林值，指出是否應該加密該屬性。 在加密期間，用戶端程式庫會使用此資訊，決定將屬性在寫到網路時是否應該加密。 委派也提供關於屬性如何加密的可能邏輯。  (例如，如果是 X，則加密屬性 A;否則，請將屬性 A 和 B 加密。 ) 請注意，在讀取或查詢實體時不需要提供這項資訊。

### <a name="batch-operations"></a>批次作業
在批次作業中，批次作業中的所有資料列上會使用相同的 KEK，因為用戶端程式庫只允許每個批次作業有一個選項物件 (也就是一個原則/KEK)。 不過，用戶端程式庫會在內部為批次中的每個資料列產生新的隨機 IV 和隨機 CEK。 使用者也可以選擇為批次中的每個作業加密不同的屬性，作法是在加密解析程式中定義此行為。

### <a name="queries"></a>查詢
> [!NOTE]
> 因為實體已加密，所以無法執行依加密屬性篩選的查詢。  如果您嘗試這麼做，結果會不正確，因為服務會嘗試比較加密資料與未加密資料。
> 
> 
> 若要執行查詢作業，您必須指定一個能夠解析結果集中的所有金鑰的金鑰解析程式。 如果查詢結果中包含的實體無法解析成提供者，用戶端程式庫會擲回錯誤。 針對執行伺服器端投影的任何查詢，用戶端程式庫會依預設將特殊加密中繼資料屬性 (_ClientEncryptionMetadata1 和 _ClientEncryptionMetadata2) 加入選取的資料行。

## <a name="azure-key-vault"></a>Azure 金鑰保存庫
Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用 Azure 金鑰保存庫時，使用者可以使用受硬體安全模組 (HSM) 保護的金鑰來加密金鑰和密碼 (例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰、.PFX 檔案和密碼)。 如需詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)。

儲存體用戶端程式庫會使用核心程式庫中的 Key Vault 介面，以提供跨 Azure 的通用架構來管理金鑰。 使用者可以利用 Key Vault 程式庫來取得其所提供的所有其他權益，例如簡單、無縫對稱/RSA 本機和雲端金鑰提供者的實用功能，以及匯總和快取的協助。

### <a name="interface-and-dependencies"></a>介面和相依性

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Key Vault 整合有兩個必要的套件：

* Azure Core 包含 `IKeyEncryptionKey` 和 `IKeyEncryptionKeyResolver` 介面。 適用于 .NET 的儲存體用戶端程式庫已將其定義為相依性。
* KeyVault (v4. x) 包含 Key Vault REST 用戶端，以及搭配用戶端加密使用的密碼編譯用戶端。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

有三個金鑰保存庫封裝：

* Microsoft.Azure.KeyVault.Core 包含 IKey 和 IKeyResolver。 這是不具有相依性的小型封裝。 .NET 的儲存體用戶端程式庫會將其定義為相依。
* KeyVault (v3. x) 包含 Key Vault REST 用戶端。
*  (KeyVault) 包含延伸模組程式碼，其中包含密碼編譯演算法的執行，以及 RSAKey 和 SymmetricKey。 它依賴 Core 和 KeyVault 命名空間，並提供功能來定義彙總解析程式 (當使用者想要使用多個金鑰提供者時) 和快取金鑰解析程式。 儲存體用戶端程式庫不直接依賴這個封裝，如果使用者想要使用 Azure 金鑰保存庫來儲存其金鑰，或使用金鑰保存庫延伸模組來取用本機和雲端密碼編譯提供者，則需要此封裝。

如需有關 v11 Key Vault 使用方式的詳細資訊，請參閱 [v11 加密程式碼範例](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)。

---

金鑰保存庫是專為高價值的主要金鑰而設計，個別金鑰保存庫的節流限制都以此為設計重點。 使用金鑰保存庫執行用戶端加密時，慣用的模型是使用在金鑰保存庫中儲存為密碼且在本機快取的對稱主要金鑰。 使用者必須執行下列動作：

1. 離線建立密碼，再上載至金鑰保存庫。
2. 使用密碼的基底識別碼做為參數，解析用於加密的目前密碼版本，並在本機快取這項資訊。 使用 CachingKeyResolver 進行快取。使用者不需要實作自己的快取邏輯。
3. 建立加密原則時，使用快取解析程式做為輸入。

## <a name="best-practices"></a>最佳作法
只有 .NET 的儲存體用戶端程式庫才支援加密。 Windows Phone 和 Windows 執行階段目前不支援加密。

> [!IMPORTANT]
> 使用用戶端加密時，請留意以下重點：
> 
> * 讀取或寫入加密的 Blob 時，使用整個 Blob 上傳命令及範圍/整個 Blob 下載命令。 避免寫入使用通訊協定作業的加密 Blob，例如：放置區塊、放置區塊清單、寫入頁面、清除頁面或附加區塊；否則您可能會破壞加密 Blob 並使它無法讀取。
> * 對於資料表，存在類似的條件約束。 請小心在未更新加密中繼資料時即更新加密的內容。
> * 如果您在加密 Blob 上設定中繼資料，您可能會覆寫加密所需的加密相關中繼資料，因為設定中繼資料不是加總解密。 快照集也是如此。在為加密的 Blob 建立快照集時，請避免指定中繼資料。 如果必須設定中繼資料，請務必先呼叫 **FetchAttributes** 方法，以取得目前的加密中繼資料，並避免在設定中繼資料時並行寫入。
> * 在預設的要求選項中啟用 **RequireEncryption** 屬性，使用者就應該只能使用加密的資料。 如需詳細資訊請參閱下方內容。
>
>

## <a name="client-api--interface"></a>用戶端 API / 介面
使用者只能提供一個金鑰、一個解析程式或兩者。 金鑰是使用金鑰識別碼來識別，並提供包裝/解除包裝的邏輯。 解析程式是用來在解密過程中解析金鑰。 它會定義一個解析方法，以傳回指定金鑰識別碼的金鑰。 這可讓使用者從多個位置中管理的多個金鑰之中選擇。

* 加密時一律使用金鑰，缺少金鑰將會導致錯誤。
* 解密：
  * 如果指定索引鍵，且其識別碼符合所需的金鑰識別碼，則會使用該金鑰來解密。 否則，就會嘗試解析程式。 如果沒有此嘗試的解析程式，則會擲回錯誤。
  * 叫用金鑰解析程式 (如果指定) 以取得金鑰。 如果已指定解析程式，但沒有金鑰識別碼的對應，則會擲回錯誤。

### <a name="requireencryption-mode-v11-only"></a>>requireencryption 模式 (v11) 
使用者可以針對所有上傳和下載都必須加密的作業模式，從中選擇啟用。 在此模式中，在用戶端上嘗試上傳沒有加密原則的資料或下載未在服務上加密的資料將會失敗。 要求選項物件的 **RequireEncryption** 屬性會控制此行為。 如果您的應用程式會將所有儲存在 Azure 儲存體中的物件加密，則您可以在服務用戶端服務的預設要求選項上，設定 **RequireEncryption** 屬性。 例如，將 **CloudBlobClient.DefaultRequestOptions.RequireEncryption** 設為 **true**，要求加密透過該用戶端物件所執行的所有 Blob 作業。


### <a name="blob-service-encryption"></a>Blob 服務加密


# <a name="net-v12"></a>[.NET v12](#tab/dotnet)
建立 **ClientSideEncryptionOptions** 物件，並在使用 **SpecializedBlobClientOptions**建立用戶端時進行設定。 您無法以每個 API 為基礎設定加密選項。 其他一切由用戶端程式庫在內部處理。

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("myContainer").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

**BlobServiceClient**不需要套用加密選項。 它們也可以傳遞至**BlobContainerClient** / 接受**BlobClientOptions**物件的 BlobContainerClient **>blobclient**函式。

如果所需的 **>blobclient** 物件已經存在，但沒有用戶端加密選項，就會有擴充方法，以指定的 **ClientSideEncryptionOptions**建立該物件的複本。 這個擴充方法可避免從頭開始建立新的 **>blobclient** 物件時所產生的額外負荷。

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)
建立 **BlobEncryptionPolicy** 物件，並在要求選項中加以設定 (透過 API 或在用戶端層級使用 **DefaultRequestOptions**)。 其他一切由用戶端程式庫在內部處理。

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>佇列服務加密
# <a name="net-v12"></a>[.NET v12](#tab/dotnet)
建立 **ClientSideEncryptionOptions** 物件，並在使用 **SpecializedQueueClientOptions**建立用戶端時進行設定。 您無法以每個 API 為基礎設定加密選項。 其他一切由用戶端程式庫在內部處理。

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

**QueueServiceClient**不需要套用加密選項。 它們也可以傳遞至接受**QueueClientOptions**物件的**QueueClient**函式。

如果所需的 **QueueClient** 物件已經存在，但沒有用戶端加密選項，就會有擴充方法，以指定的 **ClientSideEncryptionOptions**建立該物件的複本。 這個擴充方法可避免從頭開始建立新的 **QueueClient** 物件時所產生的額外負荷。

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

有些使用者可能會有佇列，其中並非所有收到的訊息都可以成功解密，而金鑰或解析程式必須擲回。 在此情況下，上述範例的最後一行將會擲回，而且將無法存取任何接收的訊息。 在這些案例中，可以使用子類別 **QueueClientSideEncryptionOptions** 來提供加密選項給用戶端。 它會公開事件 **DecryptionFailed** ，以便在佇列訊息解密時觸發，只要至少有一個調用加入至事件即可。 您可以用這種方式處理個別失敗的訊息，並將它們從 **>receivemessages**傳回的最終**QueueMessage []** 中篩選掉。

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)
建立 **QueueEncryptionPolicy** 物件，並在要求選項中加以設定 (透過 API 或在用戶端層級使用 **DefaultRequestOptions**)。 其他一切由用戶端程式庫在內部處理。

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>表格服務加密 (v11) 
除了建立加密原則並在要求選項上加以設定之外，您必須在 **TableRequestOptions** 中指定 **EncryptionResolver**，或在實體上設定 [EncryptProperty] 屬性。

#### <a name="using-the-resolver"></a>使用解析程式

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>使用屬性
如上所述，如果實體實作 TableEntity，則屬性可以使用 [EncryptProperty] 屬性裝飾，不需指定 **EncryptionResolver**。

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>加密和效能
請注意，加密您的儲存體資料會造成額外的效能負擔。 必須產生內容金鑰和 IV，內容本身必須經過加密，而且其他中繼資料必須格式化並上傳。 這個額外負荷會因所加密的資料數量而有所不同。 我們建議客戶一定要在開發期間測試其應用程式的效能。

## <a name="next-steps"></a>後續步驟
* [教學課程：在 Microsoft Azure 儲存體中使用 Azure 金鑰保存庫加密和解密 Blob](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* 下載 [適用於 .NET NuGet 封裝的 Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage)
* 下載 Azure 金鑰保存庫 NuGet 的 [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/)、[Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/) 和 [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) 封裝  
* 請瀏覽 [Azure 金鑰保存庫文件](../../key-vault/general/overview.md)
