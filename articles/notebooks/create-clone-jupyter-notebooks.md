---
title: 建立和複製 Jupyter 筆記本-Azure Notebooks 預覽
description: Azure Notebooks 預覽專案會管理筆記本和相關檔案的集合，您可以從其他來源建立新的或複製這些檔案。
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: e1321afc2ce294c8a39ba8d55574e2ca949f632e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831279"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>在 Azure Notebooks Preview 中建立和複製專案

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks 會將您的 Jupyter Notebooks 和相關檔案整理成邏輯群組，稱為*專案*。 首先將專案建立成容器，接著在資料夾中，與其他專案檔建立或複製一個或多個筆記本。 ([教學課程](tutorial-create-run-jupyter-notebook.md)會示範此流程。)

專案還會維護，影響執行筆記本伺服器的中繼資料和其他組態設定，包括自訂設定步驟和套件安裝。 如需詳細資訊，請參閱[管理和設定專案](configure-manage-azure-notebooks-projects.md)。

## <a name="use-the-my-projects-dashboard"></a>使用 [我的專案] 儀表板

`https://notebooks.azure.com/<userID>/projects` 所在的 [我的專案]**** 儀表板是您檢視、管理和建立專案的位置：

[![Azure Notebooks 中的 [我的專案] 儀表板](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

您在儀表板上可進行的操作，取決於您是否使用該帳戶所擁有的使用者識別碼登入而定：

| Command | 可供 | Description |
| --- | --- | --- |
| **執行** | 擁有者 | 啟動 Project Server，並在 Jupyter 中開啟專案資料夾。 (更常見的是，您先巡覽到專案資料夾，然後從該處啟動 筆記本。) |
| **下載** | 任何人 | 將選取專案的複本下載為 ZIP 檔案。 |
| **共用** | 任何人 | 顯示共用快顯，透過快顯您可以取得所選專案的 URL，分享到社交媒體、 傳送具有 URL 的電子郵件，並使用 URL 取得具有「 啟動筆記本」徽章的 HTML 或 Markdown 程式碼 (請參閱[取得啟動徽章](#obtain-a-launch-badge))。 |
| **刪除** | 擁有者 | 刪除選取的專案。 此作業無法復原。 |
| **終端** | 擁有者 | 啟動 Project Server，接著會使用該伺服器的 bash 終端機 開啟新的瀏覽器視窗。 |
| **+ 新增專案** | 擁有者 | 建立新專案。 請參閱[建立新專案](#create-a-new-project)。 |
| **上傳 GitHub 存放庫** | 擁有者 | 從 GitHub 匯入專案。 [從 GitHub 匯入專案](#import-a-project-from-github)。 |
| **複製** | 任何人 | 將選取的專案複製到您自己的帳戶。 若尚未準備好，會提示您登入。 請參閱[複製專案](#clone-a-project)。 |

### <a name="obtain-a-launch-badge"></a>取得啟動徽章

當您使用**共用**命令，並選取 [內嵌]**** 索引標籤，就可以複製 HTML 程式碼或建立 「 啟動筆記本」徽章的 Markdown：

![啟動筆記本徽章](https://notebooks.azure.com/launch.png)

如果您沒有 Azure Notebooks 專案，可以從 GitHub 使用下列範本，直接以適當的使用者名稱和存放庫名稱來建立複製的連結：

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>建立新專案

當您使用 **+ 新增專案**命令時，會顯示 Azure Notebooks 會顯示**建立新專案**快顯。 在此快顯中，輸入下列資訊，然後選取 [建立]****︰

| 欄位 | 描述 |
| --- | --- |
| 專案名稱 | Azure Notebooks 用於顯示用途的專案易記名稱。 例如，「我的筆記本專案」。 |
| 專案識別碼 | 自訂識別碼會成為您用來共用專案 URL 的一部分 (格式為 `https://notebooks.azure.com/<user_id>/projects/<project_id>`)。 此識別碼只能使用字母、數位和連字號、限制為30個字元，而且不能是[保留的專案識別碼](#reserved-project-ids)。 如果您不確定要使用的項目，常見的慣例是使用專案名稱的小寫版本，讓空格轉變成連字號，例如 「 my-notebook-project 」 (必要時，可截斷以符合長度限制)。 |
| 公用 | 如果設定，可讓任何人存取的專案連結。 建立私人專案時，請清除此選項。 |
| 使用讀我檔案初始化此專案 | 如果設定，會在專案中建立預設 *README.md* 檔案。 如有需要，*README.md* 檔案是您為專案提供文件的地方。 |

### <a name="reserved-project-ids"></a>保留的專案識別碼

下列保留字本身不能當做專案識別碼使用。 不過，這些保留字可以當做較長專案識別碼的一部分使用。

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| 關於 | account | 系統管理 | api | blog | 教室 |
| 內容 | 儀表板 | 探索 | 常見問題 | help | html |
| home | import | 圖書館 | 管理 | new | notebook |
| Notebooks | pdf | preview | 定價 | profile | 搜尋 |
| status | 支援 | test | | | |

如果您嘗試使用這些單字之一做為專案識別碼，則 [**建立新專案**] 和 [**專案設定**] 快顯視窗會指出「媒體櫃識別碼是保留的識別碼」。

由於專案識別碼也是專案 URL 的一部分，因此 ad 封鎖程式軟體可能會封鎖某些關鍵字的使用，例如 "廣告"。 在這種情況下，請在專案識別碼中使用不同的單字。

## <a name="import-a-project-from-github"></a>從 GitHub 匯入專案

您可以輕鬆地將整個公用 GitHub 存放庫，匯入成包括任何資料和 *README.md* 檔案的專案。 使用**上傳 GitHub 存放庫**命令，在快顯中以提供下列詳細資料，然後選取 [匯入]****：

| 欄位 | 說明 |
| --- | --- |
| GitHub 儲存機制 | Github.com 上的來源存放庫名稱。 例如，若要複製適用于 Azure 認知服務的 Jupyter 筆記本 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ，請輸入「Microsoft/認知服務-筆記本」。  |
| 以遞迴方式複製 | GitHub 存放庫可以包含多個子存放庫。 如果您想要複製父存放庫及其所有子系，請設定此選項。 因為存放庫可以擁有多個子系，除非您知道 有需要，不然請清除此選項。 |
| 專案名稱 | Azure Notebooks 用於顯示用途的專案易記名稱。 |
| 專案識別碼 | 自訂識別碼會成為您用來共用專案 URL 的一部分 (格式為 `https://notebooks.azure.com/<user_id>/projects/<project_id>`)。 此識別碼只能使用字母、數位和連字號、限制為30個字元，而且不能是[保留的專案識別碼](#reserved-project-ids)。 如果您不確定要使用的項目，常見的慣例是使用專案名稱的小寫版本，讓空格轉變成連字號，例如 「 my-notebook-project 」 (必要時，可截斷以符合長度限制)。 |
| 公用 | 如果設定，可讓任何人存取的專案連結。 建立私人專案時，請清除此選項。 |

從 GitHub 匯入存放庫也會匯入其記錄。 您可以從終端機使用標準的 Git 命令，來認可新的變更，從 GitHub 提取變更等等。

## <a name="clone-a-project"></a>複製專案

複製會在您自己的帳戶建立一份現有專案的複本，讓您可以在專案中，執行和修改任何筆記本或其他檔案。 您也可以使用複製來建立自己專案的副本，讓您可以在其中進行實驗或其他工作，而不會影響原始專案。

若要複製專案：

1. 在 [我的專案]**** 儀表板上，以滑鼠右鍵按一下所需的專案，然後選取 [複製]**** (鍵盤快速鍵： c)。

    ![在專案操作功能表上的複製命令](media/clone-command.png)

1. 在**複製專案**快顯中，輸入複製的名稱和識別碼，並指定複製是否為公用。 這些設定與[新專案](#create-a-new-project)的設定相同。

    ![複製專案快顯](media/clone-project.png)

1. 選取 [複製]**** 按鈕後，Azure Notebooks 會直接巡覽至複本。

## <a name="next-steps"></a>後續步驟

- [探索範例筆記本](azure-notebooks-samples.md)
- [如何：設定和管理專案](configure-manage-azure-notebooks-projects.md)
- [如何：從筆記本內安裝套件](install-packages-jupyter-notebook.md)
- [如何：顯示投影片放映](present-jupyter-notebooks-slideshow.md)
- [如何：使用資料檔案](work-with-project-data-files.md)
- [如何：存取資料資源](access-data-resources-jupyter-notebooks.md)
- [如何：使用 Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
