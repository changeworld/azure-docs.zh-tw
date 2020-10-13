---
title: 探索 Linux
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何使用 Linux 資料科學虛擬機器完成數個常見的資料科學工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 65a627cc009699660de0897ce853acaa78381f6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275977"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Azure 中的資料科學與 Ubuntu 資料科學虛擬機器

本逐步解說示範如何使用 Ubuntu 資料科學虛擬機器 (DSVM) 完成數個常見的資料科學工作。 Ubuntu DSVM 是 Azure 中可用的虛擬機器映射，預先安裝了一組常用於資料分析和機器學習的工具。 重要的軟體元件在布建 [Ubuntu 資料科學虛擬機器](./dsvm-ubuntu-intro.md)中有詳細的規定。 DSVM 映像可讓使用者輕鬆地在幾分鐘內開始執行資料科學，而不需要個別安裝和設定每個工具。 如有需要，您可以輕鬆地相應擴大 DSVM，而且可在不使用時將其停止。 DSVM 既有彈性，又符合成本效益。

在本逐步解說中，我們會分析 [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 資料集。 Spambase 是一組標示為垃圾郵件或非垃圾郵件的電子郵件。 Spambase 也包含有關電子郵件內容的一些統計資料。 我們稍後會在逐步解說中討論統計資料。

## <a name="prerequisites"></a>Prerequisites

您必須具備下列必要條件，才能使用 Linux DSVM：

* **Azure 訂用帳戶**。 若要取得 Azure 訂用帳戶，請參閱[立即建立您的免費 Azure 帳戶](https://azure.microsoft.com/free/)。

* [**Ubuntu 資料科學虛擬機器**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。 如需布建虛擬機器的詳細資訊，請參閱布建 [Ubuntu 資料科學虛擬機器](linux-dsvm-intro.md)。
* [**X2Go**](https://wiki.x2go.org/doku.php)，已安裝在電腦上並已開啟 XFCE 工作階段。 如需詳細資訊，請參閱[安裝與設定 X2Go 用戶端](linux-dsvm-intro.md#x2go)。
* 如需更流暢的捲動體驗，請在 DSVM 的 Firefox 網頁瀏覽器中，切換 `about:config` 中的 `gfx.xrender.enabled` 旗標。 [深入了解](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 也請考慮將 `mousewheel.enable_pixel_scrolling` 設定為 `False`。 [深入了解](https://support.mozilla.org/questions/981140)。

## <a name="download-the-spambase-dataset"></a>下載 spambase 資料集

[spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 資料集是一組較小的資料，其中包含 4601 個範例。 在示範 DSVM 的某些重要功能時，此資料集的大小便於使用，因為其會讓所需的資源需求保持適中。

> [!NOTE]
> 本逐步解說是使用 D2 v2 大小的 Linux DSVM (Ubuntu 18.04 Edition) 所建立。 您可以使用此大小的 DSVM 來完成本逐步解說中所示範的程序。

如果需要更多儲存空間，您可以建立額外的磁碟，並將其連接到 DSVM。 這些磁碟會使用永續性的 Azure 儲存體，因此，即使伺服器因為調整大小或關閉等緣故而重新佈建，磁碟中的資料仍會保留下來。 若要新增磁碟並將其連接到 DSVM，請完成[新增磁碟至 Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中的步驟。 新增磁碟的步驟會使用已安裝在 DSVM 上的 Azure CLI。 您可以完全從 DSVM 本身完成步驟。 另一種增加儲存體的選項是使用 [Azure 檔案儲存體](../../storage/files/storage-how-to-use-files-linux.md)。

若要下載資料，請開啟終端機視窗，然後執行下列命令：

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

下載的檔案沒有標頭列。 讓我們建立另一個具有標頭的檔案。 執行此命令來建立具有適當標題的檔案︰

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

然後，串連這兩個檔案：

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

對於每一封電子郵件，資料集都有數種類型的統計資料：

* **word\_freq\__WORD_** 之類的資料行會指出電子郵件中符合 *WORD* 之字詞的百分比。 例如，如果 **word\_freq\_make** 是 **1**，則表示電子郵件的所有文字中有 1% 是 *make*。
* **char\_freq\__CHAR_** 之類的資料行會指出電子郵件的所有字元中 *CHAR* 所佔的百分比。
* **capital\_run\_length\_longest** 是一連串大寫字母的最長長度。
* **capital\_run\_length\_average** 是所有連串大寫字母的平均長度。
* **capital\_run\_length\_total** 是所有連串大寫字母的總長度。
*  indicates whether the email was considered  or not (1 = , 0 = not ).

## <a name="explore-the-dataset-by-using-r-open"></a>使用 R Open 來探索資料集

讓我們使用 R 來檢查資料並執行某些基本的機器學習。DSVM 已預先安裝了 [Microsoft R Open](https://mran.revolutionanalytics.com/open/)。 預先安裝的 R 版本中有多執行緒的數學程式庫，可提供比單一執行緒版本還要好的效能。 R Open 也可透過 CRAN 套件存放庫的快照來提供重現性。

若要取得本逐步解說中所使用的程式碼範例複本，請使用 git 來複製 Azure-Machine-Learning-Data-Science 存放庫。 Git 會預先安裝在 DSVM 上。 在 git 命令列，執行：

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

開啟終端機視窗，並在 R 互動式主控台中啟動新的 R 工作階段。 您也可以使用預先安裝在 DSVM 上的 RStudio。

若要匯入資料並設定環境：

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

若要查看關於每個資料行的摘要統計資料︰

```R
summary(data)
```

針對資料的不同檢視︰

```R
str(data)
```

此檢視會顯示每個變數的類型和資料集內的前幾個值。

「spam」  資料行已讀取為整數，但它實際上是類別變數 (或係數)。 若要設定其類型︰

```R
data$spam <- as.factor(data$spam)
```

若要進行一些探勘分析，請使用 [ggplot2](https://ggplot2.tidyverse.org/) 套件，這是已預先安裝在 DSVM 上的熱門圖形庫 (適用於 R)。 根據稍早顯示的摘要資料，我們擁有關於驚嘆號字元出現頻率的摘要統計資料。 在此，讓我們執行下列命令繪製這些頻率：

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

由於零軸會影響繪圖的準確性，讓我們將其消除：

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

在 1 上面有看起來很有意思的不尋常密度。 讓我們只查看該資料：

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

然後，按照垃圾郵件與非垃圾郵件進行分割：

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

這些範例應協助您進行類似的繪圖，並探索其他資料行中的資料。

## <a name="train-and-test-a-machine-learning-model"></a>定型及測試機器學習模型

讓我們定型幾個機器學習模型，將資料集內的電子郵件分類為包含垃圾郵件或非垃圾郵件。 在本節中，我們會定型決策樹模型和隨機樹系模型。 然後，我們會測試預測的正確性。

> [!NOTE]
> 下列程式碼所使用的 *RPART* (Recursive Partitioning and Regression Trees，遞迴分割和迴歸樹狀結構) 套件已安裝在 DSVM 上。

首先，讓我們將資料集分割為定型集和測試集：

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

然後，建立決策樹來分類電子郵件：

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

結果如下︰

![所建立決策樹的圖表](./media/linux-dsvm-walkthrough/decision-tree.png)

若要判斷訓練集的表現有多良好，請使用下列程式碼︰

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

若要判斷測試集的表現有多良好︰

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

讓我們同時嘗試隨機樹系模型。 隨機樹系會定型大量決策樹，並輸出屬於所有個別決策樹之分類眾數的類別。 它們能提供更強大的機器學習方法，因為其會校正決策樹模型傾向以過度擬合定型資料集。

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>深度學習教學課程和逐步解說

除了以架構為基礎的範例，我們也提供一套完整的逐步解說。 這些逐步解說有助於您在影像和文字/語言理解之類的領域中，快速開發深度學習應用程式。

- [跨不同架構執行類神經網路](https://github.com/ilkarman/DeepLearningFrameworks)：完整的逐步解說，向您示範如何將程式碼從一種架構移轉至另一種架構。 其中也示範如何跨架構來比較模型和執行階段效能。 

- [建置端對端解決方案在影像內偵測產品的操作說明指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：影像偵測是一種可在影像內找出並分類物體的技術。 這項技術在許多現實生活商業領域中潛藏龐大的商機。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [適用於音訊的深度學習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：此教學課程說明如何針對[都市音效資料集](https://urbansounddataset.weebly.com/)上的音訊事件偵測定型深度學習模型。 教學課程提供如何使用音訊資料的概觀。

- [更文字文件的分類](https://github.com/anargyri/lstm_han)：此逐步解說將示範如何建置和定型兩種不同的類神經網路架構：分層注意網路和長短期記憶 (LSTM)。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 Keras 是三個最受歡迎深度學習架構的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="other-tools"></a>其他工具

其餘各節會示範如何使用安裝在 Linux DSVM 上的部分工具。 我們會討論下列工具：

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL 和 SQuirreL SQL
* Azure Synapse Analytics (先前為 SQL DW)

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) 提供快速且正確的推進式決策樹實作。

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost 也可以從 Python 或命令列進行呼叫。

### <a name="python"></a>Python

為了開發 Python，已在 DSVM 上安裝 Anaconda Python 散發套件 3.5 與 2.7。

> [!NOTE]
> Anaconda 散發套件包含 [Conda](https://conda.pydata.org/docs/index.html)。 您可以使用 Conda，來建立已在其中安裝不同版本或套件的自訂 Python 環境。

讓我們讀取某些 spambase 資料集，並利用 Scikit-learn 中的支援向量機器分類電子郵件：

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

若要進行預測︰

```Python
clf.predict(X.ix[0:20, :])
```

為了示範如何發佈 Azure Machine Learning 端點，讓我們建立更基本的模型。 我們將使用稍早發佈 R 模型時使用的三個變數：

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>JupyterHub

DSVM 中的 Anaconda 散發套件隨附 Jupyter Notebook，此跨平台環境可用於共用 Python、R 或 Julia 程式碼和分析。 Jupyter Notebook 是透過 JupyterHub 來存取。 您可以使用本機 Linux 使用者名稱和密碼在 HTTPs:// \<DSVM DNS name or IP address\> ： 8000/登入。 JupyterHub 的所有組態檔可在 eg /etc/ jupyterhub 中找到。

> [!NOTE]
> 若要從目前核心中的 Jupyter Notebook 使用 Python 套件管理員 (透過 `pip` 命令)，請在程式碼資料格中使用下列命令：
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 若要從目前核心中的 Jupyter Notebook 使用 Conda 安裝程式 (透過 `conda` 命令)，請在程式碼資料格中使用下列命令：
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 上已安裝數個範例筆記本：

* 範例 Python 筆記本：
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* 範例 R 筆記本：
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Julia 語言也可從 Linux DSVM 上的命令列來使用。

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) 是用於資料採礦的 R 圖形化工具。 Rattle 具有直覺式介面，可讓您輕鬆地載入、瀏覽和轉換資料，以及建置和評估模型。 [Rattle：適用於 R 的資料採礦 GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) 提供了示範其功能的逐步解說。

執行下列命令來安裝和啟動 Rattle：

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> 您不需要在 DSVM 上安裝 Rattle。 不過，當 Rattle 開啟時，系統可能會提示您安裝其他套件。

Rattle 使用索引標籤式介面。 大部分索引標籤會對應至 [Team Data Science Process ](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 中的步驟，例如載入資料或探索資料。 資料科學程序會由左到右經歷所有索引標籤。 最後一個索引標籤記錄 Rattle 所執行的 R 命令。

若要載入和設定資料集︰

1. 若要載入檔案，請選取 [資料] 索引標籤。
1. 選擇 [檔案名稱] 旁的選取器，然後選取 [spambaseHeaders.data]。
1. 若要載入檔案。 選取 [執行]。 您應該會看到每個資料行的摘要，包括其識別的資料類型、其為輸入、目標還是其他類型的變數，以及唯一值數目。
1. Rattle 已將 [垃圾郵件]  資料行正確地識別為目標。 選取 [垃圾郵件] 資料行，然後將 [目標資料類型] 設定為 [類別]。

若要瀏覽資料︰

1. 選取 [瀏覽]  索引標籤。
1. 若要查看一些關於變數類型的資訊和某些摘要統計資料，請選取 [摘要] > [執行]。
1. 若要檢視關於每個變數的其他類型的統計資料，請選取其他選項，例如 [描述] 或 [基本資訊]。

您也可以使用 [探索] 索引標籤來產生更具見解的繪圖。 若要繪製資料的長條圖︰

1. 選取 [分佈] 。
1. 針對 **word_freq_remove** 和 **word_freq_you**，選取 [長條圖]。
1. 選取 [執行] 。 您應該會在單一圖形視窗中看到這兩個密度圖，其中清楚顯示 _you_ 這個字在電子郵件中的出現頻率遠高於 _remove_。

**相互關聯**圖也很有趣。 若要建立繪圖：

1. 針對 [類型]，選取 [相互關聯]。
1. 選取 [執行] 。
1. Rattle 會警告您，它建議的上限為 40 個變數。 選取 [是]  以檢視此圖。

圖中會浮現一些有趣的相互關聯：例如，_technology_ 與 _HP_ 和 _labs_ 有高度相互關聯性。 其也與 _650_ 有高度相互關聯性，因為資料集捐贈者的區碼是 650。

文字間相互關聯性的數值可在 [探索] 視窗中取得。 舉例來說，值得注意的是 _technology_ 與 _your_ 和 _money_ 負面相關。

Rattle 可以轉換資料集來處理一些常見的問題。 例如，其可調整功能大小、插補遺漏值、處理離群值，以及移除具有遺漏資料的變數或觀察值。 Rattle 也可以識別觀察值和變數之間的關聯規則。 這些索引標籤不在此入門逐步解說的討論範圍內。

Rattle 也可以執行叢集分析。 讓我們排除部分功能以讓輸出更方便閱讀。 在 [資料] 索引標籤上，選取每個變數旁的 [忽略]，但下面這 10 個項目除外：

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

返回 [叢集] 索引標籤。選取 [Kmeans]，然後將 [叢集數目] 設定為 [4]。 選取 [執行] 。 結果會顯示在輸出視窗中。 有一個叢集具有高頻率的 _george_ 和 _hp_，因此可能是合法的商業電子郵件。

若要建置基本的決策樹機器學習模型：

1. 選取 [模型]  索引標籤。
1. 針對 [類型]，選取 [樹狀]。
1. 選取 [執行]  ，在輸出視窗中以文字形式顯示樹狀結構。
1. 選取 [繪製]  按鈕以檢視圖形化版本。 此決策樹看起來類似我們稍早使用 rpart 取得的決策樹。

有用的 Rattle 功能是其能夠執行數個機器學習方法和快速評估這些方法。 步驟如下：

1. 針對 [類型]，選取 [全部]。
1. 選取 [執行] 。
1. 當 Rattle 執行完成時，您可以選取任何 [類型] 值，例如 [SVM]，並檢視結果。
1. 您也可以使用 [評估]  索引標籤比較驗證集上模型的效能。例如，[錯誤矩陣]  選取項目會顯示驗證集上每個模型的混淆矩陣、整體錯誤和平均類別錯誤。 您也可以繪製 ROC 曲線、執行敏感度分析和進行其他類型的模型評估。

建置完模型時，選取 [記錄] 索引標籤來檢視 Rattle 在工作階段期間執行的 R 程式碼。 您可以選取 [匯出]  按鈕來加以儲存。

> [!NOTE]
> 最新版 Rattle 包含一個錯誤 (bug)。 若要修改指令碼或在稍後使用該指令碼重複執行步驟，您必須在記錄文字的 *Export this log ...* 前面插入 **#** 字元。

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL 和 SQuirreL SQL

DSVM 隨附安裝 PostgreSQL。 PostgreSQL 是複雜的開放原始碼關聯式資料庫。 本節向您說明如何將 spambase 資料集載入至 PostgreSQL，然後進行查詢。

在載入資料之前，您必須先允許從 localhost 進行密碼驗證。 在命令提示字元中執行︰

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

組態檔末尾附近有幾行詳細說明允許之連線的文字︰

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

將 **IPv4 local connections** 文字行變更為使用 **md5** 而非 **ident**，以便可以使用使用者名稱和密碼來登入：

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

然後，重新啟動 PostgreSQL 服務：

```Bash
sudo systemctl restart postgresql
```

若要以內建 postgres 使用者身分啟動 *psql* (PostgreSQL 的互動終端機)，請執行下列命令：

```Bash
sudo -u postgres psql
```

使用您用來登入的 Linux 帳戶使用者名稱，建立新的使用者帳戶。 建立密碼：

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

登入 psql：

```Bash
psql
```

將資料匯入到新資料庫：

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

現在，讓我們使用 SQuirreL SQL 來探索資料並執行一些查詢，SQuirreL SQL 是一種圖形化工具，您可用來透過 JDBC 驅動程式與資料庫互動。

若要開始使用，請在 [應用程式] 功能表上，開啟 SQuirreL SQL。 若要設定驅動程式︰

1. 選取 [Windows] > [檢視驅動程式]。
1. 以滑鼠右鍵按一下 [PostgreSQL]，然後選取 [修改驅動程式]。
1. 選取 [額外類別路徑] > [新增]。
1. 針對 [檔案名稱]，輸入 **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**。
1. 選取 [開啟] 。
1. 選取 [列出驅動程式]。 針對 [類別名稱]，選取 [org.postgresql.Driver]，然後選取 [確定]。

若要設定與本機伺服器的連線︰

1. 選取 [Windows] > [檢視別名]。
1. 選取 **+** 按鈕以建立新的別名。 針對新的別名，輸入 **Spam database**。 
1. 針對 [驅動程式]，選取 [PostgreSQL]。
1. 將 URL 設定為 **jdbc:postgresql://localhost/spam**。
1. 輸入您的使用者名稱和密碼。
1. 選取 [確定]。
1. 若要開啟 [連線] 視窗，請按兩下**垃圾郵件資料庫**別名。
1. 選取 [連接]。

若要執行一些查詢︰

1. 選取 [SQL]  索引標籤。
1. 在 [SQL] 索引標籤頂端的查詢方塊中，輸入基本查詢，例如 `SELECT * from data;`。
1. 按 Ctrl+Enter 執行查詢。 依預設，SQuirreL SQL 會傳回查詢的前 100 個資料列。

還有許多可供您執行以探索此資料的查詢。 例如，「make」  一字在垃圾郵件和非垃圾郵件之間的出現頻率有何差異？

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

或者，經常包含「3d」 的電子郵件有何特性？

```SQL
SELECT * from data order by word_freq_3d desc;
```

大部分大量出現「3d」 的電子郵件顯然是垃圾郵件。 此資訊可能有助於建置預測性模型來分類電子郵件。

如果您想要使用 PostgreSQL 資料庫中儲存的資料執行機器學習，請考慮使用 [MADlib](https://madlib.incubator.apache.org/)。

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (先前為 SQL DW)

Azure Synapse Analytics 是一種以雲端為基礎的向外延展資料庫，可處理大量資料，包括關聯式和非關聯式。 如需詳細資訊，請參閱 [什麼是 Azure Synapse Analytics？](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

若要連線到資料倉儲並建立資料表，請從命令提示字元執行下列命令︰

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

在 sqlcmd 提示字元中，執行下列命令：

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

使用 bcp 複製資料：

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> 下載的檔案包含 Windows 樣式行尾結束符號。 Bcp 工具需要 Unix 樣式行尾結束符號。 使用-r 旗標來告訴 bcp。

然後，使用 sqlcmd 進行查詢：

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

您也可以使用 SQuirreL SQL 進行查詢。 使用 SQL Server JDBC 驅動程式，遵循類似於 PostgreSQL 的步驟。 JDBC 驅動程式位於 /usr/share/java/jdbcdrivers/sqljdbc42.jar 資料夾中。


