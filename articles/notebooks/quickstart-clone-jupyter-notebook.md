---
title: 使用 Azure Notebooks 預覽從 GitHub 複製 Jupyter Notebook
description: 從 GitHub 存放庫中快速複製 Jupyter 筆記本，並在您的 Azure Notebooks 帳戶中執行。
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424472"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>快速入門：在 Azure Notebooks 預覽中複製 Notebook

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

在本快速入門中，您會將儲存在 GitHub 中的 Jupyter Notebook 複製到 Azure Notebooks 帳戶。 

GitHub 存放庫提供 Jupyter Notebook 的儲存體和版本控制。 共同作業者會維護存放庫的本機複本，並從這些複本執行 Notebook。 將 Jupyter Notebook 從 GitHub 複製到您的 Azure Notebooks 帳戶，可建立獨立的 Notebook 複本。 變更只會儲存在您的 Azure Notebooks 帳戶中，因此不會影響原始 GitHub 存放庫。 

因為您的 Azure Notebooks 複本位於雲端，因此您可與不需要製作任何本機複本或已在其電腦上安裝 Jupyter 的共同作業者共用該複本。 複製 Notebook 也可能只是當作您自己專案的起點，或是為了取得資料檔案。 

## <a name="prerequisites"></a>Prerequisites
無。

## <a name="clone-azure-cognitive-services-notebooks"></a>複製 Azure 認知服務筆記本

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]  ：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案]  頁面上，選取向上鍵按鈕 (鍵盤快速鍵：U；當瀏覽器視窗夠寬時，此按鈕會顯示為 [上傳 GitHub 存放庫]  )：

    ![[我的專案] 頁面上的 [上傳 GitHub 存放庫] 命令](media/quickstarts/upload-github-repo-command.png)

1. 在顯示的 [上傳 GitHub 存放庫]  中，輸入或設定下列詳細資料，然後選取 [匯入]  ：

   - **GitHub 存放庫**：Microsoft/cognitive-services-notebooks (此名稱可在 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) 複製 Azure 認知服務的 Jupyter 筆記本)。
   - **以遞迴方式複製**：(已清除)
   - **專案名稱**：認知服務複製
   - **專案識別碼**：cognitive-services-clone
   - **公用**：(已清除)

     ![上傳 GitHub 存放庫快顯視窗以收集存放庫資訊](media/quickstarts/upload-github-repo-popup.png)

1. 請耐心等候此程序完成；複製存放庫可能需要幾分鐘的時間。

1. 複製完成之後，Azure Notebook 會帶您前往新的專案，您可以在那裡看到所有檔案的複本。

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="檢視已完成的複本。" lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>共用筆記本

1. 若要共用已複製專案的複本，請使用 [共用] 控制或取得連結、取得包含連結的 HTML 或 Markdown 程式碼，或建立包含結的電子郵件訊息：

    ![專案共用命令](media/quickstarts/share-project-command.png)

1. 因為您在複製專案時清除了 [公用] 選項，因此複本是私密的。 若要將您的複本設為公用，選取 [專案設定]、在快顯視窗中設定 [公用專案] 選項，然後選取 [儲存]。

1. 在專案中選取要執行的筆記本。 例如，Azure 認知服務存放庫中的每個筆記本都是自己獨立的快速入門。 下圖顯示新增認知服務 API 訂用帳戶金鑰，並將搜尋字詞從 "puppies" 變更為 "bunnies" 之後，使用 BingImageSearchAPI 筆記本的結果：

    ![執行從 GitHub 複製的 Jupyter 筆記本](media/quickstarts/clone-notebook-result.png)

1. 當您執行筆記本完畢之後，選取 [檔案] > [關閉並終止] 來關閉筆記本及其瀏覽器視窗。

1. 若要共用專案中的個別筆記本，以滑鼠右鍵按一下筆記本，然後選取 **複製連結** (鍵盤快速鍵：y)：

    ![將連結複製到個別筆記本的內容功能表命令](media/quickstarts/copy-link-to-individual-notebook.png)

1. 若要編輯筆記本以外的檔案，以滑鼠右鍵按一下專案中的檔案，然後選取 **編輯檔案** (鍵盤快速鍵：i)。 預設動作 **執行** (鍵盤快速鍵：r) 只會顯示檔案內容，而且不允許編輯。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
