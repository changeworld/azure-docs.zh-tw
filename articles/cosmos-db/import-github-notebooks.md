---
title: 將筆記本從 GitHub 存放庫匯入 Azure Cosmos DB 並加以執行
description: 了解如何連線至 GitHub，並將筆記本從 GitHub 存放庫匯入您的 Azure Cosmos 帳戶。 匯入之後，您可以執行、編輯這些筆記本，並將變更儲存回 GitHub。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 8315369d2100036a50aae770267aa04bceb2dfb0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339796"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>將筆記本從 GitHub 存放庫匯入 Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

為您的 Azure Cosmos 帳戶[啟用筆記本支援](enable-notebooks.md)之後，您可以建立筆記本、從本機電腦上傳新的筆記本，或從您的 GitHub 帳戶匯入現有的筆記本。 本文示範如何將您的筆記本工作區連線至 GitHub，並將筆記本從 GitHub 存放庫匯入您的 Azure Cosmos 帳戶。 匯入之後，您可以執行這些筆記本、進行變更，並將變更儲存回 GitHub。

## <a name="get-notebooks-from-github"></a>從 GitHub 取得筆記本

您可以連線到自己的 GitHub 存放庫或其他公用 GitHub 存放庫，以在 Azure Cosmos DB 讀取、撰寫和共用筆記本。 使用下列步驟連線至 GitHub 帳戶：

1. 登入 [Azure 入口網站](https://portal.azure.com/)，並導覽至您的 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 索引標籤。此索引標籤會顯示現有的資料庫、容器和筆記本。

1. 選取 [連線到 GitHub] 功能表項目。

1. 隨即開啟索引標籤，您可以從中選擇僅連線到 **公用存放庫** ，或連線到 **公用和私人存放庫** 。  選擇所需的選項後，請選取 [授與存取權]。 Azure Cosmos DB 需要授權，才能存取您 GitHub 帳戶中的存放庫。

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="授與 Azure Cosmos DB 存取 GitHub 存放庫的權限":::

1. 系統會將您重新導向至「github.com」網頁，您可以在此確認授權。 選取 [授權 AzureCosmosDBNotebooks] 按鈕，並在提示中輸入您的 GitHub 帳戶密碼。

1. 授權成功之後，系統會帶您返回 Azure Cosmos 帳戶。 接著，您可以從 GitHub 帳戶查看所有公用/私人存放庫。 您可以從可用清單中選取存放庫，或使用其 URL 執行新增存放庫。

1. 選取所需的存放庫之後，項目會從 [取消釘選的存放庫] 區段移至 [釘選的存放庫] 區段。 如有需要，您也可以選擇該存放庫的特定分支來匯入筆記本。

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="選擇存放庫和分支":::

1. 選取 [確定]，以完成匯入作業。 在您存放庫的所選取分支中，所有可用的筆記本會匯入您的 Azure Cosmos 帳戶。

與 GitHub 帳戶整合之後，只有您才可以在您的 Azure Cosmos 帳戶中查看存放庫和筆記本的清單。 即使有多個使用者登入 Azure Cosmos DB 帳戶，並新增自己的帳戶，也是如此。 換句話說，多個使用者可以使用相同的 Azure Cosmos 帳戶，將筆記本工作區連線至 GitHub。 不過，每個使用者只會看到他們已匯入的存放庫和筆記本清單。 您不會看見其他人匯入的筆記本。

若要將您的 GitHub 帳戶與筆記本工作區中斷連線，請開啟 [資料總管] 索引標籤，選取 [GitHub 存放庫] 旁的 [`…`]，然後選取 [與 GitHub 中斷連線]。

## <a name="edit-a-notebook-and-push-changes-to-github"></a>編輯筆記本，並將變更推送至 GitHub

您可以編輯現有的筆記本，或將新筆記本新增至存放庫，並將變更儲存回 GitHub。

編輯現有的筆記本之後，請選取 [儲存]。 隨即開啟對話方塊，您可以在這裡為您所做的變更輸入認可訊息。 選取 [認可]，GitHub 中的筆記本會隨之更新。 您可以登入 GitHub 帳戶，並驗證認可記錄，以驗證更新。

在一般 GitHub 流程中，系統通常會在認可變更後將變更推送至遠端。 不過，在這種情況下，認可選項的目的是將您的更新「暫存、認可和推送」至 GitHub。

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="編輯筆記本和認可 GitHub 的變更":::

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點

