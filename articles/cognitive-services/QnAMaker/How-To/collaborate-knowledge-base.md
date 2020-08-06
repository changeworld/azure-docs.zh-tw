---
title: 在知識庫上共同作業-QnA Maker
description: QnA Maker 可讓多位使用者對知識庫進行共同作業。 這項功能是以 Azure 角色型存取控制 (Azure RBAC) 提供。
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 86c8d919618b37223e51a23635a1322a4e0d780c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837426"
---
# <a name="collaboration-with-authors-and-editors"></a>與作者和編輯者共同作業

QnA Maker 資源層級提供共同作業，可讓您根據共同作業者的角色來限制共同作業者的存取權。 深入瞭解 QnA Maker 共同作業者驗證[概念](../Concepts/role-based-access-control.md)。

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>將 azure 角色型存取控制 (Azure RBAC) 新增至您的 QnA Maker 資源

QnA Maker 可讓多人共同處理相同 QnA Maker 資源中的所有知識庫。 這項功能是連同 Azure [角色型存取控制](../../../active-directory/role-based-access-control-configure.md)一起提供。

## <a name="access-at-the-qna-maker-resource-level"></a>在 QnA Maker 資源層級存取

您無法在 QnA Maker 服務中共用特定的知識庫。 如果您想要更細微的存取控制，請考慮將您的知識庫分散到不同的 QnA Maker 資源，然後將角色新增至每個資源。

## <a name="add-role-to-resource"></a>將角色新增至資源

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>將使用者帳戶新增至 QnA Maker 資源

下列步驟會使用共同作業者角色，但可以使用這些步驟來新增任何[角色](../reference-role-based-access-control.md)

1. 登入[Azure](https://portal.azure.com/)入口網站，然後移至您的 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. 移至 [存取控制 (IAM)]**** 索引標籤。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. 選取 [新增]。

    ![QnA Maker IAM 新增](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 從下列清單中選取角色：

    |角色|
    |--|
    |擁有者|
    |參與者|
    |認知服務 QnA Maker 讀取器|
    |認知服務 QnA Maker 編輯器|
    |認知服務使用者|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM 新增角色。":::

1. 輸入使用者的電子郵件地址，然後按 [**儲存**]。

    ![QnA Maker IAM 新增電子郵件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>View QnA Maker 知識庫

當您將 QnA Maker 服務與登入[QnA Maker 入口網站](https://qnamaker.ai)的人員共用時，他們可以根據其角色查看該服務中的所有知識庫。

當他們選取知識庫時，其在該 QnA Maker 資源上的目前角色會顯示在知識庫名稱旁邊。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="網頁左上角 [知識庫名稱] 旁的 [角色名稱] 以編輯模式的知識庫螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [測試知識庫](./test-knowledge-base.md)

深入瞭解共同作業：
* [Azure](../../../active-directory/role-based-access-control-configure.md)角色型存取控制
* QnA Maker 以角色為基礎的存取控制[概念](../Concepts/role-based-access-control.md)
