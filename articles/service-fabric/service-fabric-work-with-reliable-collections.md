---
title: 使用可靠的集合
description: 瞭解在 Azure Service Fabric 應用程式中使用可靠集合的最佳做法。
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7df48bc0dfbef6fc85335801e64484914a218eb7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255790"
---
# <a name="working-with-reliable-collections"></a>使用可靠的集合
Service Fabric 透過可靠的集合向 .NET 開發人員提供具狀態的程式設計模型。 具體來說，Service Fabric 提供了可靠的字典和可靠的佇列類別。 當您使用這些類別時，您的狀態是分割的 (延展性)、複寫的 (可用性)，且在分割區內交易 (ACID 語意)。 讓我們看看可靠字典物件的一般用法，並查看其實際執行的作業。

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

可靠字典物件上的所有作業 (除了無法復原的 ClearAsync) 都需要一個 ITransaction 物件。 此物件與您嘗試對單一分割區內任何可靠字典和/或可靠佇列物件進行的任何變更和所有變更相關聯。 您可以藉由呼叫分割區的 StateManager's CreateTransaction 方法來取得 ITransaction 物件。

在上述程式碼中，ITransaction 物件會傳遞至可靠字典的 AddAsync 方法。 就內部而言，接受索引鍵的字典方法會採用與該索引鍵相關聯的讀取器/寫入器鎖定。 如果方法修改索引鍵的值，方法會在索引鍵上使用寫入鎖定，如果方法只會從索引鍵的值讀取，則會對索引鍵進行讀取鎖定。 由於 AddAsync 會將索引鍵的值修改為新的傳入值，因此會採用金鑰的寫入鎖定。 因此，如果有 2 個 (或多個) 執行緒同時嘗試新增具相同索引鍵的值，則其中一個執行緒將會取得寫入鎖定，而另一個執行緒將會封鎖。 方法預設最多封鎖 4 秒以取得鎖定，4 秒後方法就會擲回 TimeoutException。 如果您想要的話，方法多載可讓您傳遞明確的超時值。

通常，您撰寫程式碼回應 TimeoutException 的方式是攔截它，然後重試整個作業 (如上面的程式碼所示)。 在這個簡單的程式碼中，我們只是呼叫工作，每次延遲傳遞100毫秒。 但在實際狀況裡，最好還是改用某種指數型的撤退延遲。

一旦取得鎖定，AddAsync 就會在與 ITransaction 物件相關聯的內部暫存字典中加入索引鍵和值物件參考。 這就完成了讀取自己撰寫的語意。 也就是說，在您呼叫 AddAsync 之後，使用相同 ITransaction 物件的後續 TryGetValueAsync 呼叫將會傳回值，即使您尚未認可交易也一樣。

> [!NOTE]
> 使用新的交易呼叫 TryGetValueAsync 將會傳回上一個認可值的參考。 請勿直接修改該參考，因為這會略過保存和複寫變更的機制。 建議您將值設為唯讀，讓變更金鑰值的唯一方法是透過可靠的字典 Api。

接下來，AddAsync 會將索引鍵和值物件序列化為位元組陣列，並將這些位元組陣列附加至本機節點的記錄檔。 最後，AddAsync 會將位元組陣列傳送給所有次要複本，讓它們有相同的索引鍵/值資訊。 即使索引鍵/值資訊已寫入記錄檔，在相關交易獲認可之前，資訊都不會被視為字典的一部分。

在上述程式碼中，CommitAsync 的呼叫會認可所有交易的作業。 具體來說，它會將認可資訊附加到本機節點的記錄檔，也會將認可記錄傳送給所有的次要複本。 一旦回覆了複本的仲裁 (多數)，則所有資料變更就會視為永久性，並且會釋出透過 ITransaction 物件所操作的任何索引鍵相關聯鎖定，讓其他執行緒/交易可以操作相同的索引鍵及其值。

如未呼叫 CommitAsync (通常是因為擲回例外狀況)，則會處置 ITransaction 物件。 處置未認可的 ITransaction 物件時，Service Fabric 會將中止資訊附加到本機節點的記錄檔中，而不需要將任何內容傳送到任何次要複本。 然後會釋出透過交易操作的任何與索引鍵相關聯的鎖定。

## <a name="volatile-reliable-collections"></a>Volatile 可靠的集合 
在某些工作負載（例如複寫的快取）中，偶爾會發生資料遺失的情形。 避免將資料保存到磁片中，可以在寫入可靠的字典時提供更好的延遲和輸送量。 缺少持續性的缺點是，如果發生仲裁遺失的情況，將會發生完整資料遺失的情況。 由於仲裁遺失是很罕見的情況，因此增加的效能可能值得在這些工作負載中發生資料遺失的罕見可能性。

目前，volatile 支援僅適用于可靠的字典和可靠的佇列，而不是 ReliableConcurrentQueues。 請參閱[警告](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)清單，告知您是否要使用變動性集合。

若要在服務中啟用變動性支援，請將 ```HasPersistedState``` 服務型別宣告中的旗標設定為 ```false``` ，如下所示：
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>現有的持續性服務無法變成 volatile，反之亦然。 如果您想要這麼做，就必須刪除現有的服務，然後使用更新的旗標來部署服務。 這表示如果您想要變更旗標，則必須願意產生完整的資料遺失 ```HasPersistedState``` 。 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>常見陷阱以及如何避免
既然您已經瞭解可靠的集合在內部的工作方式，讓我們來看看其中一些常見的誤用。 參閱下列程式碼：

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

使用一般的 .NET 字典時，您可以在字典中加入索引鍵/值，然後變更屬性的值 (例如 LastLogin)。 不過，這段程式碼和可靠的字典不會合作無間。 我們稍早討論過：呼叫 AddAsync 會將索引鍵/值物件序列化成位元組陣列，然後將陣列儲存到本機檔案，並將它們傳送到次要複本。 如果您稍後變更屬性，這只會變更記憶體中的屬性值。它不會影響本機檔案或傳送至複本的資料。 如果進程損毀，則會擲回記憶體中的哪些內容。 啟動新的處理序，或另一個複本變成主要複本時，使用的就是舊的屬性值。

我一再強調上面這種錯誤是很容易發生的。 而且您只有在處理序當機時才會發現錯誤。 撰寫程式碼的正確方式其實只要反轉兩行即可︰


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

這是另一個常見的錯誤︰

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

同樣地，使用一般的 .NET 字典時，上面的程式碼以常見的模式正常運作：開發人員使用索引鍵查詢值。 如果值存在，開發人員會變更屬性的值。 不過，使用可靠的集合，這段程式碼會出現前面討論過的同樣問題：**物件一旦給了可靠的集合，就「無法」修改。**

在可靠的集合中更新值的正確方式，是取得現有值的參考，並考慮這個參考所參考的物件為不可變。 接著，建立新的物件，此物件是與原始物件完全相同的複本。 現在，您可以修改這個新物件的狀態，將新物件寫入集合，讓它序列化為位元組陣列，附加至本機檔案並傳送到複本。 認可變更之後，記憶體中的物件、本機檔案及所有複本都會有完全一致的狀態。 大功告成！

下列程式碼會示範在可靠的集合中更新值的正確方式︰

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>定義不可變的資料類型，以防止程式設計人員犯錯。
在理想的情況下，我們希望編譯器在您意外產生程式碼時報告錯誤，以變動此您應該視為不可變的物件狀態。 但是 C# 編譯器做不到這一點。 所以，為避免潛在的程式設計人員錯誤，我們強烈建議您將可靠集合所使用的類型定義為不可變的類型。 具體來說，這表示您要堅持核心值類型 (例如數字 [Int32、UInt64 等等]、DateTime、Guid、TimeSpan 等等)。 您也可以使用 String。 最好避免使用集合屬性，因為將其序列化和還原序列化經常會降低效能。 不過，如果您想要使用集合屬性，我們強烈建議使用。NET 的不可變集合程式庫 ([system.object](https://www.nuget.org/packages/System.Collections.Immutable/)) 。 此程式庫可從下載 https://nuget.org 。我們也建議您盡可能密封類別，並將欄位設為唯讀。

以下的 UserInfo 類型會示範如何利用上述建議定義不可變的類型。

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId 類型也是不可變的類型，如下所示︰

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>結構描述版本控制 (升級)
就內部而言，可靠的集合會使用將您的物件序列化。NET 的 DataContractSerializer。 序列化的物件會保存到主要複本的本機磁片，而且也會傳送至次要複本。 隨著您的服務成熟，您可能會想要變更您的服務所需) 的資料類型 (架構。 請謹慎處理資料的版本設定。 首先也是最重要的，您必須永遠有能力還原序列化舊的資料。 具體來說，這表示您的還原序列化程式碼必須具有無限回溯相容性︰服務程式碼的版本 333 必須能夠操作 5 年前放在可靠的集合中，第 1 版的服務程式碼資料。

而且，服務程式碼一次只能升級一個網域。 所以，在升級期間，您會同時執行兩個不同版本的服務程式碼。 您必須避免新版本的服務程式碼使用新的結構描述，因為舊版的服務程式碼可能無法處理新的結構描述。 您應該盡可能將服務的每個版本都設計為正向相容一個版本。 具體來說，這表示服務程式碼的 V1 應該能夠略過它未明確處理的任何結構描述元素。 不過，它必須能夠儲存未明確得知的任何資料，並在更新字典索引鍵或值時將其寫回。

> [!WARNING]
> 雖然您可以修改索引鍵的架構，但您必須確保金鑰的雜湊碼和 equals 演算法都是穩定的。 如果您變更這些演算法其中一個的運作方式，您就再也無法在可靠的字典內查詢索引鍵。
> .NET 字串可用來做為索引鍵，但使用字串本身做為索引鍵--請勿使用 GetHashCode 的結果做為索引鍵。

或者，您也可以執行通稱為兩階段升級的功能。 透過兩階段升級，您可以將服務從 V1 升級至 V2： V2 包含知道如何處理新架構變更的程式碼，但這段程式碼不會執行。 當 V2 程式碼讀取 V1 資料時，它會在其上操作並寫入 V1 資料。 然後，在跨所有升級網域的升級都完成之後，您就可以通知執行中的 V2 執行個體，升級已完成。  (一種表示這種情況的方式，就是推出設定升級;這會使此作業成為兩階段升級。現在 ) ，V2 實例可以讀取 V1 資料，將它轉換成 V2 資料，對其進行操作，並將其寫出為 V2 資料。 當其他執行個體讀取 V2 資料時，不需要轉換它，只要操作並寫出 V2 資料即可。

## <a name="next-steps"></a>後續步驟
若要了解如何建立正向相容的資料合約，請參閱[正向相容的資料合約](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts) \(機器翻譯\)。

若要了解資料合約版本設定的最佳做法，請參閱[資料合約版本設定](/dotnet/framework/wcf/feature-details/data-contract-versioning) \(機器翻譯\)。

若要了解如何實作版本容錯的資料合約，請參閱[版本容錯序列化回呼](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks) \(機器翻譯\)。

若要了解如何提供可跨多個版本相互操作的資料結構，請參閱 [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject?view=netcore-3.1) \(機器翻譯\)。
