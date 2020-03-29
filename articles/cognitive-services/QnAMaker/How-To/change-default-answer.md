---
title: 獲取預設答案 - QnA 製造商
description: 當與問題不匹配時，將返回預設答案。 您可能希望從標準預設答案更改預設答案。
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76846342"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>為知識庫設置預設答案

當與問題不匹配時，將返回預設答案。 您可能希望從標準預設答案更改預設答案。

## <a name="change-default-answer"></a>變更預設答案

1. 移至 [Azure 入口網站](https://portal.azure.com)，並瀏覽到代表您建立的 QnA Maker 服務之用的資源群組。

2. 按一下以開啟 **App Service**。

    ![在 Azure 入口網站中，存取 QnA Maker 的 App Service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 按一下 [應用程式設定]****，並編輯 [DefaultAnswer]**** 欄位成為所需的預設回應。 按一下 [儲存]****。

    ![選取 [應用程式設定]，然後編輯 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重新啟動 App Service

    ![變更 DefaultAnswer 之後，請重新啟動 QnA Maker Appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>後續步驟

* [使用 QnA 製造商和 LUIS 創建機器人](../tutorials/integrate-qnamaker-luis.md)