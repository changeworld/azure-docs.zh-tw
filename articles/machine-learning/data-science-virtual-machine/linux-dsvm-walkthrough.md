---
title: 探索 Linux
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何使用 Linux 資料科學虛擬機器完成數個常見的資料科學工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 38088503fee016651a8c1c9a1f57ad4bbe102456
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257142"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>在 Azure 中使用 Linux 資料科學虛擬機器的資料科學

本逐步解說將示範如何使用 Linux 資料科學虛擬機器（DSVM）來完成數個常見的資料科學工作。 Linux DSVM 是 Azure 中可用的虛擬機器映射，預先安裝了一組常用於資料分析和機器學習服務的工具。 重要的軟體元件會在布建[Linux 資料科學虛擬機器](linux-dsvm-intro.md)中進行詳細的配置。 DSVM 映射可讓您在短短幾分鐘內輕鬆開始進行資料科學，而不需要個別安裝和設定每個工具。 如有需要，您可以輕鬆地相應增加 DSVM，而您可以在不使用時將其停止。 DSVM 資源既彈性又符合成本效益。

本逐步解說所示範的資料科學工作[會遵循什麼是 Team 資料科學程式？](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)中所述的步驟進行。 Team 資料科學程式是一種資料科學的系統化方法，可協助資料科學家的小組有效率地在建立智慧型應用程式的生命週期中共同作業。 資料科學程序也為資料科學提供了可反覆進行的架構供個人遵循。

在此逐步解說中，我們會分析[spambase](https://archive.ics.uci.edu/ml/datasets/spambase)資料集。 Spambase 是一組標示為垃圾郵件或 ham （非垃圾郵件）的電子郵件。 Spambase 也包含有關電子郵件內容的一些統計資料。 我們稍後會在本逐步解說中討論統計資料。

## <a name="prerequisites"></a>先決條件

您必須具備下列必要條件，才能使用 Linux DSVM：

* **Azure 訂用帳戶**。 若要取得 Azure 訂用帳戶，請[立即建立免費的 azure 帳戶](https://azure.microsoft.com/free/)。
* [**Linux 資料科學虛擬機器**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。 如需布建虛擬機器的相關資訊，請參閱布建[Linux 資料科學虛擬機器](linux-dsvm-intro.md)。
* [**X2Go**](https://wiki.x2go.org/doku.php)已安裝在您的電腦上，並具有開啟的 XFCE 會話。 如需詳細資訊，請參閱[安裝和設定 X2Go 用戶端](linux-dsvm-intro.md#x2go)。
* 若要取得更流暢的滾動體驗，請在 DSVM 的 Firefox 網頁流覽`gfx.xrender.enabled`器中`about:config`，切換中的旗標。 [深入了解](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 也請考慮`mousewheel.enable_pixel_scrolling`將`False`設定為。 [深入了解](https://support.mozilla.org/questions/981140)。
* **Azure Machine Learning 帳戶**。 如果您還沒有帳戶，請在[Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//)] 首頁上註冊新的帳戶。

## <a name="download-the-spambase-dataset"></a>下載 spambase 資料集

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase)資料集是一組相對較小的資料，其中包含4601範例。 資料集是一個方便的大小，用來示範 DSVM 的一些主要功能，因為它會將資源需求保持在適當的程度。

> [!NOTE]
> 本逐步解說是使用 D2 v2 大小的 Linux DSVM （Ubuntu 18.04 Edition）所建立。 您可以使用 DSVM 此大小來完成本逐步解說中所示範的程式。

如果您需要更多儲存空間，您可以建立額外的磁片，並將它們附加到您的 DSVM。 磁片會使用持續性的 Azure 儲存體，因此即使伺服器因為調整大小或已關閉而重新布建，仍會保留其資料。 若要新增磁片並將它連結到您的 DSVM，請完成[將磁片新增至 LINUX VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的步驟。 新增磁片的步驟會使用已安裝在 DSVM 上的 Azure CLI。 您可以完全從 DSVM 本身完成步驟。 增加儲存體的另一個選項是使用[Azure 檔案儲存體](../../storage/files/storage-how-to-use-files-linux.md)。

若要下載資料，請開啟終端機視窗，然後執行此命令：

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

下載的檔案沒有標頭資料列。 讓我們建立另一個具有標頭的檔案。 執行此命令來建立具有適當標題的檔案︰

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

然後，將這兩個檔案串連在一起：

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

此資料集具有每個電子郵件的數種類型的統計資料：

* 像是**文字\_頻率\_單字**的資料行，表示電子郵件中符合*單字*的單字百分比。 例如，如果**文字\_\_頻率**為**1**，則會在電子郵件中輸入1% 的所有文字 *。*
* 如**char\_\_頻率字元**的資料行，表示電子郵件中所有字元的百分比，也就是*char*。
* **capital\_run\_length\_longest** 是一連串大寫字母的最長長度。
* **capital\_run\_length\_average** 是所有連串大寫字母的平均長度。
* **capital\_run\_length\_total** 是所有連串大寫字母的總長度。
* **** indicates whether the email was considered  or not (1 = , 0 = not ).

## <a name="explore-the-dataset-by-using-r-open"></a>使用 R Open 來探索資料集

讓我們使用 R 來檢查資料並執行一些基本的機器學習。DSVM 隨附于已預先安裝的[Microsoft R Open](https://mran.revolutionanalytics.com/open/) 。 預先安裝之 R 版本中的多執行緒數學程式庫提供比單一執行緒版本更好的效能。 R Open 也會透過 CRAN 封裝存放庫的快照集提供重現性。

若要取得本逐步解說中使用的程式碼範例複本，請使用 git 來複製 Azure 機器學習資料科學存放庫。 Git 會預先安裝在 DSVM 上。 在 git 命令列中，執行：

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

開啟終端機視窗，並在 R 互動主控台中啟動新的 R 會話。 您也可以使用預先安裝在 DSVM 上的 RStudio。

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

此視圖會顯示每個變數的類型，以及資料集中的前幾個值。

「spam」 **** 資料行已讀取為整數，但它實際上是類別變數 (或係數)。 若要設定其類型︰

```R
data$spam <- as.factor(data$spam)
```

若要進行一些探索分析，請使用[ggplot2](https://ggplot2.tidyverse.org/)套件，這是預先安裝在 DSVM 上的 R 常用圖形程式庫。 根據稍早所顯示的摘要資料，我們會針對驚嘆號字元的頻率提供摘要統計資料。 藉由執行下列命令，讓我們在此繪製這些頻率：

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

因為零長條會扭曲繪圖，所以讓我們將它排除：

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

在1以上的重要密度看起來很有趣。 讓我們來看一下該資料：

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

然後，依垃圾郵件與 ham 分割：

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

這些範例應協助您進行類似的繪圖，並流覽其他資料行中的資料。

## <a name="train-and-test-a-machine-learning-model"></a>定型和測試機器學習模型

讓我們將幾個機器學習模型定型，將資料集中的電子郵件分類為包含垃圾郵件或 ham。 在本節中，我們會訓練決策樹模型和隨機樹系模型。 然後，我們會測試預測的精確度。

> [!NOTE]
> 下列程式碼中所使用的*rpart* （遞迴資料分割和迴歸樹）封裝已安裝在 DSVM 上。

首先，讓我們將資料集分割成定型集和測試集：

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

![建立之決策樹的圖表](./media/linux-dsvm-walkthrough/decision-tree.png)

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

讓我們同時嘗試隨機樹系模型。 隨機樹系會定型許多決策樹，並輸出類別，這是來自所有個別決策樹的分類模式。 它們提供更強大的機器學習服務方法，因為它們會針對決策樹模型過度學習訓練資料集的趨勢進行修正。

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

除了以架構為基礎的範例之外，也提供一組完整的逐步解說。 這些逐步解說有助於您在影像和文字/語言理解之類的領域中，快速開發深度學習應用程式。

- [跨不同](https://github.com/ilkarman/DeepLearningFrameworks)的架構執行類神經網路：說明如何將程式碼從一個架構遷移至另一個 framework 的完整逐步解說。 它也會示範如何比較架構之間的模型和執行時間效能。 

- [建立端對端解決方案以在影像中偵測產品](https://github.com/Azure/cortana-intelligence-product-detection-from-images)的操作指南：影像偵測是一種可在影像內找出和分類物件的技術。 這項技術可能會在許多真實生活的商業領域中帶來巨大的報酬。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [適用于音訊的深度學習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：本教學課程示範如何在「市內音效」[資料集](https://urbansounddataset.weebly.com/)上訓練深入學習模型以進行音訊事件偵測。 本教學課程提供如何使用音訊資料的總覽。

- [文字檔分類](https://github.com/anargyri/lstm_han)：本逐步解說示範如何建立和定型兩個不同的類神經網路架構：階層式注意網路和長短期記憶體（LSTM）。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 Keras 是三個最受歡迎深度學習架構的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="other-tools"></a>其他工具

其餘各節會示範如何使用安裝在 Linux DSVM 上的部分工具。 我們會討論這些工具：

* XGBoost
* Python
* JupyterHub
* Rattle
* 于 postgresql 和 SQuirreL SQL
* SQL Server 資料倉儲

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/)提供快速且正確的推進式樹狀結構。

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

XGBoost 也可以從 Python 或命令列呼叫。

### <a name="python"></a>Python

針對 Python 開發，Anaconda Python 散發3.5 和2.7 會安裝在 DSVM 上。

> [!NOTE]
> Anaconda 散發套件包含[Conda](https://conda.pydata.org/docs/index.html)。 您可以使用 Conda 來建立已安裝不同版本或套件的自訂 Python 環境。

讓我們閱讀一些 spambase 資料集，並在 Scikit-learn 中使用支援向量機器來分類電子郵件-瞭解：

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

為了示範如何發佈 Azure Machine Learning 端點，讓我們建立更基本的模型。 我們將使用我們稍早發佈 R 模型時使用的三個變數：

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

若要將模型發行至 Azure Machine Learning：

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> 此選項僅適用于 Python 2.7。 Python 3.5 尚不支援。 若要執行，請使用 **/anaconda/bin/python2.7 來執行**。

### <a name="jupyterhub"></a>JupyterHub

DSVM 中的 Anaconda 散發隨附 Jupyter Notebook，這是一種跨平臺環境，可用於共用 Python、R 或 Julia 程式碼和分析。 Jupyter Notebook 是透過 JupyterHub 來存取。 您可以在 HTTPs://\<DSVM DNS 名稱或 IP 位址\>： 8000/使用本機 Linux 使用者名稱和密碼登入。 JupyterHub 的所有設定檔都可以在/etc/jupyterhub. 中找到

> [!NOTE]
> 若要從目前核心中的 Jupyter Notebook 使用`pip` Python 套件管理員（透過命令），請在程式碼單元中使用此命令：
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 若要從目前核心中的 Jupyter Notebook `conda`使用 Conda 安裝程式（透過命令），請在程式碼資料格中使用此命令：
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 上已安裝數個範例筆記本：

* Python 筆記本範例：
  * [Introtojupyterpython.ipynb. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 範例 R 筆記本：
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> 您也可以從 Linux DSVM 上的命令列取得 Julia 語言。

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) （*r* *A*nalytical *t*ool *t*o *L*贏得*E*asily）是用於資料採礦的圖形化 R 工具。 Rattle 具有直覺的介面，可讓您輕鬆地載入、流覽和轉換資料，以及建立和評估模型。 [Rattle：適用于 R 的資料採礦 GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)提供了示範 Rattle 功能的逐步解說。

執行下列命令來安裝和啟動 Rattle：

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> 您不需要在 DSVM 上安裝 Rattle。 不過，當 Rattle 開啟時，系統可能會提示您安裝其他套件。

Rattle 使用索引標籤式介面。 大部分的索引標籤會對應至[Team 資料科學](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)程式中的步驟，例如載入資料或流覽資料。 資料科學程序會由左到右經歷所有索引標籤。 最後一個索引標籤包含 Rattle 所執行 R 命令的記錄檔。

若要載入和設定資料集︰

1. 若要載入檔案，請選取 [**資料**] 索引標籤。
1. 選擇 [**檔案名**] 旁的選取器，然後選取 [ **spambaseheaders.data**]。
1. 若要載入檔案。 選取 [執行]  。 您應該會看到每個資料行的摘要，包括其識別的資料類型;它是輸入、目標或其他類型的變數，和唯一值的數目。
1. Rattle 已將 [垃圾郵件] **** 資料行正確地識別為目標。 選取 [**垃圾郵件**] 資料行，然後將 [**目標資料類型**] 設定為 **[類別]**。

若要瀏覽資料︰

1. 選取 [瀏覽] **** 索引標籤。
1. 若要查看變數類型和某些摘要統計資料的相關資訊，請選取 [**摘要** > ] [**執行**]。
1. 若要查看有關每個變數的其他類型統計資料，請選取其他選項，例如 [**描述**] 或 [**基本**]。

您也可以使用 [**流覽**] 索引標籤來產生更具洞察力的繪圖。 若要繪製資料的長條圖︰

1. 選取 [分佈] ****。
1. 針對**word_freq_remove**和**word_freq_you**，請選取 [**長條圖**]。
1. 選取 [**執行**]。 您應該會在單一圖形視窗中看到這兩個密度圖，這表示_您_在電子郵件中顯示的文字比 [_移除_] 更為頻繁。

相互**關聯**繪圖也很有趣。 若要建立繪圖：

1. 針對 [**類型**]，選取 [相互**關聯**]。
1. 選取 [**執行**]。
1. Rattle 會警告您，它建議的上限為 40 個變數。 選取 [是] **** 以檢視此圖。

有一些有趣的相互關聯，例如，_技術_與_HP_和_實驗室_有強烈的相互關聯。 它也與_650_有強烈的相互關聯，因為資料集捐贈者的區碼為650。

在 [**流覽**] 視窗中可取得單字之間相互關聯的數值。 請特別注意，這項_技術_與_您_的和_money_有負面的相互關聯。

Rattle 可以轉換資料集來處理一些常見的問題。 例如，它可以重新調整功能、插補遺漏值、處理極端值，以及移除含有遺漏資料的變數或觀察。 Rattle 也可以識別觀察和變數之間的關聯規則。 本入門逐步解說中不涵蓋這些索引標籤。

Rattle 也可以執行叢集分析。 讓我們排除部分功能以讓輸出更方便閱讀。 在 [**資料**] 索引標籤上，選取每個變數旁的 [**略**過]，但這10個專案除外：

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

返回 **[叢集**] 索引標籤。選取 [ **kmeans]**]，然後將 [叢集**數目**] 設定為**4**。 選取 [**執行**]。 結果會顯示在輸出視窗中。 一個叢集的_george_和_hp_頻率很高，而且可能是合法的商務電子郵件。

若要建立基本的決策樹機器學習模型：

1. 選取 [**模型**] 索引標籤，
1. 針對 [**類型**]，選取 [**樹狀**]。
1. 選取 [執行] **** ，在輸出視窗中以文字形式顯示樹狀結構。
1. 選取 [繪製] **** 按鈕以檢視圖形化版本。 決策樹看起來類似我們先前使用 rpart 所取得的樹狀結構。

Rattle 有一個很有用的功能，就是能夠執行數個機器學習方法並快速進行評估。 以下是步驟：

1. 針對 [**類型**]，選取 [**全部**]。
1. 選取 [**執行**]。
1. 當 Rattle 執行完成時，您可以選取任何**類型**值（例如**SVM**），並查看結果。
1. 您也可以使用 [**評估**] 索引標籤比較驗證集上模型的效能。例如，[**錯誤矩陣**] 選取專案會顯示驗證集上每個模型的混淆矩陣、整體錯誤和平均類別錯誤。 您也可以繪製 ROC 曲線、執行敏感度分析，以及執行其他類型的模型評估。

當您完成建立模型時，請選取 [**記錄**] 索引標籤，以查看 Rattle 在會話期間執行的 R 程式碼。 您可以選取 [匯出] **** 按鈕來加以儲存。

> [!NOTE]
> 目前的 Rattle 版本包含 bug。 若要修改腳本或使用它在稍後重複執行步驟，您必須在記錄檔**#** 文字中的 [*匯出此記錄檔 ...* ] 前面插入一個字元。

### <a name="postgresql-and-squirrel-sql"></a>于 postgresql 和 SQuirreL SQL

DSVM 隨附安裝 PostgreSQL。 PostgreSQL 是複雜的開放原始碼關聯式資料庫。 本節說明如何將 spambase 資料集載入于 postgresql 中，然後進行查詢。

在您可以載入資料之前，必須先允許來自 localhost 的密碼驗證。 在命令提示字元中執行︰

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

將 [ **IPv4 本機**連線] 行變更為 [使用**md5** ] 而不是 [ **ident**]，讓我們可以使用使用者名稱和密碼進行登入：

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

然後，重新開機于 postgresql 服務：

```Bash
sudo systemctl restart postgresql
```

若要啟動*psql* （于 postgresql 的互動式終端機）做為內建 postgres 使用者，請執行此命令：

```Bash
sudo -u postgres psql
```

使用您用來登入之 Linux 帳戶的使用者名稱，建立新的使用者帳戶。 建立密碼：

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

將資料匯入至新的資料庫：

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

現在，讓我們使用 SQuirreL SQL 來流覽資料並執行一些查詢，這是一種圖形化工具，可用來透過 JDBC 驅動程式與資料庫互動。

若要開始使用，請在 [**應用程式**] 功能表上，開啟 [SQuirreL SQL]。 若要設定驅動程式︰

1. 選取 [ **Windows** > **View 驅動程式**]。
1. 以滑鼠右鍵按一下 [**于 postgresql** ]，然後選取 [**修改驅動程式**]。
1. 選取 [**額外類別路徑** > ] [**新增**]。
1. 針對 [**檔案名**]，輸入 **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar 做**。
1. 選取 [開啟]****。
1. 選取 [**列出驅動程式**]。 在 [**類別名稱**] 中，選取 [**于 postgresql**]，然後選取 **[確定]**。

若要設定與本機伺服器的連線︰

1. 選取 [ **Windows** > **View 別名]。**
1. 選取 [ **+** ] 按鈕以建立新的別名。 針對新的別名名稱，輸入**垃圾郵件資料庫**。 
1. 針對 [**驅動程式**]，選取 [**于 postgresql**]。
1. 將 URL 設定為 **jdbc:postgresql://localhost/spam**。
1. 輸入您的使用者名稱和密碼。
1. 選取 [確定]  。
1. 若要開啟 [連線]**** 視窗，請按兩下**垃圾郵件資料庫**別名。
1. 選取 [連線]  。

若要執行一些查詢︰

1. 選取 [SQL] **** 索引標籤。
1. 在 [ **SQL** ] 索引標籤頂端的 [查詢] 方塊中，輸入基本查詢`SELECT * from data;`，例如。
1. 按 Ctrl + Enter 執行查詢。 根據預設，SQuirreL SQL 會從您的查詢傳回前100個數據列。

還有許多查詢可供您執行，以探索此資料。 例如，「make」 ** 一字在垃圾郵件和非垃圾郵件之間的出現頻率有何差異？

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

或者，經常包含*3d*的電子郵件有何特性？

```SQL
SELECT * from data order by word_freq_3d desc;
```

明顯出現*3d*的大部分電子郵件都是垃圾郵件。 這類資訊可能適用于建立預測性模型來分類電子郵件。

如果您想要使用儲存在於 postgresql 資料庫中的資料進行機器學習，請考慮使用[MADlib](https://madlib.incubator.apache.org/)。

### <a name="sql-data-warehouse"></a>SQL 資料倉儲

Azure SQL 資料倉儲是以雲端為基礎的向外延展資料庫，可以處理大量的資料，包括關聯式和非關聯式。 如需詳細資訊，請參閱 [什麼是 Azure SQL 資料倉儲？](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

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

您也可以使用 SQuirreL SQL 進行查詢。 使用 SQL Server JDBC 驅動程式，遵循類似于 postgresql 的步驟。 JDBC 驅動程式位於/usr/share/java/jdbcdrivers/sqljdbc42.jar 來進行資料夾中。

## <a name="next-steps"></a>後續步驟

如需引導您完成在 Azure 中構成資料科學程式之工作的文章總覽，請參閱[Team 資料科學流程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)。

如需示範 Team Data 科學程式中特定案例之步驟的端對端逐步解說的說明，請參閱[Team 資料科學流程](../team-data-science-process/walkthroughs.md)逐步解說。 這些逐步解說也示範如何將雲端和內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。
