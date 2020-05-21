---
title: 在知識庫上共同作業-QnA Maker
description: QnA Maker 可讓多位使用者對知識庫進行共同作業。 這項功能是連同 Azure 角色型存取控制一起提供。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650779"
---
# <a name="collaboration-with-authors-and-editors"></a>與作者和編輯者共同作業

QnA Maker 資源層級提供共同作業，可讓您根據共同作業者的角色來限制共同作業者的存取權。 深入瞭解 QnA Maker 共同作業者驗證[概念](../Concepts/role-based-access-control.md)。

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>將以角色為基礎的存取（RBAC）新增至您的 QnA Maker 資源

QnA Maker 可讓多人共同處理相同 QnA Maker 資源中的所有知識庫。 這項功能是連同 Azure [角色型存取控制](../../../active-directory/role-based-access-control-configure.md)一起提供。

## <a name="access-at-the-qna-maker-resource-level"></a>在 QnA Maker 資源層級存取

您無法在 QnA Maker 服務中共用特定的知識庫。 如果您想要更細微的存取控制，請考慮將您的知識庫分散到不同的 QnA Maker 資源，然後將角色新增至每個資源。

## <a name="add-role-to-resource"></a>將角色新增至資源

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>將使用者帳戶新增至 QnA Maker 資源

下列步驟會使用共同作業者角色，但可以使用這些步驟來新增任何[角色](../reference-role-based-access-control.md)

1. 登入[Azure](https://portal.azure.com/)入口網站，然後移至您的 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. 移至 [存取控制 (IAM)]**** 索引標籤。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. 選取 [新增]。

    ![QnA Maker IAM 新增](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. 從下列清單中選取角色：

    |角色|
    |--|
    |擁有者|
    |參與者|
    |QnA Maker 讀取器|
    |QnA Maker 編輯器|
    |認知服務使用者|

    ![QnA Maker IAM 新增角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 輸入使用者的電子郵件地址，然後按 [**儲存**]。

    ![QnA Maker IAM 新增電子郵件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

當您將 QnA Maker 服務與登入[QnA Maker 入口網站](https://qnamaker.ai)的人員共用時，他們可以根據其角色查看該服務中的所有知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [測試知識庫](./test-knowledge-base.md)

深入瞭解共同作業：
* [Azure](../../../active-directory/role-based-access-control-configure.md)角色型存取控制
* QnA Maker 以角色為基礎的存取控制[概念](../Concepts/role-based-access-control.md)
