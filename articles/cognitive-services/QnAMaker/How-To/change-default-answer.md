---
title: 取得預設解答 QnA Maker
description: 當不符合問題時，就會傳回預設答案。 您可能想要變更標準預設答案的預設答案。
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097093"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>變更 QnA Maker 資源的預設答案

當不符合問題時，就會傳回預設答案。 您可能想要變更標準預設答案的預設答案。

## <a name="change-default-answer-in-the-azure-portal"></a>變更 Azure 入口網站中的預設答案

1. 移至 [Azure 入口網站](https://portal.azure.com)，並瀏覽到代表您建立的 QnA Maker 服務之用的資源群組。

2. 按一下以開啟 **App Service**。

    ![在 Azure 入口網站中，存取 QnA Maker 的 App Service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 按一下 [應用程式設定]****，並編輯 [DefaultAnswer]**** 欄位成為所需的預設回應。 按一下 **[儲存]** 。

    ![選取 [應用程式設定]，然後編輯 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重新啟動 App Service

    ![變更 DefaultAnswer 之後，請重新啟動 QnA Maker Appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>後續步驟

* [建立知識庫](../How-to/manage-knowledge-bases.md)