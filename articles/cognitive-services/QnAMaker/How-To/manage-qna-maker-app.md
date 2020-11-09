---
title: 管理 QnA Maker 應用程式-QnA Maker
description: QnA Maker 可讓多位使用者對知識庫進行共同作業。 QnA Maker 提供可利用主動式學習來改善知識庫品質的功能。 您可以複習、接受或拒絕，也無須移除或變更現有的問題。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 93d9cc871d1cb114f7f08b68eb8ae9d597e228b9
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376480"
---
# <a name="manage-qna-maker-app"></a>管理 QnA Maker 應用程式

QnA Maker 可讓您根據共同作業者的角色限制共同作業者的存取權，來與不同的作者和內容編輯器共同作業。
深入瞭解 QnA Maker 共同作業者 [驗證概念](../Concepts/role-based-access-control.md)。

您也可以透過 [主動式學習](../Concepts/active-learning-suggestions.md)建議替代問題，來改善知識庫的品質。 使用者提交會納入考慮，並顯示為替代問題清單中的建議。 您可以彈性地將這些建議新增為替代問題或予以拒絕。

您的知識庫不會自動變更。 為了讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="add-azure-role-based-access-control-azure-rbac"></a> (Azure RBAC) 新增 Azure 角色型存取控制

QnA Maker 可讓多人在相同 QnA Maker 資源的所有知識庫上共同作業。 這項功能隨附于 [azure 角色型存取控制 (AZURE RBAC) ](../../../active-directory/role-based-access-control-configure.md)。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 資源層級的存取權

您無法在 QnA Maker 服務中共用特定的知識庫。 如果您想要更細微的存取控制，請考慮將您的知識庫散發到不同的 QnA Maker 資源，然後將角色新增至每個資源。

## <a name="add-a-role-to-a-resource"></a>將角色新增至資源

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>將使用者帳戶新增至 QnA Maker 資源

下列步驟會使用「共同作業者」角色，但您可以使用這些步驟來新增任何[角色](../reference-role-based-access-control.md)

1. 登入 [Azure](https://portal.azure.com/) 入口網站，然後移至您的 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. 移至 [存取控制 (IAM)] 索引標籤。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. 選取 [新增]。

    ![QnA Maker IAM 新增](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 從下列清單中選取角色：

    |角色|
    |--|
    |擁有者|
    |參與者|
    |認知服務 QnA Maker 讀者|
    |認知服務 QnA Maker 編輯器|
    |認知服務使用者|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM 新增角色。":::

1. 輸入使用者的電子郵件地址，然後按 [ **儲存** ]。

    ![QnA Maker IAM 新增電子郵件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>View QnA Maker 知識庫

當您共用 QnA Maker 服務的人員登入 [QnA Maker 入口網站](https://qnamaker.ai)時，他們可以根據其角色查看該服務中的所有知識庫。

當他們選取知識庫時，在知識庫名稱旁邊會顯示其目前在該 QnA Maker 資源上的角色。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="頁面左上角的知識庫名稱，在編輯模式中具有角色名稱的知識庫螢幕擷取畫面。":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>升級執行階段版本以使用主動式學習

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用這項功能。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 受控 (預覽版本) ](#tab/v2)

在 QnA Maker managed (Preview) 中，因為執行時間是由 QnA Maker 服務本身裝載，所以不需要手動升級執行時間。

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>開啟適用于替代問題的主動式學習

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)

依預設會關閉主動式學習。 開啟它以查看建議的問題。 開啟主動式學習之後，您必須將用戶端應用程式的資訊傳送至 QnA Maker。 如需詳細資訊，請參閱 [使用 GenerateAnswer 的架構流程以及從 Bot 定型 api](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 選取 [ **發行** ] 來發行知識庫。 主動式學習查詢只會從 GenerateAnswer API 預測端點收集。 QnA Maker 入口網站中 [測試] 窗格的查詢不會影響主動式學習。

1. 若要在 QnA Maker 入口網站中開啟主動式學習，請移至右上角，選取您的 **名稱** ，然後移至 [ [**服務設定**](https://www.qnamaker.ai/UserSettings)]。

    ![從 [服務設定] 頁面開啟主動式學習的建議問題替代方案。 在右上方的功能表中選取您的使用者名稱，然後選取 [服務設定]。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換 **主動式學習** 。

    > [!div class="mx-imgBorder"]
    > [![在 [服務設定] 頁面上，切換主動式學習功能。如果您無法切換功能，則可能需要升級您的服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > 上述映射上的確切版本只會顯示為範例。 您的版本可能會不同。
    啟用 **主動學習** 之後，知識庫會根據使用者提交的問題定期提供新問題的建議。 您可以再次切換設定以停用 **主動式學習** 。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 受控 (預覽版本) ](#tab/v2)

主動學習預設會在 QnA Maker 受控 (預覽) 中 **開啟** 。 若要查看建議的替代問題，請使用 [編輯] 頁面上的 [ [視圖選項](../How-To/improve-knowledge-base.md#view-suggested-questions) ]。

---

## <a name="review-suggested-alternate-questions"></a>查看建議的替代問題

在每個知識庫的 [ **編輯** ] 頁面上 [查看替代建議的問題](improve-knowledge-base.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立知識庫](./manage-knowledge-bases.md)