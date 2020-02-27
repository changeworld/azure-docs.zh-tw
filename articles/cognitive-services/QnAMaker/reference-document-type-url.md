---
title: 支援匯入的 Url 類型-QnA Maker
description: 瞭解 Url 類型如何用來匯入和建立 QnA 集。
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651987"
---
# <a name="urls-supported-for-importing-documents"></a>匯入檔所支援的 Url

瞭解 Url 類型如何用來匯入和建立 QnA 集。

## <a name="faq-urls"></a>常見問題集 URL

QnA Maker 可支援 3 種不同形式的常見問題集網頁：

* 一般的常見問題集頁面
* 具有連結的常見問題集網頁
* 具有主題首頁的常見問題頁面

### <a name="plain-faq-pages"></a>一般的常見問題集頁面

這是最常用的常見問題集頁面類型，在此處，解答會在相同的頁面中緊接在問題後面。

以下是一般常見問題集網頁的範例：

![知識庫的一般常見問題集頁面範例](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>具有連結的常見問題集網頁

在這種常見問題集網頁中，問題會彙總在一起並連結至解答 (位於相同網頁的不同區段，或位於不同網頁)。

以下是連結位於相同網頁區段的常見問題集網頁範例：

 ![知識庫的區段連結常見問題集頁面範例](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>父主題頁面連結至子系回應頁面

這種類型的常見問題具有 [主題] 頁面，其中的每個主題都會連結至不同頁面上的一組對應問題和答案。 QnA Maker 會編目所有連結的頁面，以 & 答案將對應的問題解壓縮。

以下是主題頁面的範例，其中包含不同頁面中常見問題區段的連結。

 ![知識庫的深層連結常見問題集頁面範例](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>支援 Url

QnA Maker 可處理半結構化的支援網頁，例如 Web 文件，其中會說明如何執行指定工作、如何診斷及解決特定問題，以及指定程序的最佳做法是什麼。 內容若具有包含階層式標題的明確結構，擷取將會有最佳效果。

> [!NOTE]
> 擷取支援文章是一項新功能，並且處於早期階段。 其適合用於結構完整且不包含複雜標頭/頁尾的簡單頁面。

![QnA Maker 支援從半結構化的網頁進行擷取，其中會以階層式標題顯示清楚的結構](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)