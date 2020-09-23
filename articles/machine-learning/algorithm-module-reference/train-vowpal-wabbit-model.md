---
title: 定型 Vowpal Wabbit 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用「定型 Vowpal Wabbit 模型」模組，利用 Vowpal Wabbit 的實例來建立機器學習模型。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 6bc9f69440be772910ea8200b5ccf7d5a5122ae6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907798"
---
# <a name="train-vowpal-wabbit-model"></a>定型 Vowpal Wabbit 模型
本文描述如何使用 Azure Machine Learning 設計工具中的 [ **定型 Vowpal Wabbit 模型** ] 模組，透過 Vowpal Wabbit 來建立機器學習模型。  

若要將 Vowpal Wabbit 用於機器學習，請根據 Vowpal Wabbit 需求來格式化您的輸入，並以所需的格式來準備資料。 您可以使用此模組來指定 Vowpal Wabbit 命令列引數。 

當管線執行時，會將 Vowpal Wabbit 的實例連同指定的資料一起載入實驗執行時間。 定型完成時，會將模型序列化回工作區。 您可以立即使用模型來評分資料。 

若要在新資料上以累加方式定型現有的模型，請將儲存的模型連接到**定型 Vowpal Wabbit 模型**的**預先定型 Vowpal Wabbit 模型**輸入埠，然後將新的資料加入至另一個輸入埠。  

## <a name="what-is-vowpal-wabbit"></a>什麼是 Vowpal Wabbit？  

Vowpal Wabbit (VW) 是一種快速、平行的機器學習架構，由 Yahoo！針對分散式運算所開發。 研究。 後來移植到 Windows，並由 John Langford (Microsoft Research) 在平行架構中應用於科學運算。  

對機器學習服務而言很重要的 Vowpal Wabbit 功能包括持續學習 (線上學習) 、維度縮減，以及互動式學習。 當記憶體無法容納模型資料時，Vowpal Wabbit 也是問題的解決方案。  

Vowpal Wabbit 的主要使用者是先前使用架構進行機器學習工作的資料科學家，例如分類、回歸、主題模型或矩陣分解。 適用于 Vowpal Wabbit 的 Azure 包裝函式與內部部署版本的效能特性非常類似，因此您可以使用 Vowpal Wabbit 的強大功能和原生效能，並且輕鬆地將定型的模型發佈為實際運作服務。  

「 [特徵雜湊](feature-hashing.md) 」模組也包含 Vowpal Wabbit 所提供的功能，可讓您使用雜湊演算法將文字資料集轉換成二進位功能。  

## <a name="how-to-configure-vowpal-wabbit-model"></a>如何設定 Vowpal Wabbit 模型  

本節描述如何訓練新模型，以及如何將新的資料加入至現有的模型。

與設計工具中的其他模組不同，此模組會指定模組參數，並訓練模型。 如果您有現有的模型，您可以將它新增為選擇性輸入，以累加方式定型模型。

+ [以其中一種必要格式準備輸入資料](#prepare-the-input-data)
+ [訓練新模型](#create-and-train-a-vowpal-wabbit-model)
+ [以累加方式定型現有的模型](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>準備輸入資料

若要使用此模組來定型模型，輸入資料集必須以兩種支援格式的其中一種來組成單一文字資料行： **SVMLight** 或 **VW**。 這並不表示 Vowpal Wabbit 只會分析文字資料，而且必須以所需的文本檔案格式來準備功能和值。  

您可以從兩種資料集、檔案資料集或表格式資料集讀取資料。 這兩個資料集都必須是 SVMLight 或 VW 格式。 Vowpal Wabbit 資料格式的優點是它不需要單欄式格式，在處理稀疏資料時可節省空間。 如需此格式的詳細資訊，請參閱 [Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>建立和定型 Vowpal Wabbit 模型

1. 將 **定型 Vowpal Wabbit 模型** 模組新增至您的實驗。 
  
2. 新增訓練資料集，並將其連接到 **定型資料**。 如果訓練資料集是包含定型資料檔的目錄，請使用 **定型資料檔案的名稱**指定定型資料檔案名稱。 如果訓練資料集是單一檔案，請將 **定型資料檔案的名稱** 保留空白。

3. 在 [ **VW 引數** ] 文字方塊中，輸入 Vowpal Wabbit 可執行檔的命令列引數。

     例如，您可以新增 *`–l`* 來指定學習速率，或 *`-b`* 表示雜湊位的數目。  

     如需詳細資訊，請參閱 [Vowpal Wabbit 參數](#supported-and-unsupported-parameters) 一節。  

4. **定型資料檔案的名稱**：輸入包含輸入資料的檔案名。 只有當訓練資料集是目錄時，才會使用這個引數。

5. **指定檔案類型**：指出定型資料所使用的格式。 Vowpal Wabbit 支援這兩種輸入檔案格式：  

    - **VW** 代表 Vowpal Wabbit 所使用的內部格式。 如需詳細資訊，請參閱 [Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) 。 
    - **SVMLight** 是其他一些機器學習工具所使用的格式。 

6. **輸出可讀取模型**檔案：如果您想要模組將可讀取的模型儲存至執行記錄，請選取此選項。 此引數會對應至 `--readable_model` VW 命令列中的參數。  

7. **輸出反向雜湊**檔案：如果您想要模組將反向雜湊函式儲存至執行記錄中的一個檔案，請選取此選項。 此引數會對應至 `--invert_hash` VW 命令列中的參數。  

8. 提交管線。

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>重新定型現有的 Vowpal Wabbit 模型

Vowpal Wabbit 藉由將新的資料加入至現有的模型，來支援累加式定型。 有兩種方式可以取得現有的重新定型模型：

+ 在相同的管線中，使用另一個「 **定型 Vowpal Wabbit 模型** 」模組的輸出。  
  
+ 在設計師左方流覽窗格的 [ **資料集** ] 類別中找出已儲存的模型，並將它拖曳至您的管線。  

1. 將 **定型 Vowpal Wabbit 模型** 模組新增至您的管線。  
2. 將先前定型的模型連接至模組 **預先定型的 Vowpal Wabbit 模型** 輸入埠。
3. 將新的定型資料連線至模組的 **定型資料** 輸入埠。
4. 在 [ **定型 Vowpal Wabbit 模型**] 的 [參數] 窗格中，指定新定型資料的格式，以及如果輸入資料集是目錄，則為定型資料檔案名稱。
5. 如果對應的檔案需要儲存在執行記錄中，請選取 * * [輸出可讀取模型檔案] 和 [ **輸出反向雜湊檔** 選項]。

6. 提交管線。  
7. 選取模組，然後在右窗格中選取 [**輸出 + 記錄**] 索引標籤下的 [**註冊資料集**]，以在您的 Azure Machine Learning 工作區中保留更新的模型。  如果您未指定新名稱，更新的模型就會覆寫現有的已儲存模型。

## <a name="technical-notes"></a>技術說明

本節包含對常見問題的執行詳細資料、秘訣和解答。

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit 的優點

Vowpal Wabbit 能夠極快速地學習非線性特徵 (例如 N 字母組)。  

Vowpal Wabbit 使用*線上學習*技術，例如隨機梯度下降 (SGD)，一次一筆記錄來配適模型。 因此，能夠非常快速地反覆運算原始資料，且比其他大部分的模型更快速開發理想的預測量。 這個方法也可避免必須將所有定型資料讀入記憶體中。  

Vowpal Wabbit 會將所有資料都轉換成雜湊，而不只是文字資料，還有其他類別變數。 使用雜湊讓回歸加權的查閱更有效率，這對有效的隨機梯度下降非常重要。  

###  <a name="supported-and-unsupported-parameters"></a>支援和不支援的參數 

本節說明 Azure Machine Learning 設計工具中的 Vowpal Wabbit 命令列參數支援。 

一般來說，只支援一組有限的引數。 如需完整的引數清單，請使用 [Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。    

不支援下列參數：

-   中指定的輸入/輸出選項 [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     模組已自動設定這些屬性。  
  
-   此外，也不允許產生多個輸出或接受多個輸入的任何選項。 其中包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   僅支援受監督的學習演算法。 因此，不支援這些選項： *`–active`* 、 `--rank` 等等 *`--search`* 。 

### <a name="restrictions"></a>限制

因為服務的目標是要支援經驗豐富的 Vowpal Wabbit 使用者，所以必須事先使用 Vowpal Wabbit 原生文字格式來準備輸入資料，而不是使用其他模組所使用的資料集格式。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
