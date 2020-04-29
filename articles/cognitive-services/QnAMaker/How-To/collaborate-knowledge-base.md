---
title: 在知識庫上共同作業-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓多位使用者對知識庫進行共同作業。 這項功能是連同 Azure 角色型存取控制一起提供。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75660744"
---
# <a name="collaborate-on-your-knowledge-base"></a>對知識庫進行共同作業

QnA Maker 可讓多人共同處理相同 QnA Maker 資源中的所有知識庫。 這項功能是連同 Azure [角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)一起提供。

執行下列步驟，與他人共用 QnA Maker 服務：

1. 登入 Azure 入口網站，並移至您的 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. 移至 [存取控制 (IAM)]**** 索引標籤。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. 選取 [新增]  。

    ![QnA Maker IAM 新增](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. 選取**擁有者**或**參與者**角色。 您無法透過角色型存取控制授與唯讀存取。 「擁有者」和「參與者」角色具有 QnA Maker 服務的讀寫存取權限。

    ![QnA Maker IAM 新增角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 輸入使用者的電子郵件地址，然後按 [**儲存**]。

    ![QnA Maker IAM 新增電子郵件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

當您將 QnA Maker 服務與共享時，會登入[QnA Maker 入口網站](https://qnamaker.ai)，他們可以看到該服務中的所有知識庫。

請記住，您無法共用 QnA Maker 服務中特定一個知識庫。 如果您想要更細部的存取控制，請考慮在不同的 QnA Maker 服務發佈您的知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [測試知識庫](./test-knowledge-base.md)
