---
title: 在 Azure Cosmos DB 中模型化資料
titleSuffix: Azure Cosmos DB
description: 了解如何在 NoSQL 資料庫中將資料模型化，以及在關聯式資料庫和文件資料庫中將資料模型化的差異。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755023"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的資料建模

雖然無架構資料庫（如 Azure Cosmos DB）使存儲和查詢非結構化和半結構化資料變得超級容易，但您應該花一些時間考慮資料模型，以充分利用性能和可擴充性和最低成本。

要如何儲存資料？ 您的應用程式要如何擷取及查詢資料？ 您的應用程式是讀取重，還是寫重？

閱讀本文後，您將能夠回答下列問題：

* 什麼是資料模型化，以及為什麼應該關心？
* Azure Cosmos DB 中的建模資料與關係資料庫如何不同？
* 如何表達非關聯式資料庫中的資料關聯性？
* 何時內嵌資料，以及何時連結至資料？

## <a name="embedding-data"></a>內嵌資料

在 Azure Cosmos DB 中開始建模資料時，嘗試將實體視為表示為 JSON 文檔**的自包含項**。

為了進行比較，讓我們先看看如何在關係資料庫中對資料建模。 下列範例示範人員可能如何儲存在關聯式資料庫中。

![關聯式資料庫模型](./media/sql-api-modeling-data/relational-data-model.png)

使用關係資料庫時，策略是使所有資料正常化。 使資料正常化通常涉及獲取實體（如人員）並將其分解為離散元件。 在上面的示例中，人員可以有多個連絡人詳細資訊記錄以及多個地址記錄。 可以通過進一步提取常見欄位（如類型）來進一步細分連絡人詳細資訊。 同樣適用于位址，每個記錄可以是 *"主頁*"或"*業務*"類型。

將資料正規化的引導前提是在每個記錄資料上 **避免儲存多餘的資料** ，而是參考資料。 在此示例中，要讀取具有所有連絡人詳細資訊和位址的人員，您需要使用 JOINS 在運行時有效地重新撰寫（或取消正常化）資料。

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

更新單一人員與其連絡詳細資料和地址需要跨許多個別資料表的寫入作業。

現在，讓我們看一下如何在 Azure Cosmos DB 中對與自包含實體相同的資料建模。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

使用上述方法，我們將與此人有關的所有資訊（如其聯繫資訊和位址）**嵌入**到*單個 JSON*文檔中，從而使人員記錄**非正常化**。
此外，因為我們不受限於固定的結構描述，我們有彈性可進行一些動作，像是讓連絡詳細資料不同的圖形。

從資料庫檢索完整的人員記錄現在是針對單個容器和單個項的**單個讀取操作**。 更新人員記錄及其連絡人詳細資訊和位址也是針對單個項**的單個寫入操作**。

藉由反正規化資料，您的應用程式可能需要發出更少的查詢和更新以完成一般作業。

### <a name="when-to-embed"></a>內嵌的時機

一般而言，使用內嵌的資料模型的時機為：

* 實體之間存在**包含**關係。
* 實體之間有 **一對一些** 關聯性。
* 內嵌的資料 **不常變更**。
* 有嵌入式資料，**不會增長沒有綁定**。
* 有一些嵌入的資料**經常一起查詢**。

> [!NOTE]
> 通常反正規化的資料模型可提供較佳的 **讀取** 效能。

### <a name="when-not-to-embed"></a>不要內嵌的時機

雖然 Azure Cosmos DB 中的經驗法則是使所有內容非正常化並將所有資料嵌入到單個專案中，但這可能會導致某些應避免的情況。

取得此 JSON 程式碼片段。

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

如果我們要模型化一般的部落格或 CMS 系統，這可能是具有內嵌註解的文章實體的外觀。 此範例的問題在於註解陣列是 **unbounded**，表示任何單一文章可以具備的註解數目沒有 (實際) 的限制。 這可能成為一個問題，因為專案的大小可能會無限大。

隨著專案大小的增長，通過線傳輸資料以及大規模讀取和更新專案的能力將受到影響。

在這種情況下，最好考慮以下資料模型。

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

此模型包含嵌入在 post 容器中的三個最新注釋，這是一個包含一組固定屬性的陣列。 其他注釋分組到 100 個注釋的批次中，並存儲為單獨的專案。 因為我們虛構的應用程式可讓使用者一次載入 100 個註解，因此已將批次大小選擇為 100。  

嵌入資料的另一種情況是，嵌入資料經常跨專案使用，並且會頻繁更改。

取得此 JSON 程式碼片段。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

這可以代表個人的股票組合。 我們已選擇內嵌股票資料到每個組合文件。 在相關資料經常會變更的環境中，像是股票交易應用程式，內嵌經常變更的資料表示，每次交易股票時您便經常更新每個組合文件。

股票 *zaza* 可能在單一日交易數百次，而上千名使用者的組合上可能有 *zaza*。 如果使用如上所示的資料模型，我們每天都必須多次更新數千個組合文件，這會導致系統無法妥善延展。

## <a name="referencing-data"></a>參考資料

嵌入資料適用于許多情況，但在某些情況下，使資料非正常化會導致比其價值更多的問題。 那我們現在該怎麼辦？

關聯式資料庫不是您可以建立實體之間的關聯性的唯一位置。 在文檔資料庫中，可以在一個文檔中具有與其他文檔中的資料相關的資訊。 我們不建議構建更適合 Azure Cosmos DB 或任何其他文檔資料庫中的關係資料庫的系統，但簡單關係很好，很有用。

在以下的 JSON 中，我們選擇使用先前的股票組合範例，但這次我們參考組合上的股票項目而不是加以內嵌。 如此一來，經常全天變更的股票項目，需要更新的唯一文件是單一股票文件。

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

不過，這個方法目前的缺點是您的應用程式是否需要在顯示人員的組合時顯示持有的每個股票的相關資訊；在此情況下，您必須對資料庫中進行多個來回行程，才能載入每個股票文件的資訊。 在這裡，我們決定提升全天經常發生之寫入作業的效率，但反而對此特定的系統效能影響較小的讀取作業有害。

> [!NOTE]
> 正規化的資料模型 **可能需要更多來回行程** 到伺服器。

### <a name="what-about-foreign-keys"></a>外部索引鍵呢？

因為目前沒有條件約束、外部索引鍵之類的概念，您在文件中具有的任何文件間關聯性實際上是「弱式連結」，並且將不會由資料庫本身驗證。 如果您想要確定文件所參考的資料真的存在，您就需要在您的應用程式中這麼做，或在 Azure Cosmos DB 上透過使用伺服器端觸發程序或預存程序。

### <a name="when-to-reference"></a>參考時機

一般而言，使用正規化資料模型的時機為：

* 代表 **一對多** 關聯性。
* 代表 **多對多** 關聯性。
* 相關資料 **經常變更**。
* 參考資料可能是 **unbounded**。

> [!NOTE]
> 通常正規化可提供較佳的 **寫入** 效能。

### <a name="where-do-i-put-the-relationship"></a>放置關聯性的位置為何？

關聯性的成長將有助於判斷用來儲存參考的文件。

如果我們看看下面會建立發行者和書籍模型的 JSON。

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

如果每個發行者書籍的數量很少而且成長有限，那麼，將書籍參考儲存在發行者文件內可能很有用。 不過，如果每個發行者的書籍數量無限，此資料模型會導致可變動、成長的陣列，如上述的範例發行者文件所示。

切換項目位元會導致模型仍代表相同的資料，但現在可避免這些大型的可變動集合。

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

在上述範例中，我們在發行者文件上捨棄無限制的集合。 我們在每個書籍文件上只有發行者的參考。

### <a name="how-do-i-model-manymany-relationships"></a>如何建立多對多關聯性的模型？

在關聯式資料庫 *多對多* 關聯性中，通常是與聯結資料表模型化，其只是將記錄從其他資料表聯結在一起。

![聯結資料表](./media/sql-api-modeling-data/join-table.png)

您可能會想要使用文件複寫相同的項目，並產生看起來如下所示的資料模型。

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

這應該可行。 不過，載入其中一個作者的書籍，或載入書籍的作者，一律需要對資料庫進行至少兩個其他查詢。 一個對聯結文件的查詢，另一個查詢則用來擷取實際聯結的文件。

如果此聯結資料表的作用完全是在結合兩組資料在一起，那麼為何不完全捨棄它？
請考慮下列。

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

現在，如果我有一個作者，我立即知道他們寫了哪些書，相反，如果我有一本書檔載入，我會知道作者的證。 這樣可以省下對聯結資料表的中繼查詢，減少您的應用程式必須進行的伺服器來回行程數目。

## <a name="hybrid-data-models"></a>混合式資料模型

我們現在已看過內嵌 (或反正規化) 和參考 (或正規化) 資料，如我們所見，各有其優缺點。

不必害怕採行不同的方式。

根據您的應用程式特定的使用模式和工作負載，可能有時候混用內嵌和參考的資料有意義，而可能導致較簡單的應用程式邏輯與較少的伺服器來回行程，同時維持良好的效能等級。

請考慮下列 JSON。

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

這裡我們 (差不多) 看完了內嵌的模型，即來自其他實體的資料會內嵌在最上層的文件中，但參考其他資料。

如果您查看書籍文件，在查看作者陣列時就會看到一些有趣的欄位。 有一個`id`欄位，就是我們用來引用作者文檔的欄位，標準化模型中的標準實踐，但隨後我們也有`name`和`thumbnailUrl`。 我們本可以堅持`id`並離開應用程式，以便使用"連結"從各自的作者文檔中獲取所需的任何附加資訊，但由於我們的應用程式顯示作者的姓名和每本書的縮略圖，因此，我們可以通過使作者**的某些**資料非正常化，將每本書的往返保存到清單中的伺服器。

當然，如果作者的名字被更改或他們想更新他們的照片，我們將不得不去更新每本書，他們曾經出版，但為我們的應用程式，基於假設作者不經常更改他們的名字，這是一個可接受的設計決定。  

在此示例中，有**預先計算的聚合**值，以節省讀取操作上昂貴的處理。 在範例中，作者文件中內嵌的有些資料是在執行階段計算的資料。 每次發行新的書籍時，會建立書籍的文件 **並且** 將 countOfBooks 欄位設定為根據某位特定作者存在的書籍文件數目計算值。 在讀取繁重的系統中 (我們可以負擔執行寫入計算以最佳化讀取)，這項最佳化將很適合。

由於 Azure Cosmos DB 支援**多文件交易**，因此模型現在能夠具有預先計算的欄位。 許多 NoSQL 存放區無法跨文件中執行交易，而因為這項限制而提倡「一律內嵌一切」的設計決策。 藉由 Azure Cosmos DB，您可以使用伺服器端觸發程序或預存程序，插入書籍並更新作者，全都在 ACID 交易內完成。 現在您不**需**在一份文件內嵌所有內容，只需要確保您的資料保持一致。

## <a name="distinguishing-between-different-document-types"></a>區分不同的文件類型

在某些情況下，您可能希望在同一集合中混合不同的文件類型;但是，如果同一集合，則不同類型。當您希望多個相關文檔位於同一[分區](partitioning-overview.md)中時，通常就是這種情況。 例如，您可以將書籍和書籍評論放在相同的集合中，然後按 進行`bookId`分區。 在這種情況下，您通常希望向文檔中添加一個欄位，該欄位標識其類型，以便區分它們。

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>後續步驟

這篇文章的重點，在於了解資料模型在無結構描述環境中是非常重要的。

正如同沒有單一方法可表示螢幕上的資料片段，沒有單一方法可為您的資料建立模型。 您需要了解您的應用程式，以及它將如何產生、取用及處理資料。 然後，藉由套用一些此處所提供的指導方針，您可以設定相關的建立模型，來處理您的應用程式的立即需求。 當您的應用程式需要進行變更時，您可以利用無結構描述之資料庫的彈性來納入變更，並輕鬆進化您的資料模型。

若要深入了解 Azure Cosmos DB，請參閱服務的[文件 (英文)](https://azure.microsoft.com/documentation/services/cosmos-db/)頁面。

若要了解如何跨多個分割將您的資料分區，請參閱[在 Azure Cosmos DB 中分割資料](sql-api-partition-data.md)。

要瞭解如何使用真實示例對 Azure Cosmos DB 上的資料建模和分區，請參閱[資料建模和分區 - 真實示例](how-to-model-partition-example.md)。
