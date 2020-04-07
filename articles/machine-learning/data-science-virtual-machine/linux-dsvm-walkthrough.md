---
title: 探索 Linux
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何使用 Linux 數據科學虛擬機完成幾個常見的數據科學任務。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755138"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Azure 中使用 Linux 資料科學虛擬機器的數據科學

本演練介紹如何使用 Linux 數據科學虛擬機器 (DSVM) 完成幾個常見的數據科學任務。 Linux DSVM 是 Azure 中可用的虛擬機器映像,它預裝了通常用於數據分析和機器學習的工具集合。 關鍵軟體元件在[「設定Linux數據科學虛擬機器](linux-dsvm-intro.md)」中進行了逐項列出。 DSVM 映像便於在幾分鐘內開始執行數據科學,而無需單獨安裝和配置每個工具。 如果需要,可以輕鬆地擴展 DSVM,並且可以在不使用時停止它。 DSVM 資源具有彈性和成本效益。

本演練中演示的數據科學任務遵循團隊[數據科學流程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)中概述的步驟? 團隊數據科學流程是一種系統的數據科學方法,可幫助數據科學家團隊在構建智慧應用程式的生命週期內有效協作。 資料科學程序也為資料科學提供了可反覆進行的架構供個人遵循。

在本演練中,我們將分析[垃圾郵件庫](https://archive.ics.uci.edu/ml/datasets/spambase)數據集。 垃圾郵件庫是一組標記為垃圾郵件或火腿(不是垃圾郵件)的電子郵件。 垃圾郵件庫還包含有關電子郵件內容的一些統計資訊。 我們將在後面的演練中討論統計數據。

## <a name="prerequisites"></a>Prerequisites

在使用 Linux DSVM 之前,必須具備以下先決條件:

* **Azure 訂閱**。 要取得 Azure 訂閱,請參閱[今天建立免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [**Linux資料科學虛擬機器**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 有關預先介面的虛擬機器的資訊,請參閱預先用 Linux[資料科學虛擬機器](linux-dsvm-intro.md)。
* [**X2Go**](https://wiki.x2go.org/doku.php)安裝在您的電腦上,並帶有打開的 XFCE 工作階段。 有關詳細資訊,請參閱[安裝和設定 X2Go 用戶端](linux-dsvm-intro.md#x2go)。
* 為了更流暢的滾動體驗,在 DSVM 的 Firefox`gfx.xrender.enabled`Web`about:config`瀏覽器中 ,在 中切換標誌。 [深入了解](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 還要考慮設定為`mousewheel.enable_pixel_scrolling``False`。 [深入了解](https://support.mozilla.org/questions/981140)。
* **Azure 機器學習帳戶**。 如果還沒有,請在[Azure 機器學習主頁](https://azure.microsoft.com/free/services/machine-learning//)上註冊新帳戶。

## <a name="download-the-spambase-dataset"></a>下載 spambase 資料集

[垃圾郵件庫](https://archive.ics.uci.edu/ml/datasets/spambase)數據集是一組相對較小的數據,包含 4,601 個示例。 數據集是演示 DSVM 的一些關鍵功能的方便大小,因為它使資源需求保持適度。

> [!NOTE]
> 本演練是使用 D2 v2 大小 Linux DSVM(Ubuntu 18.04 版本)創建的。 您可以使用此大小的 DSVM 完成本演練中演示的過程。

如果需要更多儲存空間,可以創建其他磁碟並將其附加到 DSVM。 磁碟使用持久 Azure 儲存,因此即使伺服器由於調整大小或關閉而重新預配,也會保留其數據。 要添加磁碟並將其附加到 DSVM,請完成將[磁碟添加到 Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的步驟。 添加磁碟的步驟使用 Azure CLI,Azure CLI 已在 DSVM 上安裝。 您可以完全從 DSVM 本身完成這些步驟。 增加儲存的另一個選項是使用[Azure 檔案](../../storage/files/storage-how-to-use-files-linux.md)。

要下載資料,請開啟終端機視窗,然後執行以下命令:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

下載的檔沒有標題行。 讓我們創建另一個具有標頭的檔。 執行此命令來建立具有適當標題的檔案︰

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

然後,將兩個檔案串聯在一起:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

資料集對每封電子郵件具有多種類型的統計資訊:

* 像**單\_詞\_freq WORD**這樣的列指示電子郵件中與*WORD*匹配的單詞的百分比。 例如,如果**freq\_\_make 單字**為**1**,則電子郵件中所有單詞的 1%*都已製作*。
* 像**\_char\_freq CHAR**這樣的列表示電子郵件中所有字元的*百*分比。
* **capital\_run\_length\_longest** 是一連串大寫字母的最長長度。
* **capital\_run\_length\_average** 是所有連串大寫字母的平均長度。
* **capital\_run\_length\_total** 是所有連串大寫字母的總長度。
* **** indicates whether the email was considered  or not (1 = , 0 = not ).

## <a name="explore-the-dataset-by-using-r-open"></a>使用 R Open 瀏覽資料集

讓我們檢查數據,並使用 R 進行一些基本的機器學習。DSVM 附帶[微軟 R Open](https://mran.revolutionanalytics.com/open/)預安裝。 預安裝版本的 R 中的多線程數學庫比單線程版本具有更好的性能。 R Open 還透過 CRAN 包儲存庫的快照提供可重現性。

要獲取本演練中使用的代碼示例的副本,請使用 git 克隆 Azure-機器學習-數據科學存儲庫。 Git 預置在 DSVM 上。 在 git 命令列處,執行:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

開啟終端視窗,在 R 交互控制台中啟動新的 R 作業階段。 您還可以使用預安裝在 DSVM 上的 RStudio。

要匯入資料與設定環境,可以:

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

此檢視顯示每個變數的類型和數據集中的前幾個值。

「spam」 **** 資料行已讀取為整數，但它實際上是類別變數 (或係數)。 若要設定其類型︰

```R
data$spam <- as.factor(data$spam)
```

要進行一些探索性分析,請使用[ggplot2](https://ggplot2.tidyverse.org/)包,這是一個流行的 R 圖形庫,預安裝在 DSVM 上。 根據前面顯示的匯總數據,我們有感嘆號字元頻率的匯總統計資訊。 讓我們透過執行以下指令來繪製這些頻率:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

由於零條是傾斜繪圖,因此讓我們消除它:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

有一個非平凡的密度高於1,看起來很有趣。 我們只看這些資料:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

然後,通過垃圾郵件與火腿拆分:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

這些範例將説明您製作類似的繪圖並流覽其他列中的數據。

## <a name="train-and-test-a-machine-learning-model"></a>訓練和測試機器學習模型

讓我們訓練幾個機器學習模型,將數據集中的電子郵件分類為包含垃圾郵件或火腿。 在本節中,我們訓練決策樹模型和隨機林模型。 然後,我們測試預測的準確性。

> [!NOTE]
> 以下代碼中使用的*rpart(* 遞迴分區和回歸樹)包已在 DSVM 上安裝。

首先,我們將數據集拆分為訓練集和測試集:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

然後,建立一個決策樹來對電子郵件進行分類:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

結果如下︰

![建立的決策樹圖](./media/linux-dsvm-walkthrough/decision-tree.png)

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

讓我們同時嘗試隨機樹系模型。 隨機林訓練大量決策樹,並輸出一個類,該類是所有單個決策樹的分類模式。 它們提供了更強大的機器學習方法,因為它們糾正了決策樹模型過度擬合訓練數據集的傾向。

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

## <a name="deep-learning-tutorials-and-walkthroughs"></a>深度學習教學和演練

除了基於框架的示例外,還提供一組全面的演練。 這些逐步解說有助於您在影像和文字/語言理解之類的領域中，快速開發深度學習應用程式。

- [跨不同框架運行神經網路](https://github.com/ilkarman/DeepLearningFrameworks):一個全面的演練,演示如何將代碼從一個框架遷移到另一個框架。 它還演示如何比較跨框架的模型和運行時性能。 

- 構建端到端[解決方案以檢測圖像中的產品的方法指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images):圖像檢測是一種可以定位圖像中的物件並進行分類的技術。 該技術有可能在許多現實生活中的業務領域帶來巨大的回報。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [音訊深度學習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/):本教程演示如何在城市[聲音數據集](https://urbansounddataset.weebly.com/)上訓練音訊事件檢測的深層學習模型。 本教程概述了如何使用音頻數據。

- [文本文件分類](https://github.com/anargyri/lstm_han):本演練演示如何構建和訓練兩種不同的神經網路體系結構:分層關注網路和長期短期內存 (LSTM)。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 Keras 是三個最受歡迎深度學習架構的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="other-tools"></a>其他工具

其餘部分將介紹如何使用 Linux DSVM 上安裝的一些工具。 我們討論這些工具:

* XGBoost
* Python
* 朱彼特Hub
* Rattle
* 後SQL和SQuirreL SQL
* SQL Server 資料倉儲

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/)提供了快速、準確的提升樹實現。

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

XGBoost 也可以從 Python 或命令列調用。

### <a name="python"></a>Python

對於 Python 開發,Anaconda Python 發行版 3.5 和 2.7 安裝在 DSVM 上。

> [!NOTE]
> 阿納康達的分佈包括[康達](https://conda.pydata.org/docs/index.html)。 您可以使用 Conda 建立自訂 Python 環境,其中安裝了不同的版本或套件。

讓我們在一些垃圾郵件庫資料集中閱讀,並在 Scikit 學習中使用支援向量電腦對電子郵件進行分類:

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

要演示如何發佈 Azure 機器學習終結點,讓我們創建一個更基本的模型。 我們將使用之前發佈 R 模型時使用的三個變數:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

要將模型發佈到 Azure 機器學習:

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
> 此選項僅適用於 Python 2.7。 Python 3.5 上尚不支援它。 要執行,請使用 **/anaconda/bin/python2.7**。

### <a name="jupyterhub"></a>朱彼特Hub

DSVM 中的 Anaconda 發行版附帶了 Jupyter 筆記本,這是一個用於共用 Python、R 或 Julia 代碼和分析的跨平台環境。 朱比特筆記本可通過朱比特Hub訪問。 您可以使用本地 Linux 使用者名稱和密碼\<在 https:// DSVM DNS 名稱或 IP 位址\>:8000/登錄。 JupyterHub 的所有配置檔都在 /etc/jupyterhub 中找到。

> [!NOTE]
> 要使用目前內核中的 Jupyter`pip`筆記本中的 Python 套件管理員(透過命令),請在此代碼單元中使用此指令:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 要使用目前內核中的 Jupyter`conda`筆記本中的 Conda 安裝程式(透過該命令),請在此代碼單元中使用此指令:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 上已安裝了多個範例筆記本:

* Python 筆記本範例:
  * [簡介 朱比特Python.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [虹膜分類PyML網路服務](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 範例 R 筆記本:
  * [簡介教程R](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> 裘莉婭語言也可從 Linux DSVM 的命令行中獲取。

### <a name="rattle"></a>Rattle

[Rattle(R](https://cran.r-project.org/web/packages/rattle/index.html) *R* *A*nalyttt *T* *T*o *L*獲得*E*等利)是一個圖形 R 工具的數據挖掘。 Rattle 具有直觀的介面,可輕鬆載入、瀏覽和轉換資料,並生成和評估模型。 [Rattle:R 的數據挖掘 GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)提供了演示拉特功能的演練。

以執行以下指令安裝並啟動 Rattle:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> 您無需在 DSVM 上安裝 Rattle。 但是,當 Rattle 打開時,系統可能會提示您安裝其他包。

Rattle 使用索引標籤式介面。 大多數選項卡對應於[團隊數據科學流程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)中的步驟,如載入數據或流覽數據。 資料科學程序會由左到右經歷所有索引標籤。 最後一個選項卡包含由 Rattle 執行的 R 命令的紀錄。

若要載入和設定資料集︰

1. 要載入檔案,請選擇 **「資料**」選項卡。
1. 選擇**Filename**旁邊的選擇器,然後選擇**垃圾郵件基礎標題.data**。
1. 若要載入檔案。 選取 [執行]  。 您應該看到每列的摘要,包括其標識的數據類型;無論是輸入、目標還是其他類型的變數;和唯一值的數量。
1. Rattle 已將 [垃圾郵件] **** 資料行正確地識別為目標。 選擇**垃圾郵件**列,然後將**目標數據類型**設置為 **"分類"。**

若要瀏覽資料︰

1. 選取 [瀏覽] **** 索引標籤。
1. 要查看有關變數類型和一些摘要統計資訊的資訊,請選擇 **「摘要** > **執行**」。
1. 要查看有關每個變數的其他類型的統計資訊,請選擇其他選項,如 **「描述」** 或 **「基本」。**

您還可以使用 **「瀏覽」** 選項卡生成有見地的繪圖。 若要繪製資料的長條圖︰

1. 選取 [分佈] ****。
1. 對於**word_freq_remove**與**word_freq_you,** 選擇**直方圖**。
1. 選擇 **"執行**" 您應該在單個圖形視窗中看到兩個密度圖,其中很明顯,_在_電子郵件中顯示的單詞比_刪除_時更頻繁地顯示。

**關聯**圖也很有趣。 要建立繪圖:

1. 對於**類型**,選擇 **"關聯**"。
1. 選擇 **"執行**"
1. Rattle 會警告您，它建議的上限為 40 個變數。 選取 [是] **** 以檢視此圖。

有一些有趣的關聯出現:例如 _,技術_與_惠普_和_實驗室_密切相關。 它還與_650_密切相關,因為數據集捐贈者的區號為 650。

單詞之間相關性的數值在 **「瀏覽」** 視窗中可用。 例如,有趣的是,_技術__與你和你的__錢_是負相關的。

Rattle 可以轉換資料集來處理一些常見的問題。 例如,它可以重新縮放要素、估計缺失值、處理異常值以及刪除缺少數據的變數或觀測值。 拉特還可以識別觀測值和變數之間的關聯規則。 本介紹性演練中未介紹這些選項卡。

拉特還可以運行群集分析。 讓我們排除部分功能以讓輸出更方便閱讀。 在 **「資料**」選項卡上,選擇**忽略**每個變數旁邊的變數,這 10 個專案除外:

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

傳回**為 「群集」** 選項卡。選擇**K 表示**,然後將**叢集數**設定為**4**。 選擇 **"執行**" 結果會顯示在輸出視窗中。 一個集群有高頻率_的喬治_和_hp,_ 可能是合法的商業電子郵件。

要構建基本的決策樹機器學習模型:

1. 選擇**模型「選項」** 選項卡,
1. 對於**類型**,選擇 **"樹**"。
1. 選取 [執行] **** ，在輸出視窗中以文字形式顯示樹狀結構。
1. 選取 [繪製] **** 按鈕以檢視圖形化版本。 決策樹看起來類似於我們之前通過使用rpart獲得的樹。

Rattle 的一個有用功能是能夠運行多種機器學習方法並快速評估它們。 以下步驟:

1. 對**型態型態型態,** 請選擇**所有。**
1. 選擇 **"執行**"
1. 當 Rattle 完成執行時,您可以選擇任何**Type**值(如**SVM),** 並查看結果。
1. 您還可以使用 **「評估」** 選項卡比較驗證集中模型的性能。例如,「**錯誤矩陣」** 選擇顯示驗證集中每個模型的混淆矩陣、總體誤差和平均類誤差。 您還可以繪製 ROC 曲線、運行靈敏度分析以及執行其他類型的模型評估。

構建模型完成後,選擇 **「日誌」** 選項卡以查看 Rattle 在工作階段期間執行的 R 程式碼。 您可以選取 [匯出] **** 按鈕來加以儲存。

> [!NOTE]
> 拉特的當前版本包含一個 Bug。 若要修改腳本或使用它稍後重複步驟,必須在日誌文本中插入**#***「匯出此日誌」* 前面的字元。

### <a name="postgresql-and-squirrel-sql"></a>後SQL和SQuirreL SQL

DSVM 隨附安裝 PostgreSQL。 PostgreSQL 是複雜的開放原始碼關聯式資料庫。 本節介紹如何將垃圾郵件庫數據集載入到 PostgreSQL 中,然後查詢它。

在載入數據之前,必須允許來自本地主機的密碼身份驗證。 在命令提示字元中執行︰

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

將**IPv4 本地連線**列變更為使用**md5**而不是**識別**,以便我們可以使用使用者名稱和密碼登入:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

然後,重新啟動 PostgreSQL 服務:

```Bash
sudo systemctl restart postgresql
```

要將*psql*psql(PostgreSQL 的互動式終端)作為內建 postgres 使用者啟動,執行以下指令:

```Bash
sudo -u postgres psql
```

使用用於登錄的 Linux 帳戶的使用者名創建新使用者帳戶。 建立密碼:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

登入 psql:

```Bash
psql
```

匯入新資料庫:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

現在,讓我們瀏覽數據並使用 SQuirreL SQL 執行一些查詢,這是一種圖形工具,可用於透過 JDBC 驅動程式與資料庫進行交互。

要開始,在 **「應用程式」** 選單上,打開 SQuirreL SQL。 若要設定驅動程式︰

1. 選擇**Windows** > **檢視驅動程式**。
1. 右鍵按一**下 PostgreSQL**並選擇 **「修改驅動程式**」 。。
1. 選擇 **「額外類路徑** > **添加**」。
1. 對**檔案名稱**,輸入 **/usr/share/java/jdbcdrives/postgresql-9.4.1208.jre6.jar**。
1. 選取 [開啟]****。
1. 選擇**清單驅動程式**。 對於**類別名稱**,選擇**org.postgresql.Driver,** 然後選擇 **「確定**」。

若要設定與本機伺服器的連線︰

1. 選擇**Windows** > **查看別名。**
1. 選擇按鈕**+** 以創建新別名。 對於新別名,請輸入**垃圾郵件資料庫**。 
1. 匯**入驅動程式**,選擇**PostgreSQL**。
1. 將 URL 設定為 **jdbc:postgresql://localhost/spam**。
1. 輸入您的使用者名稱和密碼。
1. 選取 [確定]  。
1. 若要開啟 [連線]**** 視窗，請按兩下**垃圾郵件資料庫**別名。
1. 選擇**連接**"連接"

若要執行一些查詢︰

1. 選取 [SQL] **** 索引標籤。
1. 在**SQL**選項卡頂端的查詢框中,輸入基本`SELECT * from data;`查詢,如 。
1. 按Ctrl_Enter以運行查詢。 默認情況下,SQuirreL SQL 從查詢中返回前 100 行。

可以運行更多查詢來流覽此數據。 例如，「make」 ** 一字在垃圾郵件和非垃圾郵件之間的出現頻率有何差異？

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

或者,經常包含*3d*的電子郵件有哪些特徵?

```SQL
SELECT * from data order by word_freq_3d desc;
```

大多數電子郵件的*3d*高發生顯然是垃圾郵件。 此資訊對於構建用於對電子郵件進行分類的預測模型可能很有用。

如果要使用儲存在 PostgreSQL 資料庫中的數據進行機器學習,請考慮使用[MADlib](https://madlib.incubator.apache.org/)。

### <a name="sql-data-warehouse"></a>SQL 資料倉儲

Azure SQL 資料倉庫是一個基於雲的橫向擴展資料庫,可以處理海量數據,包括關係和非關係數據。 如需詳細資訊，請參閱 [什麼是 Azure SQL 資料倉儲？](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

若要連線到資料倉儲並建立資料表，請從命令提示字元執行下列命令︰

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

在 sqlcmd 提示符下,執行以下指令:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

使用 bcp 複製資料:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> 下載的檔包含 Windows 樣式行尾。 bcp 工具預計 Unix 風格的行尾。 使用 -r 標誌告訴 bcp。

然後,使用 sqlcmd 查詢:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

您還可以使用 SQuirreL SQL 進行查詢。 使用 SQL Server JDBC 驅動程式,按照與 PostgreSQL 類似的步驟操作。 JDBC 驅動程式位於 /usr/share/java/jdbcdrives/sqljdbc42.jar 資料夾中。

## <a name="next-steps"></a>後續步驟

有關引導您完成 Azure 中資料科學過程的任務的文章的概述,請參閱[團隊資料科學流程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)。

有關顯示特定方案團隊資料科學流程中步驟的端到端演練的說明,請參閱[團隊資料科學流程演練](../team-data-science-process/walkthroughs.md)。 這些逐步解說也示範如何將雲端和內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。
