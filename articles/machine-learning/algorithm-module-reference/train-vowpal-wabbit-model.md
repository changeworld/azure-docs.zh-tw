---
title: 定型 Vowpal Wabbit 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用定型 Vowpal Wabbit 模組來建立機器學習模型，方法是使用 Vowpal Wabbit 的實例。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 34caefba5bae660ca150f6e447b1d59b7174c768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857730"
---
# <a name="train-vowpal-wabbit-model"></a>定型 Vowpal Wabbit 模型
本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的**定型 Vowpal Wabbit**模組，以使用 Vowpal Wabbit 來建立機器學習模型。  

若要將 Vowpal Wabbit 用於機器學習服務，請根據 Vowpal Wabbit 需求來格式化您的輸入，並以所需的格式準備資料。 使用此模組來指定 Vowpal Wabbit 命令列引數。 

執行管線時，會將 Vowpal Wabbit 的實例與指定的資料一起載入實驗執行時間。 定型完成時，模型會序列化回工作區。 您可以立即使用模型來評分資料。 

若要以累加方式將新資料的現有模型定型，請將儲存的模型連接到**訓練 Vowpal Wabbit 模型**的**預先定型 Vowpal Wabbit 模型**輸入埠，並將新的資料加入至其他輸入埠。  

## <a name="what-is-vowpal-wabbit"></a>什麼是 Vowpal Wabbit？  

Vowpal Wabbit （VW）是快速、平行的機器學習架構，由 Yahoo！為分散式運算所開發 研究。 後來移植到 Windows，並由 John Langford (Microsoft Research) 在平行架構中應用於科學運算。  

對機器學習很重要的 Vowpal Wabbit 功能包括持續學習（線上學習）、維度縮減和互動式學習。 當記憶體無法容納模型資料時，Vowpal Wabbit 也是問題的解決方案。  

Vowpal Wabbit 的主要使用者是先前已針對機器學習工作（例如分類、回歸、主題模型或矩陣分解）使用架構的資料科學家。 適用于 Vowpal Wabbit 的 Azure 包裝函式與內部部署版本具有非常相似的效能特性，因此您可以使用 Vowpal Wabbit 的強大功能和原生效能，並輕鬆地將定型的模型發佈為運作服務。  

[功能雜湊](feature-hashing.md)模組也包含 Vowpal Wabbit 所提供的功能，可讓您使用雜湊演算法將文字資料集轉換成二進位功能。  

## <a name="how-to-configure-vowpal-wabbit-model"></a>如何設定 Vowpal Wabbit 模型  

本節說明如何將新的模型定型，以及如何將新的資料加入至現有的模型。

不同于設計師中的其他模組，此模組會指定模組參數，並訓練模型。 如果您有現有的模型，您可以將它加入做為選擇性輸入，以累加方式定型模型。

+ [以其中一個必要的格式準備輸入資料](#prepare-the-input-data)
+ [將新模型定型](#create-and-train-a-vowpal-wabbit-model)
+ [以累加方式訓練現有模型](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>準備輸入資料

若要使用此模組來定型模型，輸入資料集必須由下列兩種支援格式之一的單一文字資料行所組成： **SVMLight**或**VW**。 這並不表示 Vowpal Wabbit 只會分析文字資料，而只有必須以必要的文本檔案格式來準備功能和值。  

您可以從兩種資料集、檔案資料集或表格式資料集讀取資料。 這兩個資料集都必須是 SVMLight 或 VW 格式。 Vowpal Wabbit 資料格式的優點是它不需要單欄式格式，在處理稀疏資料時可節省空間。 如需有關此格式的詳細資訊，請參閱[Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>建立和定型 Vowpal Wabbit 模型

1. 將**訓練 Vowpal Wabbit**模組新增至您的實驗。 
  
2. 新增訓練資料集，並將它連接至**定型資料**。 如果訓練資料集是包含定型資料檔的目錄，請以**定型資料檔案的名稱**指定定型資料檔案名稱。 如果訓練資料集是單一檔案，請將**定型資料檔案的名稱**保留空白。

3. 在 [ **VW 引數**] 文字方塊中，輸入 Vowpal Wabbit 可執行檔的命令列引數。

     例如，您可以新增 *`–l`* 來指定學習速率，或用 *`-b`* 來表示雜湊位的數目。  

     如需詳細資訊，請參閱[Vowpal Wabbit parameters](#supported-and-unsupported-parameters)一節。  

4. **定型資料檔案的名稱**：輸入包含輸入資料的檔案名。 只有當訓練資料集是目錄時，才會使用這個引數。

5. **指定檔案類型**：指出定型資料所使用的格式。 Vowpal Wabbit 支援這兩種輸入檔案格式：  

    - **VW**代表 Vowpal Wabbit 所使用的內部格式。 如需詳細資訊，請參閱[Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
    - **SVMLight**是一些其他機器學習工具所使用的格式。 

6. **輸出可讀取的模型**檔案：如果您想要模組將可讀取的模型儲存至執行記錄，請選取此選項。 這個引數會對應至 `--readable_model` VW 命令列中的參數。  

7. **輸出反向雜湊**檔案：如果您想要模組將反向雜湊函式儲存至執行記錄中的一個檔案，請選取此選項。 這個引數會對應至 `--invert_hash` VW 命令列中的參數。  

8. 提交管線。

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>重新定型現有的 Vowpal Wabbit 模型

Vowpal Wabbit 藉由將新的資料加入至現有的模型，支援累加式訓練。 有兩種方式可取得重新定型的現有模型：

+ 在相同的管線中，使用另一個**定型 Vowpal Wabbit 模型**模組的輸出。  
  
+ 在設計師左側流覽窗格的 [**資料集**] 分類中找出已儲存的模型，並將它拖曳到您的管線中。  

1. 將**訓練 Vowpal Wabbit**模組新增至您的管線。  
2. 將先前定型的模型連接到模組**預先定型的 Vowpal Wabbit 模型**輸入埠。
3. 將新的訓練資料連線到模組的**定型資料**輸入埠。
4. 在 [**定型 Vowpal Wabbit 模型**] 的 [參數] 窗格中，指定新定型資料的格式，以及如果輸入資料集是目錄，則為定型資料檔案名稱。
5. 如果對應的檔案需要儲存在執行記錄中，請選取 [輸出可讀取的模型檔案] 和 [**輸出反轉的雜湊**檔選項]。

6. 提交管線。  
7. 選取模組，然後在右窗格中選取 [**輸出 + 記錄**] 索引標籤底下的 [**註冊資料集**]，以在您的 Azure Machine Learning 工作區中保留更新的模型。  如果您未指定新的名稱，則更新的模型會覆寫現有已儲存的模型。

## <a name="technical-notes"></a>技術說明

本章節包含執行詳細資料、秘訣和常見問題的解答。

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit 的優點

Vowpal Wabbit 能夠極快速地學習非線性特徵 (例如 N 字母組)。  

Vowpal Wabbit 使用*線上學習*技術，例如隨機梯度下降 (SGD)，一次一筆記錄來配適模型。 因此，能夠非常快速地反覆運算原始資料，且比其他大部分的模型更快速開發理想的預測量。 這個方法也可避免必須將所有定型資料讀入記憶體中。  

Vowpal Wabbit 會將所有資料都轉換成雜湊，而不只是文字資料，還有其他類別變數。 使用雜湊讓回歸權數的查閱更有效率，這對於有效的隨機梯度下降而言非常重要。  

###  <a name="supported-and-unsupported-parameters"></a>支援和不支援的參數 

本節說明 Azure Machine Learning 設計工具中 Vowpal Wabbit 命令列參數的支援。 

一般來說，只支援一組有限的引數。 如需引數的完整清單，請使用[Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。    

不支援下列參數：

-   中指定的輸入/輸出選項[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     模組已自動設定這些屬性。  
  
-   此外，不允許任何會產生多個輸出或接受多個輸入的選項。 其中包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   僅支援受監督的學習演算法。 因此，不支援下列選項： *`–active`* 、 `--rank` 等 *`--search`* 。 

### <a name="restrictions"></a>限制

因為服務的目標是要支援 Vowpal Wabbit 的有經驗的使用者，所以必須事先使用 Vowpal Wabbit 原生文字格式（而不是其他模組所使用的資料集格式）預先準備輸入資料。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
