---
title: 教學課程 - 建立並執行 Jupyter Notebook - Azure Notebooks 預覽
description: 了解如何在 Azure Notebooks 預覽中建立和執行 Jupyter Notebook，以示範資料科學的線性迴歸流程。
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: cb8be1618de7e95a7096e4ea9dec78bf4aa277f6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589246"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>教學：使用 Python 建立和執行 Jupyter Notebook

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

本教學將逐步引導您使用 Azure Notebooks 來建立完整的 Jupyter Notebook，以示範簡單的線性迴歸。 在本教學中，您將熟悉 Jupyter Notebook UI，其中包括建立不同的資料格、執行資料格，以及透過放映投影片的方式呈現筆記本。

您可在 [GitHub - Azure Notebooks 範例](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)中找到已完成的 Notebook。 不過，本教學會從新的專案和空白的 Notebook 開始，讓您體驗逐步建立的過程的過程。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立包含一些範例資料的專案 Notebook
> * 使用 Notebook 介面來建立各種類型的資料格
> * 執行 Notebook
> * 儲存 Notebook
> * 在 Visual Studio Code 中對 Notebook 進行偵錯

## <a name="create-the-project"></a>建立專案

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]  ：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案]  頁面上，選取 [+ 新增專案]  \(鍵盤快速鍵：n\)；瀏覽器視窗很窄時，該按鈕會顯示為 **+** ：

    ![[我的專案] 頁面上的 [新增專案] 命令](media/quickstarts/new-project-command.png)

1. 在顯示的 [建立新專案]  快顯視窗中，輸入或設定下列詳細資料，然後選取 [建立]  ：

   - **專案名稱**：線性迴歸範例 - Cricket Chirps
   - **專案ID**：linear-regression-example
   - **公用專案**：(已清除)
   - **建立 README.md**：(已清除)

1. 稍後，Azure Notebooks 會帶您至新的專案。

## <a name="create-the-data-file"></a>建立資料檔案

您在筆記本中建立的線性迴歸模型，會從您專案中的 cricket_chirps.csv  檔案獲取資料。 您可藉由從 [GitHub - Azure Notebooks 範例](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)複製此檔案，或直接輸入資料來建立該檔案。 下列幾段會說明這兩種方法。

### <a name="upload-the-data-file"></a>上傳資料檔案

1. 在 Azure Notebooks 中的專案儀表板上，選取 [上傳]   > [從 URL] 
1. 在快顯視窗的 [檔案 URL]  中輸入下列 URL，並於 [檔案名稱]  中輸入 cricket_chirps.csv  ，然後選取 [完成]  。

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. cricket_chirps.csv  檔案現在應該出現在您專案的檔案清單中：

    ![新建的 CSV 檔案出現在專案檔案清單中](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>從頭開始建立檔案

1. 在 Azure Notebooks 中的專案儀表板上，選取 [+ 新增]   > [空白檔案] 
1. 專案的檔案清單中會出現一個欄位。 輸入 cricket_chirps.csv  並且按 Enter。
1. 以滑鼠右鍵按一下 cricket_chirps.csv  ，然後選取 [編輯檔案]  。
1. 在顯示的編輯器中，輸入以下資料：

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. 選取 [儲存檔案]  以儲存檔案並返回專案儀表板。

## <a name="install-project-level-packages"></a>安裝專案層級套件

您隨時可以在筆記本的程式碼資料格中使用 `!pip install` 之類的命令，以安裝必要的套件。 不過，當您每次執行筆記本的程式碼資料格時，此類命令也會隨之執行，並可能需要花費相當長的時間。 因此，您可以改為使用 `requirements.txt` 檔案在專案層級安裝套件。

1. 使用[從頭建立檔案](#create-a-file-from-scratch)中的步驟流程來建立名為 `requirements.txt` 的檔案，其中包含下列內容：

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    如[上傳資料檔案](#upload-the-data-file)所述，您也可以從本機電腦上傳 `requirements.txt` 檔案。

1. 在專案儀表板上，選取 [專案設定]  。
1. 在出現的快顯視窗中，選取 [環境]  索引標籤，然後選取 [+新增]  。
1. 在 [環境設定步驟]  底下的第一個下拉式控制項 (作業) 中，選擇 **Requirements.txt**。
1. 在第二個下拉式控制項 (檔案名稱) 中，選擇 *requirements.txt* (您所建立的檔案)。
1. 在第三個下拉式控制項 (Python 版本) 中，選擇 [Python 3.6 版]  。
1. 選取 [儲存]  。

![用於指定 requirements.txt 檔案的 [專案設定環境] 索引標籤](media/tutorial/tutorial-requirements-txt.png)

透過這個安裝步驟，您在專案中執行的任何筆記本都會在已安裝這些套件的環境中執行。

## <a name="create-and-run-a-notebook"></a>建立及執行筆記本

備妥資料檔案並設定好專案環境後，即可建立並開啟筆記本。

1. 在專案儀表板上，選取 [+ 新增]   > [Notebook]  。
1. 在快顯視窗的 [項目名稱]  中輸入 [線性迴歸範例 - Cricket Chirps.ipynb]  並選擇 [Python 3.6]  作為程式語言，然後選取 [新增]  。
1. 在新的筆記本出現在檔案清單之後，選取它來啟動筆記本。 自動開啟新的瀏覽器索引標籤。
1. 因為您的環境設定中包含 requirements.txt  檔案，所以會看到「等候您的容器完成準備」的訊息。 您可以選取 [確定]  以關閉訊息並繼續在筆記本中操作；不過，在環境全部設定完成之前，您無法執行程式碼資料格。
1. 筆記本開啓Jupyter 介面時，預設有一個空白程式碼資料格。

    [![Azure Notebooks 中新筆記本的起始檢視](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>筆記本介面導覽

筆記本在執行時，您可以新增程式碼和 Markdown 資料格、執行這些資料格，並管理筆記本作業。 不過，首先您可以花幾分鐘的時間來熟悉介面。 如需閲讀完整的説明文件，請選取 [協助]   >  [筆記本協助]  功能表命令。

在視窗的頂端，您會看到下列項目：

(A) 筆記本的名稱，按一下即可編輯。
(B) 能導航至專案和專案面板的按鈕，按鈕將開啓新的瀏覽器索引標籤。
(C) 一個功能表，內含搭配筆記本運作的命令。
(D) 內含一般作業快速鍵的工具列。
(E) 包含資料格的編輯畫布。
(F) 表示筆記本是否受信任的指標 (預設值為 [不受信任]  )。
(G) 用來執行筆記本的核心以及活動指標。

[![Jupyter 介面的主要 UI 區域](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter 會提供主要 UI 元素的內建導覽。 選取 [協助]   >  [使用者介面導覽]  命令並點選快顯功能表，即可開始導覽。

功能表命令如下所示：

| 功能表 | 描述 |
| --- | --- |
| 檔案 | 用以管理筆記本檔案的命令，命令包括建立和複製筆記本、顯示列印預覽，以及使用各種格式下載筆記本。 |
| 編輯 | 用以剪下、複製和貼上資料格、尋找和取代值、管理資料格附件，以及插入圖片的一般命令。  |
| 檢視 | 控制 Jupyter UI 各部分是否顯示的命令。 |
| 插入 | 用以在目前資料格上方或下方插入新資料格的命令。 建立筆記本時會時常用到這些命令。 |
| 資料格 | 各種 [Run]  命令會以不同的組合執行一或多個資料格。 [資料格類型]  命令可在 [程式碼]  、[Markdown]  及 [原始 NBConvert]  (純文字) 的類型之間變更。 [目前輸出]  和 [所有輸出]  命令可控制如何顯示來自執行程式碼的輸出，並且包含清除所有輸出的命令。 |
| 核心 | 用以管理在核心中如何執行程式碼的命令，以及 [變更核心]  用來變更語言或用來執行筆記本的 Python 版本。 |
| 資料 | 從專案或工作階段上傳和下載檔案的命令。 請參閱[使用專案資料檔案](work-with-project-data-files.md) |
| 小工具 | 用以管理 [Jupyter Widget](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html) 的命令，其可針對視覺效果、對映及繪圖提供額外功能。|
| 説明 | 可提供 Jupyter 介面說明和文件的命令。 |

工具列上大部分的命令在功能表上都有對等的命令。 除了在[共用及展示筆記本](present-jupyter-notebooks-slideshow.md)討論過的 [輸入/編輯 RISE 投影片]  。

您接下來填入筆記本時會使用其中一些命令。

## <a name="create-a-markdown-cell"></a>建立 Markdown 資料格

1. 按一下筆記本畫布上顯示的第一個空白資料格。 資料格預設為程式碼  類型，這個類型用於寫可執行，并且適用於所選核心的程式碼 (Python、R 或 F#) 目前類型會顯示在工具列上的類型下拉式清單中：

    ![資料格類型工具列下拉式清單](media/tutorial/tutorial-cell-type-drop-down.png)

1. 使用工具列下拉式清單將資料格類型變更為 **Markdown**；或使用 [Cell]   > [Cell Type]   >  [Markdown]  功能表命令：

    ![資料格類型功能表命令](media/tutorial/tutorial-cell-type-menu.png)

1. 在資料格中按一下以開始編輯，然後輸入下列 Markdown：

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. 若要將 Markdown 轉譯成瀏覽器適用的 HTML，請選取工具列上的 [Run]  命令，或使用 [Cell]   > [Run Cells]  命令。 現在，用於格式化的 Markdown 程式碼和連結會如您所預期出現在瀏覽器中。

1. 當您在執行筆記本最後的資料格時，Jupyter 會在您執行的資料格下面自動建立新的資料格。 使用下列 Markdown 重複上述步驟，將更多 Markdown 放入此資料格中：

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. 若要再次編輯 Markdown，按兩下已轉譯的資料格。 若要在進行變更之後再次轉譯 HTML，請執行資料格。

## <a name="create-a-code-cell-with-commands"></a>使用命令建立程式碼資料格

如先前的 Markdown 資料格所述，您可以直接在筆記本中包含命令。 您可以使用命令來安裝套件、執行 curl 或 wget 來擷取資料，或任何其他動作。 Jupyter Notebooks 會在 Linux 虛擬機器中有效地執行，所以您可以使用完整的 Linux 命令集。

1. 在您於前一個 Markdown 資料格上使用[執行]  之後出現的程式碼資料格中，輸入以下命令。 如果您沒有看到新的資料格，可使用 [Insert]   > [Insert Cell Below]  ，或使用工具列上的 **+** 按鈕建立。

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. 在執行資料格之前，使用工具列上的 **+** 按鈕來建立新的資料格，將它設定為 Markdown，然後輸入以下說明：

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. 選取 [Cell]   > [Run All]  命令，以在筆記本中執行所有資料格。 請注意，Markdown 資料格會轉譯為 HTML，而命令會在核心中執行，并且核心指標會顯示如 Markdown 内容所述：

    ![筆記本核心的忙碌指標](media/tutorial/tutorial-kernel-busy.png)

1. 所有 `pip install` 命令都需要花一些時間執行，而因為您已在專案環境中安裝這些套件 (且這些套件也預設包含在 Azure Notebook 中)，所以您會看到許多訊息表示「需求已滿足」。 這麽多輸出可能擾亂視覺，因此請選取該資料格 (按一下滑鼠)，然後使用 [資料格]   > [資料格輸出]   > [切換]  來隱藏輸出。 您也可以使用同一個子功能表上的 [清除]  命令，移除所有輸出。

    [Toggle]  命令只會隱藏來自資料格的最新輸出；如果您再次執行資料格，輸出就會重新出現。

1. 因為套件已安裝於專案環境中，您可以使用 `#` 來註解排除 `! pip install` 命令；如此這些命令即可作為指示留在筆記本中，但不需花費任何時間來執行，也不會產生不必要的輸出。 在此情況下，在筆記本中保留註解的命令，也可指出筆記本的相依性。

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>建立其餘的資料格

若要填入筆記本的其餘部分，您可以接著建立一系列的 Markdown 和程式碼資料格。 對於以下所列的每個資料格，首先要做的是建立新的資料格，接著設定類型，然後貼上內容。

雖然您可以等建立好資料格之後再執行筆記本，但是在建立過程中執行會很有趣。 並非所有資料格都會顯示輸出；如果您沒有看到任何錯誤，則假設資料格已正常執行。

每個程式碼資料格會相依於已在先前資料格中執行的程式碼，如果您忘了執行其中一個資料格，則後續的資料格可能會產生錯誤。 如果您發現忘了執行資料格，請先嘗試使用 [資料格]   > [執行上列全部]  命令，再執行目前的資料格。

如果您看到非預期的結果 (這是有可能的！)，請檢查每個資料格是否已依需要設定為 [Code] 或 [Markdown]。 例如，「語法無效」錯誤通常在您在程式碼資料格中輸入 Markdown 時發生。

1. Markdown 資料格：

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. 程式碼資料格；執行時，會將資料表內容顯示為輸出。 您可藉由註解排除 `print` 敘述來隱藏輸出。

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > 您可能會看到此程式碼發出的「numpy.dtype 大小已變更」相關警告；請放心地忽略警告。

1. Markdown 資料格：

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. 程式碼資料格；執行時，此資料格沒有輸出。

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown 資料格：

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. 程式碼資料格；執行時，此資料格會顯示輸出 `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`。

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown 資料格：

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. 程式碼資料格；執行時，此資料格會顯示 `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` 之類的結果。

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown 資料格：

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown 資料格：

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. 程式碼資料格；執行時，此資料格會產生圖表繪圖。 如果您第一次沒有看到此繪圖 (而是看到「640x480大小，具有 1 個座標軸的圖」)，請再次執行資料格。

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![來自 matplotlib 程式碼的繪圖輸出](media/tutorial/tutorial-plot-output.png)

1. Markdown 資料格：

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>清除輸出並重新執行所有資料格

按照上述步驟填入整個筆記本之後，您已建立了一套可執行的程式碼，并且完成了線性迴歸的完整教學。 這種程式碼和文字的直接結合是筆記本很大的一個優點！

立即嘗試重新執行整個筆記本：

1. 選取 [Kernel]   > [Restart & Clear Output]  ，以清除所有核心的工作階段資料和所有資料格輸出。 當您完成筆記本后，執行此命令總是一個良好的習慣，這樣能確保您未在程式碼資料格之間建立任何奇怪的相依性。

1. 使用 [Cell]   > [Run All]  以重新執行筆記本。 請注意，核心指標會在執行程式碼時填入。

    如果您有任何執行太久或者卡住的程式碼，可以使用 [Kernel]   > [Interrupt]  命令來停止核心。

1. 捲動筆記本來檢查結果。 (如果繪圖再次未出現，請重新執行該資料格。)

## <a name="save-halt-and-close-the-notebook"></a>儲存、停止及關閉筆記本

在您編輯筆記本期間，可以使用 [File]   > [Save and Checkpoint]  命令或工具列上的 [儲存] 按鈕，儲存其目前狀態。 「檢查點」會建立一個快照集，您可以在工作階段期間隨時將其還原。 檢查點可讓您進行一系列的實驗性變更，如果這些變更沒有效果，只要使用 [File]   > [Revert to Checkpoint]  命令，即可還原至檢查點。 您也可以建立額外的資料格，並註解排除任何您不想要執行的程式碼；兩種方法都可用。

您也可以隨時使用 [File]   > [Make a Copy]  命令，製作筆記本的目前狀態複本，使其成為您專案中的新檔案。 該複本會在新的瀏覽器索引標籤中自動開啟。

當您處理好筆記本時，請使用 [File]   > [Close and halt]  命令，這個命令會關閉筆記本並關閉已在執行它的核心。 Azure Notebooks 接著會自動關閉瀏覽器索引標籤。

## <a name="debug-notebooks-using-visual-studio-code"></a>使用 Visual Studio Code 進行筆記本偵錯

如果筆記本中的程式碼資料格並未以您預期的方式運作，則可能因為有程式碼錯誤 (bug) 或其他缺失。 但是，除了使用 `print` 敘述來顯示變數值，Jupyter 環境通常不會提供任何偵錯工具。

幸運的是，您可以下載筆記本的 *.ipynb* 檔案，然後在 Visual Studio Code 中使用 Python 擴充功能開啟。 擴充功能會直接將筆記本匯入為單一程式碼檔案，并且在註解中保留您的 Markdown 資料格。 一旦匯入筆記本後，就可以使用 Visual Studio Code 偵錯工具逐步執行程式碼、設定中斷點、檢查狀態等。 在更正程式碼之後，您需要從 Visual Studio Code 將 *.ipynb* 檔案匯出，然後將它上傳回 Azure Notebooks。

如需詳細資訊，請參閱 Visual Studio Code 文件中的[針對 Jupyter Notebook 進行偵錯](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook)。

另請參閱 [Visual Studio Code - Jupyter 支援](https://code.visualstudio.com/docs/python/jupyter-support)以了解適用於 Jupyter Notebooks 的 Visual Studio Code。

## <a name="next-steps"></a>後續步驟

- [探索範例筆記本](azure-notebooks-samples.md)

操作說明文章：

- [建立及複製專案](create-clone-jupyter-notebooks.md)
- [設定和管理專案](configure-manage-azure-notebooks-projects.md)
- [從筆記本內安裝套件](install-packages-jupyter-notebook.md)
- [放映投影片](present-jupyter-notebooks-slideshow.md)
- [使用資料檔案](work-with-project-data-files.md)
- [存取資料資源](access-data-resources-jupyter-notebooks.md)
- [使用 Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
