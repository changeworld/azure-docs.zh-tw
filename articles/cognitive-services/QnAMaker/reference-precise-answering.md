---
title: 使用答案範圍偵測進行精確的回應-QnA Maker
description: 瞭解 QnA Maker 受控提供的精確解答功能。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384082"
---
# <a name="precise-answering"></a>精確的解答

精確的回應功能可讓您從知識庫中提供的最佳候選答案，取得任何使用者查詢的精確簡短答案。 這項功能會在執行時間使用深度學習模型，該模型可瞭解使用者查詢的意圖，並從解答的答案中偵測確切的簡短答案（如果回應中有短暫的答案）。 

這項功能在 [測試] 窗格中預設為開啟，讓您可以測試案例的特定功能。 這項功能對於內容開發人員和一般使用者都非常有利。 現在，內容開發人員不需要針對知識庫中的每個事實手動策展特定的 QnA 組，而且使用者不需要查看從服務傳回的整個答案，以找出回答使用者查詢的實際事實。 

## <a name="precise-answering-on-qna-maker-portal"></a>QnA Maker 入口網站的精確解答

在 QnA Maker 入口網站中，當您開啟 [測試] 窗格時，您會看到可在頂端 **顯示簡短答案** 的選項。 預設會選取此選項。 當您在 [測試] 窗格中輸入查詢時，如果答案的答案有短暫的答案，您將會看到簡短答案以及答案的答案。 
 
![受管理啟用的測試窗格](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

如果您只想要在 [測試] 窗格中查看回應的格式，則可以取消選取 [ **顯示簡短答案** ] 選項。 

這項服務也會傳回精確答案的信賴分數作為 **答案範圍分數** ，您可以選取 [測試] 窗格中的查詢正下方的 [ **檢查** ] 選項來檢查。

![受管理的答案範圍分數](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>發佈 QnA Maker bot

當您發佈 bot 時，您會在應用程式中依預設取得精確的回應啟用體驗，您將會在其中看到簡短的答案以及答案的段。 使用者可以透過透過 eBot app service 更新範本的方式，彈性地選擇其他體驗。 

## <a name="language-support"></a>語言支援

目前只支援英文版的精確回應功能。
