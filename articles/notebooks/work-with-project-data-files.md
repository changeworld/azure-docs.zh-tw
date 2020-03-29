---
title: 使用 Azure 筆記本預覽的專案導入和匯出資料
description: 瞭解如何將資料從外部源引入 Azure 筆記本預覽專案，以及如何從專案匯出資料。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646971"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>使用 Azure 筆記本預覽專案中的資料檔案

資料是許多的 Jupyter Notebook 的命脈，特別是用於資料科學的 Notebook。 使用 Azure Notebooks，您可以輕鬆地從各種來源匯入專案中，然後從 Notebook 使用該資料。 您也可以讓 Notebooks 產生儲存在專案中的資料，再下載用於其他地方。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

執行中 Notebook 內的 [資料]**** 功能表，也提供 [上傳]**** 與 [下載]**** 命令，可使用專案中的檔案以及目前 Notebook 工作階段的暫存檔案。

您也可以使用 Notebook 內的程式碼，直接存取各種不同的資料來源，包括專案內的檔案。 您也可以使用程式碼單元中的命令來存取任意資料。 因為這類資料會以變數儲存在 Notebook 工作階段內，除非您使用程式碼明確產生專案檔，否則資料不會儲存在專案中。

使用資料中的程式碼，最好在執行中的 Notebook 本身內體驗：為此，請參閱[取得您在 Azure Notebooks 範例 Notebook 中的資料](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)。

本文其餘的部分會提供專案層級檔案作業的詳細資料。

## <a name="import-data"></a>匯入資料

您可以在專案儀表板中或執行中的 Notebook 內，使用 [資料]**** 功能表或像是 `curl` 命令，將檔案帶入專案。

### <a name="import-files-from-the-project-dashboard"></a>從專案儀表板匯入檔案

1. 在專案中，瀏覽至您要匯入檔案的資料夾。

1. 選取 [上傳]**** 命令，然後選取 [從 URL]**** 或 [從電腦]**** 及專案的必要資詳細資料，以用於您要匯入資料：

   - **從 URL**：在 **"檔 URL"** 欄位中輸入源位址，在 **"檔案名"** 欄位中輸入要分配給專案中筆記本的檔案名。 接著，選取 [+ 新增檔案]****，將 URL 新增至上傳清單。 針對任何其他 URL 重複此程序，然後選取 [完成]****。

     ![從 URL 上傳的快顯視窗](media/quickstarts/upload-from-url-popup.png)

   - **從電腦**：將檔拖放到快顯視窗中，或選擇 **"選擇檔"，** 然後流覽並選擇要導入的資料檔案。 您可以放入或選擇任意數量且類型與格式不拘的檔案，因為是根據 Notebook 中的程式碼來開啟檔案和剖析其資料。

     ![從電腦上傳的快顯視窗](media/quickstarts/upload-from-computer-popup.png)

1. 匯入之後，檔案會出現在專案儀表板上，並可使用包含資料夾的相對路徑名稱，在 Notebook 程式碼內存取。

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>從 Notebook 的 [檔案] 功能表匯入檔案

1. 在正在運行的筆記本中，選擇 **"檔** > **上傳**"命令：

    ![Notebook 內的 [檔案上傳] 功能表命令](media/file-menu-upload.png)

1. 在隨即開啟的對話方塊中，瀏覽至您想要上傳的檔案並加以選取。 您可以選取任意數目且類型不拘的檔案。 完成時選取 [開啟]****。

1. 在顯示的 [上傳狀態]**** 快顯視窗中，從下拉式清單中選取 [目的資料夾]****：

    - 會話資料夾*~/*（ ）： 將檔上載到當前筆記本會話，但不會在專案中創建檔。 工作階段資料夾與專案資料夾是對等資料夾，但在工作階段結束之後即不存在。 若要存取程式碼中的工作階段檔案，請在檔案名稱前面加上相對路徑 *.../*。

        使用工作階段資料夾可幫助進行實驗，並避免而長期來說可有可無的檔案造成專案過於凌亂。 您也可以將檔案上傳到專案中包含同名檔案的資料夾中，而不會造成衝突，也無需將檔案重新命名。 例如，假設專案中已有一個版本的*data.csv*，但您想要試驗不同版本的*data.csv*。 將檔案上傳到工作階段資料夾，您就可以使用所上傳檔案中的資料來執行 Notebook (使用 *.../data.csv* 在程式碼中參考)，而不是專案檔中的資料。

    - 專案資料夾 (*/project*)：在程式碼中使用相對路徑名稱將檔案上傳至專案進行存取。 將檔案上傳到此資料夾，就如同在專案儀表板上傳檔案。 檔案會與專案一起儲存，並可在後續的工作階段使用。

        如果您嘗試上傳的檔案和已存在專案中的檔案同名，上傳會失敗。 若要覆寫檔案，請改從提供覆寫選項的專案儀表板上傳新的檔案。

1. 選取 [開始上傳]****，完成此流程。

### <a name="create-or-import-files-using-commands"></a>使用命令建立或匯入檔案

您可以在終端機或 Python 程式碼單元內使用命令，在專案和工作階段資料夾內建立檔案。 例如，像是 `curl` 和 `wget` 命令會直接從網際網路下載檔案。

若要在終端機中下載檔案，請在專案儀表板上選取 [終端機]**** 命令，然後輸入適當的命令：

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

在 Notebook 中使用 Python 程式碼單元時，請在命令前面加上 `!`。

專案資料夾是預設資料夾，因此指定像是 *oil_price.csv*的目的檔案名稱，會在專案中建立該檔案。 若要建立工作階段檔，請在名稱前面加上 *.../*，如 *.../oil_price.csv*。

### <a name="create-files-in-code"></a>以程式碼建立檔案

使用建立檔案的程式碼 (例如 pandas `write_csv` 函式) 時，路徑名稱一律是專案資料夾的相對路徑名稱。 使用 *.../* 建立的工作階段檔案會在 Notebook 暫止並關閉時捨棄。

## <a name="export-files"></a>匯出檔案

您可以從專案儀表板或從 Notebook 內將資料匯出。

## <a name="export-files-from-the-project-dashboard"></a>從專案儀表板匯入檔案

在專案儀表板上，以滑鼠右鍵按一下檔案，然後選取 [下載]****：

![專案項目快顯功能表上的下載命令](media/download-command.png)

您也可以選取檔案，並使用儀表板上的 [下載]**** 命令 (鍵盤快速鍵：d)：

![專案儀表板上的 [下載] 工具列命令](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>從 Notebook 的 [資料] 功能表匯出檔案

1. 選擇"**檔** > **下載"** 功能表命令：

    ![Notebook 內的 [資料下載] 功能表命令](media/file-menu-download.png)

1. 隨即出現的快顯視窗會顯示工作階段中的資料夾；*project* 資料夾包含專案檔：

    ![您選取檔案與資料夾的 [資料下載] 快顯命令](media/file-menu-download-popup.png)

1. 選取您想要下載的檔案和資料夾左側的方塊，然後選取 [下載已選項目]****。

1. Notebook 會準備單一 *.zip* 檔案，其中包含所選取的檔案，然後您可以如往常一樣從瀏覽器來儲存。 即使您要下載單一檔案，Notebook 仍會建立 *.zip* 檔案。

## <a name="next-steps"></a>後續步驟

- [在 Notebook 中存取雲端資料](access-data-resources-jupyter-notebooks.md)
