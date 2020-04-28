---
title: 在 Azure Cosmos DB 上使用真實世界的範例來建立模型和分割資料
description: 了解如何使用 Azure Cosmos DB Core API 建立實際範例的模型及加以分割
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445384"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>如何使用實際範例在 Azure Cosmos DB 上建立資料的模型及加以分割

本文根據數個 Azure Cosmos DB 概念而撰寫 (例如[資料模型化](modeling-data.md)、[分割](partitioning-overview.md)和[佈建的輸送量](request-units.md))，以示範如何進行實際資料的設計演練。

如果您經常使用關聯式資料庫，您可能已有設計資料模型的習慣和觀念。 基於特定的條件約束，以及 Azure Cosmos DB 的獨特功能，這些最佳做法大多無法發揮實際功效，而可能迫使您選擇次佳的解決方案。 本文的目的，是要引導您在 Azure Cosmos DB 中完成為實際使用案例建立模型的程序，從建立項目模型、實體共置到容器分割，逐步完成。

## <a name="the-scenario"></a>案例

在此練習中，我們將考量*使用者*可建立*貼文*的部落格平台領域。 使用者也可對這些貼文*按讚*及新增*留言*。

> [!TIP]
> 某些字詞會以*斜體*醒目提示；這些字詞表示我們的模型必須操作的某些「事物」。

對我們的規格新增更多需求：

- 首頁會顯示最近建立貼文的摘要，
- 我們可以擷取某個使用者的所有貼文、某篇貼文的所有留言，以及某篇貼文所有的讚，
- 會連同貼文傳回作者的使用者名稱，以及貼文的留言數和按讚數，
- 此外也會連同留言和按讚數傳回留言和按讚者的使用者名稱，
- 以清單形式顯示時，貼文只需要顯示其內容的截斷摘要。

## <a name="identify-the-main-access-patterns"></a>識別主要存取模式

一開始，我們將藉由識別解決方案的存取模式給予初始規格某種結構。 在設計 Azure Cosmos DB 的資料模型時，我們務必要了解的模型所須因應的要求，以確定模型將可有效因應這些要求。

為了讓整體程序更容易執行，我們從命令是寫入要求 (也就是更新系統的意圖) 且查詢為唯讀要求的 [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) 中借用了從某些詞彙，將這些不同的要求分類為命令或查詢。

以下列出我們的平台所必須公開的要求：

- **[C1]** 建立/編輯使用者
- **[Q1]** 取出使用者
- **[C2]** 建立/編輯貼文
- **[Q2]** 擷取貼文
- **[Q3]** 以簡短形式列出使用者的貼文
- **[C3]** 建立批註
- **[Q4]** 列出貼文的留言
- **[C4]** 對貼文按讚
- **[Q5]** 列出貼文的讚
- **[Q6]** 列出以簡短形式建立的*x*最新貼文（摘要）

在此階段中，我們尚未考慮到每個實體 (使用者、貼文等) 所將包含的詳細資料。 此步驟通常是在針對關聯式存放區進行設計時要破解的第一個步驟，因為我們必須找出這些實體在資料表、資料行、外鍵等方面的轉譯方式。對於不會在寫入時強制執行任何架構的檔資料庫，這方面的顧慮就少很多。

之所以要在一開始就找出存取模式，主要是因為這份要求清單將成為我們的測試套件。 我們在每次反覆執行資料模型時，都將查看每個要求，並檢查其效能和延展性。

## <a name="v1-a-first-version"></a>V1：第一個版本

我們一開始有兩個容器：`users` 和 `posts`。

### <a name="users-container"></a>使用者容器

此容器只會儲存使用者項目：

    {
      "id": "<user-id>",
      "username": "<username>"
    }

我們依據 `id` 分割此容器，這表示該容器內的每個邏輯分割區都只會包含一個項目。

### <a name="posts-container"></a>貼文容器

此容器裝載貼文、留言和讚：

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

我們依據 `postId` 分割此容器，這表示該容器內的每個邏輯分割區都會包含一篇貼文、該貼文的所有留言，以及該貼文所有的讚。

請注意，我們在儲存於此容器內的項目中導入了 `type` 屬性，用以區分此容器所裝載的三種實體。

此外，我們也選擇要參考相關資料，而非加以內嵌 (如需這些概念的詳細資訊，請查看[本節](modeling-data.md))，原因是：

- 使用者可建立的貼文並沒有數量上限，
- 貼文沒有長度限制，
- 一篇貼文可以有的留言數和按讚數並沒有上限，
- 我們想要能夠直接對貼文新增留言或按讚，而無須更新貼文本身。

## <a name="how-well-does-our-model-perform"></a>模型的執行成效如何？

現在我們將評估第一個版本的效能和延展性。 針對先前所識別的每個要求，我們將測量其延遲及其耗用的要求單位數量。 這項測量將對一個虛擬資料集執行，其中包含 100,000 名使用者，每個使用者有 5 到 50 篇貼文，而每篇貼文最多有 25 個留言和 100 個讚。

### <a name="c1-createedit-a-user"></a>[C1] 建立/編輯使用者

此要求很容易實作，因為我們剛剛才在 `users` 容器中建立或更新項目。 憑藉 `id` 分割區索引鍵的效用，要求會妥善分散到所有分割區。

![將單一項目寫入使用者容器](./media/how-to-model-partition-example/V1-C1.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 7 毫秒 | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] 擷取使用者

擷取使用者的作業會藉由從 `users` 容器中讀取對應的項目來完成。

![從使用者容器中擷取單一項目](./media/how-to-model-partition-example/V1-Q1.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 2 毫秒 | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] 建立/編輯貼文

類似於 **[C1]**，我們只需寫入 `posts` 容器即可。

![將單一項目寫入貼文容器](./media/how-to-model-partition-example/V1-C2.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 9 毫秒 | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] 擷取貼文

首先我們從 `posts` 容器中擷取對應的文件。 但這樣還不夠，根據我們的規格，我們還必須彙總貼文作者的使用者名稱、此貼文的留言數，和此貼文的按讚數，而為此還需要另行發出 3 個 SQL 查詢。

![擷取貼文並和彙總額外的資料](./media/how-to-model-partition-example/V1-Q2.png)

這些額外的查詢分別會依其各自容器的分割區索引鍵進行篩選，而這正是我們想盡可能提高效能和延展性所需要的。 但我們終究還是需要執行四項作業才能傳回單一貼文，因此我們將下次反覆執行時加以改善。

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 9 毫秒 | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] 以簡短形式列出使用者的貼文

首先，我們必須使用會擷取該名使用者對應貼文的 SQL 查詢，來擷取所需的貼文。 但我們也須發出其他查詢，以彙總作者的使用者名稱以及留言數和按讚數。

![擷取某個使用者的所有貼文並彙總其他資料](./media/how-to-model-partition-example/V1-Q3.png)

此實作有許多缺點：

- 必須針對第一個查詢所傳回的每篇貼文發出會彙總留言數和按讚數的查詢，
- 主要查詢不會依 `posts` 容器的分割區索引鍵進行篩選，而導致整個容器的展開傳送和分割區掃描。

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 130 毫秒 | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] 建立留言

留言可藉由在 `posts` 容器中寫入對應的項目而建立。

![將單一項目寫入貼文容器](./media/how-to-model-partition-example/V1-C2.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 7 毫秒 | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 列出貼文的留言

首先，我們以查詢擷取該貼文的所有留言，且同樣地，我們也必須個別彙總每個留言的使用者名稱。

![擷取某篇貼文的所有留言並彙總其他資料](./media/how-to-model-partition-example/V1-Q4.png)

雖然主要查詢會依容器的分割區索引鍵進行篩選，但個別彙總使用者仍會導致整體效能下降。 我們將在稍後加以改善。

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 23 毫秒 | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] 對貼文按讚

如同 **[C3]**，我們在 `posts` 容器中建立對應的項目。

![將單一項目寫入貼文容器](./media/how-to-model-partition-example/V1-C2.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 6 毫秒 | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 列出貼文的讚

如同 **[Q4]**，我們查詢該貼文的讚，然後彙總其使用者名稱。

![擷取某篇貼文所有的讚並彙總其他資料](./media/how-to-model-partition-example/V1-Q5.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 59 毫秒 | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 以簡短形式列出最近建立的 x 篇貼文 (摘要)

我們查詢依遞減的建立日期排序的 `posts` 容器以擷取最新的貼文，然後彙總每篇貼文的使用者名稱以及留言數和按讚數。

![擷取最新的貼文並彙總其他資料](./media/how-to-model-partition-example/V1-Q6.png)

同樣地，我們的初始查詢不會針對`posts`容器的分割區索引鍵進行篩選，這會觸發昂貴的展開傳送。當我們以較大的結果集為目標，並使用`ORDER BY`子句來排序結果時，這會變得更糟，這會使要求單位的成本更高。

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 306 毫秒 | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>V1 效能的反映

回顧我們在上一節中面臨的效能問題，我們主要可歸納出兩種類別的問題：

- 對於某些要求，必須要發出多個查詢，才能收集我們需要傳回的所有資料，
- 某些查詢不會依其目標容器的分割區索引鍵進行篩選，導致會妨礙延展性的展開傳送。

我們將從第一個問題開始逐一解決這些問題。

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2：介紹反正規化以優化讀取查詢

我們之所以必須在某些情況下發出其他要求，是因為初始要求的結果未包含我們需要傳回的所有資料。 在使用 Azure Cosmos DB 等非關聯式資料存放區時，這類問題通常可藉由對我們的資料集反正規化資料而獲得解決。

在此範例中，我們將修改貼文項目，以新增貼文作者的使用者名稱、留言數和按讚數：

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

我們也會修改留言和讚這兩個項目，以新增其建立者的使用者名稱：

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>反正規化留言數和按讚數

我們想要達到的結果是，每當我們新增留言或讚時，對應貼文中的 `commentCount` 或 `likeCount` 也會遞增。 由於我們的 `posts` 容器是以 `postId` 分割的，因此新的項目 (留言或讚) 及其對應的貼文會位於相同的邏輯分割區中。 因此，我們可以使用[預存程序](stored-procedures-triggers-udfs.md)來執行該作業。

現在，在建立留言時 (**[C3]**)，除了在 `posts` 容器中新增項目以外，我們還會對該容器呼叫下列預存程序：

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

此預存程序會使用貼文的識別碼和新留言的本文作為參數，然後：

- 擷取貼文
- 增加 `commentCount`
- 取代貼文
- 新增留言

預存程序會以不可部分完成交易的形式執行，因此可確保 `commentCount` 的值與留言的實際數目將隨時保持同步。

我們在增加新的讚時理當也呼叫了類似的預存程序，使 `likeCount` 遞增。

### <a name="denormalizing-usernames"></a>反正規化使用者名稱

使用者名稱需要不同的方法，因為使用者不只會位於不同的分割區，也位於不同的容器中。 當我們需要在分割區和容器間反正規化資料時，我們可以使用來源容器的[變更摘要](change-feed.md)。

在此範例中，我們使用 `users` 的變更摘要，以在使用者更新其使用者名稱時做出因應。 發生這種情況時，我們將對 `posts` 容器呼叫另一個預存程序，以傳播變更：

![將使用者名稱反正規化至貼文容器中](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

此預存程序會以使用者的識別碼和使用者的新使用者名稱作為參數，然後：

- 擷取所有符合 `userId` 的項目 (可以是貼文、留言或讚)
- 針對這些項目
  - 取代 `userUsername`
  - 取代項目

> [!IMPORTANT]
> 這項作業的成本高昂，因為它需要在 `posts` 容器的每個分割區上執行此預存程序。 我們假設大部分的使用者在註冊時都選擇適當的使用者名稱，且不會變更，因此這項更新將鮮少執行。

## <a name="what-are-the-performance-gains-of-v2"></a>V2 有哪些效能提升？

### <a name="q2-retrieve-a-post"></a>[Q2] 擷取貼文

現在，反正規化作業已準備就緒，我們只需擷取單一項目來處理該要求即可。

![從貼文容器中擷取單一項目](./media/how-to-model-partition-example/V2-Q2.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 2 毫秒 | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 列出貼文的留言

同樣地，我們不需執行額外的要求來擷取使用者名稱，而只需要依分割區索引鍵進行篩選的單一查詢。

![擷取某貼文的所有留言](./media/how-to-model-partition-example/V2-Q4.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 4 毫秒 | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 列出貼文的讚

列出讚時的情況完全相同。

![擷取某貼文所有的讚](./media/how-to-model-partition-example/V2-Q5.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 4 毫秒 | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3：確定所有要求都是可調整的

檢視整體效能的改進時，我們發現還有兩個要求未完全最佳化：**[Q3]** 和 **[Q6]**。 這些要求牽涉到不會依目標容器的分割區索引鍵進行篩選的查詢。

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] 以簡短形式列出使用者的貼文

此要求獲益於 V2 導入的改進，因而不需要額外的查詢。

![擷取某使用者的所有貼文](./media/how-to-model-partition-example/V2-Q3.png)

但其餘查詢仍未依 `posts` 容器的分割區索引鍵進行篩選。

對此情況的思考方向其實很明確：

1. 此要求*必須*依 `userId` 進行篩選，因為我們想要擷取特定使用者的所有貼文
1. 其執行效果不佳，因為執行依據為 `posts` 容器，但其分割依據並非 `userId`
1. 顯而易見，我們會對分割依據為**`userId` 的容器執行此要求，以解決效能問題
1. 而其實我們已有這樣的容器：`users` 容器！

因此，我們藉由將所有貼文複製到 `users` 容器，來導入第二層反正規化。 藉由這麼做，我們有效地取得以不同維度分割的貼文複本，使其能更有效地依 `userId` 擷取。

`users` 容器現在包含 2 種項目：

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

請注意：

- 我們在使用者項目中導入了 `type` 欄位，用以區分使用者與貼文，
- 我們也在使用者項目中新增了 `userId` 欄位，雖然這在已有 `id` 欄位時是多餘的，但由於 `users` 容器現在會依 `userId` 進行分割 (而非先前的 `id`)，因此成為必要欄位

為了完成此一反正規化，我們再次使用變更摘要。 這次，我們回應 `posts` 容器的變更摘要，以將任何新的或更新的貼文分派至 `users` 容器。 由於列出貼文並不需要傳回其完整內容，因此我們可以在處理時加以截斷。

![將貼文反正規化至使用者容器中](./media/how-to-model-partition-example/denormalization-2.png)

我們現在可以將查詢路由到依據容器的分割區索引鍵篩選的 `users` 容器。

![擷取某使用者的所有貼文](./media/how-to-model-partition-example/V3-Q3.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 4 毫秒 | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 以簡短形式列出最近建立的 x 篇貼文 (摘要)

在此我們必須處理類似的情況：即使已因 V2 中導入的反正規化而不再需要進行額外的查詢，其餘查詢仍不會依據容器的分割區索引鍵進行篩選：

![擷取最新的貼文](./media/how-to-model-partition-example/V2-Q6.png)

依循相同的方式，要讓此要求達到最高的效能和延展性，要求必須僅在一個分割區中。 這是可想而知的，因為我們只需要傳回有限數量的項目；若要填入我們部落格平台的首頁，我們只需要取得 100 篇最新的貼文，而不需要將整個資料集分頁。

因此，為了將這最後一個要求最佳化，我們在設計中導入了第三個容器，完全用來處理此要求。 我們將貼文反正規化到這個新的 `feed` 容器：

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

此容器依 `type` 進行分割，這在我們項目中一律為 `post`。 這麼做可確保此容器中的所有項目將位於相同的分割區中。

要完成反正規化，我們只需連結先前導入的變更摘要管線，以將貼文分派到這個新的容器即可。 需留意的一項重點，就是必須確定我們僅儲存了 100 篇最新的貼文；否則，容器的內容可能會超出分割區的大小上限。 每次在容器中新增文件後，您可以藉由呼叫[後置觸發程序](stored-procedures-triggers-udfs.md#triggers)來確認這一點：

![將貼文反正規化至摘要容器中](./media/how-to-model-partition-example/denormalization-3.png)

以下是會截斷集合的後續觸發程序主體：

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

最後一個步驟是將查詢重新路由到新的 `feed` 容器：

![擷取最新的貼文](./media/how-to-model-partition-example/V3-Q6.png)

| **Latency** | **RU 費用** | **效能** |
| --- | --- | --- |
| 9 毫秒 | 16.97 RU | ✅ |

## <a name="conclusion"></a>結論

讓我們看看不同版本的設計所產生的整體效能和延展性改進。

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **C1** | 7 毫秒 / 5.71 RU | 7 毫秒 / 5.71 RU | 7 毫秒 / 5.71 RU |
| **[Q1]** | 2 毫秒 / 1 RU | 2 毫秒 / 1 RU | 2 毫秒 / 1 RU |
| **C2** | 9 毫秒 / 8.76 RU | 9 毫秒 / 8.76 RU | 9 毫秒 / 8.76 RU |
| **[Q2]** | 9 毫秒 / 19.54 RU | 2 毫秒 / 1 RU | 2 毫秒 / 1 RU |
| **[Q3]** | 130 毫秒 / 619.41 RU | 28 毫秒 / 201.54 RU | 4 毫秒 / 6.46 RU |
| **低耗** | 7 毫秒 / 8.57 RU | 7 毫秒 / 15.27 RU | 7 毫秒 / 15.27 RU |
| **[Q4]** | 23 毫秒 / 27.72 RU | 4 毫秒 / 7.72 RU | 4 毫秒 / 7.72 RU |
| **C4** | 6 毫秒 / 7.05 RU | 7 毫秒 / 14.67 RU | 7 毫秒 / 14.67 RU |
| **[Q5]** | 59 毫秒 / 58.92 RU | 4 毫秒 / 8.92 RU | 4 毫秒 / 8.92 RU |
| **[Q6]** | 306 毫秒 / 2063.54 RU | 83 毫秒 / 532.33 RU | 9 毫秒 / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>我們已將大量讀取案例最佳化

您可能已注意到我們著重於改善讀取要求 (查詢) 的效能，而犧牲了寫入要求 (命令) 的效能。 在許多情況下，目前的寫入作業會透過變更摘要觸發後續的反正規化，而這會加重運算成本並拉長具體化的所需時間。

這相對而言是合理的，因為部落格平台 (例如大部分的社交應用程式) 具有大量讀取的特性，這表示它所須處理的讀取要求數量通常遠高於寫入要求數量 (呈指數性的比例)。 因此，為了讓讀取要求以較低成本和較高的效率執行，而讓寫入要求的成本較為昂貴，是合理的做法。

在我們完成的最佳化之中，**[Q6]** 是最極致的一個，它從 2000 多個 RU 陡降到 17 個 RU；這是我們以每個項目約 10 個 RU 的成本將貼文反正規化所達到的成果。 由於我們處理的摘要要求量遠高於建立或更新貼文的數量，考量到整體的節省效果，這項反正規化的成本是可忽略的。

### <a name="denormalization-can-be-applied-incrementally"></a>反正規化可以累加方式套用

我們在本文中探討的延展性改進，牽涉到整個資料集內的資料反正規化和複製。 應注意的是，這些最佳化並非一開始就必須具備的。 依據分割區索引鍵篩選的查詢在大規模執行時的效能較佳，但跨分割區查詢若鮮少呼叫或僅針對有限的資料集執行，也是完全可接受的。 如果您剛剛建置原型，或啟動了使用者群體較小或受控制的產品，您應該可以後續再進行這些改進；重要的是必須[監視](use-metrics.md)模型的效能，以利判斷進行改進的必要性和時機。

我們用來將更新散佈到其他容器的變更摘要，會持續完整地儲存這些更新。 如此，即使您的系統已有許多資料，您仍可要求容器建立後的所有更新，和以一次性追趕作業的形式啟動反正規化檢視。

## <a name="next-steps"></a>後續步驟

在看完這些關於實際資料模型化和分割的簡介之後，您可以查看下列文章，以檢閱我們所說明的概念：

- [使用資料庫、容器和項目](databases-containers-items.md)
- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中變更摘要](change-feed.md)
