---
title: 使用自訂環境建立 Azure Notebooks 預覽專案
description: 在使用一組已安裝的特定套件及啟動指令碼設定的 Azure Notebooks 預覽中建立新專案。
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196836"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>快速入門：在 Azure Notebooks 預覽中使用自訂環境建立專案

Azure Notebooks 中的專案是一組檔案，集合了筆記本、資料檔案、文件、影像等等，還有可使用特定安裝程式命令來設定的環境。 使用專案來定義環境，任何人只要將專案複製到自己的 Azure Notebooks 帳戶，即擁有重新建立必要環境所需的所有資訊。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>建立專案

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]  ：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案]  頁面上，選取 [+ 新增專案]  \(鍵盤快速鍵：n\)；瀏覽器視窗很窄時，該按鈕會顯示為 **+** ：

    ![[我的專案] 頁面上的 [新增專案] 命令](media/quickstarts/new-project-command.png)

1. 在顯示的 [建立新專案]  快顯視窗中，輸入或設定下列詳細資料，然後選取 [建立]  ：

    - **專案名稱**：使用自訂環境的專案
    - **專案識別碼**：project-custom-environment
    - **公用專案**：(已清除)
    - **建立 README.md**：(已清除)

1. 稍後，Azure Notebooks 會帶您至新的專案。 選取 [+ 新增]  下拉式清單 (可能只顯示為 **+** )，然後選取 [Notebook]  ，將 Notebook 新增至專案。

1. 提供像是 *Custom environment.ipynb* 的 Notebook 名稱，為語言選取 **Python 3.6**，然後選取 [新增]  。

## <a name="add-a-custom-setup-step"></a>新增自訂設定步驟

1. 在專案頁面中，選取 [專案設定]  。

    ![專案設定命令](media/quickstarts/project-settings-command.png)

1. 在 [專案設定]  快顯視窗中，選取 [環境]  索引標籤，然後選取 [環境設定步驟]  下的 [+ 新增]  ：

    ![新增環境設定步驟的命令](media/quickstarts/environment-add-command.png)

1. [+ 新增]  命令建立的步驟是由作業和從專案的檔案中選取的目標檔案來定義的。 系統支援下列作業：

   | 作業 | 描述 |
   | --- | --- |
   | Requirements.txt | Python 專案會在 requirements.txt 檔案中定義其相依性。 使用此選項，請從專案的檔案清單中選取適當的檔案，還要在出現的其他下拉式清單中選取 Python 版本。 若有必要，請選取 [取消]  以回到專案、上傳或建立檔案，接著返回 [專案設定]   > [環境]  索引標籤，然後建立新的步驟。 備妥此步驟，在專案中的執行 Notebook 就會自動執行 `pip install -r <file>` |
   | Shell 指令碼 | 選取 bash shell 指令碼 (通常是具有 *.sh* 副檔名的檔案)，其中包含您希望執行以初始化環境的任何命令。 |
   | Environment.yml | 使用 Conda 管理環境的 Python 專案，可使用 *environments.yml* 檔案來描述相依性。 使用此選項時，請從專案的檔案清單中選取適當的檔案。 |

   > [!WARNING]
   > 這是開發中的預覽服務，目前有一個已知問題，即 `Environment.yml` 設定無法如預期套用至您的專案。 其中的專案和 Jupyter Notebook 目前不會載入指定的環境檔案。

1. 若要移除任何設定步驟，請選取該步驟右邊的 **X**。

1. 當所有設定步驟都準備就緒之後，請選取 [儲存]  。 (選取 [取消]  以捨棄變更)。

1. 若要測試環境，請建立並執行新的 Notebook，然後根據環境中的套件，使用陳述式來建立程式碼單元，例如使用 Python `import` 陳述式。 如果此陳述式執行成功，則表示已在環境中成功安裝所需的套件。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure Notebooks 中管理和設定專案](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
