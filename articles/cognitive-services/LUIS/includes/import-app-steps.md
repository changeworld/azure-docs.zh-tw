---
title: 匯入應用程式步驟
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130716"
---
1. 在 [LUIS 入口網站](https://www.luis.ai)中，從 [我的應用程式] 頁面選取 [+ 新增對話應用程式]，然後選取 [匯入為 JSON]。 尋找上一個步驟中已儲存的 JSON 檔案。 您不需要變更應用程式的名稱。 選取 [完成] 

1. 從 [管理] 區段的 [版本] 索引標籤上選取 `0.1` 版本，然後選取 [複製] 以複製版本，並為其指定新的 `ml-entity` 名稱，然後選取 [完成] 以結束複製程序。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。

    > [!TIP]
    > 在修改應用程式之前，複製到新版本是最佳做法。 完成版本變更後，請將版本匯出為 .json 或 .lu 檔案，然後將該檔案簽入您的原始檔控制系統中。

1. 選取畫面頂端的 [建置]，然後按一下左側導覽功能表中的 [意圖]。 您會看到應用程式的意圖，也就是 LUIS 應用程式的主要建置組塊。
