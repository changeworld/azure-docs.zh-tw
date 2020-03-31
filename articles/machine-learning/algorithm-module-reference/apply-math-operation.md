---
title: 套用數學運算
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用應用數學操作模組，將數學運算應用於管道中的列值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456738"
---
# <a name="apply-math-operation"></a>套用數學運算

本文介紹 Azure 機器學習設計器（預覽）模組。

使用"應用數學運算"創建應用於輸入資料集中的數位列的計算。 

支援的數學運算包括常用算術函數，如乘法和除法、三角函數、各種舍入函數，以及資料科學中使用的特殊函數，如伽馬函數和誤差函數。  

定義操作並運行管道後，這些值將添加到資料集中。 根據配置模組的方式，您可以：

+ 將結果追加到資料集中。 當您驗證操作的結果時，這特別有用。
+ 將列值替換為新的計算值。
+ 為結果生成新列，不顯示原始資料。 

查找以下類別中所需的操作：  

- [基本](#basic-math-operations)  
  
     **基本**類別中的函數可用於操作單個值或值列。 例如，您可以獲取列中所有數位的絕對值，或者計算列中每個值的平方根。  
  
-   [比較](#comparison-operations)  
  
      **比較**類別中的函數都用於比較：您可以對兩列中的值進行對數比較，也可以將列中的每個值與指定的常量進行比較。 例如，可以比較列以確定兩個資料集中的值是否相同。 或者，您可以使用常量（如允許的最大值）在數值列中查找異常值。  
  
-   [作業](#arithmetic-operations)  
  
     **操作**類別包括基本數學函數：加法、減法、乘法和除法。 可以使用列或常量。 例如，您可以將列 A 中的值添加到列 B 中的值。或者，您可能從列 A 中的每個值中減去常量，如以前計算的平均值。  
  
-   [四捨五入](#rounding-operations)  
  
     **舍入**類別包括用於執行操作的各種功能，如舍入、天花板、地板和截斷，以達到不同的精度級別。 您可以為小數數和整數指定精度級別。  
  
-   [特殊](#special-math-functions)  
  
     **特殊**類別包括資料科學中特別使用的數學函數，如橢圓積分和高斯誤差函數。  
  
-   [三角](#trigonometric-functions)  
  
     **三角學**類別包括所有標準三角函數。 例如，您可以將弧度轉換為度，或計算以弧度或度中的切線等函數。
     這些函數是一元函數，這意味著它們以單個值列作為輸入，應用三角函數，並返回一列值作為結果。  因此，您需要確保輸入列是適當的類型，並且包含指定操作的正確類型的值。   

## <a name="how-to-configure-apply-math-operation"></a>如何配置應用數學操作  

**應用數學操作模組**需要一個資料集，該資料集至少包含一個僅包含數位的列。 數位可以是離散的，也可以是連續的，但必須是數位資料類型，而不是字串。

可以將相同的操作應用於多個數位列，但所有列必須位於同一資料集中。 

此模組的每個實例一次只能執行一種類型的操作。 要執行複雜的數學運算操作，您可能需要將**應用數學操作**模組的幾個實例連結在一起。  
  
1.  將**應用數學操作**模組添加到管道中。

1. 連接至少包含一個數位列的資料集。  

1.  選擇要對其執行計算的一個或多個源列。   
  
    - 您選擇的任何列都必須是數位資料類型。 
    - 資料範圍必須對所選數學運算有效。 否則可能會發生錯誤或 NaN (不是數字) 結果。 例如，Ln（-1.0）是一個不正確操作，並會導致 值`NaN`。
  
1.  按一下 **"類別"** 以選擇要執行的數學運算**類型**。
    
1. 從該類別中的清單中選擇特定操作。
  
1.  設置每種操作類型所需的其他參數。  
  
1.  使用 **"輸出模式"** 選項指示如何生成數學運算： 

    - **Append**。 用作輸入的所有列都包含在輸出資料集中，另外附加一列，其中包含數學運算的結果。
    - **原位**。 用作輸入的列中的值將替換為新的計算值。 
    - **結果僅**。 返回一列，其中包含數學運算的結果。
  
1.  提交管道。  
  
## <a name="results"></a>結果

如果使用"**追加"或"****結果僅"** 選項生成結果，則返回的資料集的列標題指示操作和使用的列。 例如，如果使用**Equals**運算子比較兩列，則結果如下所示：  
  
-   **等於（Col2_Col1），** 表示你測試了Col2對科爾1。  
-   **等於（Col2_$10），** 表示您將列 2 與常量 10 進行比較。  

即使您使用"原地"選項，來源資料也不會被刪除或更改;因此，即使使用 **"原地"** 選項，來源資料也不會被刪除或更改。原始資料集中的列在設計器中仍然可用。 要查看原始資料，可以連接["添加列"](add-columns.md)模組並將其聯接到**應用數學運算的**輸出。  
    
## <a name="basic-math-operations"></a>基本數學運算 

**基本**類別中的函數通常從列中獲取單個值，執行預定義的操作，並返回單個值。 對於某些函數，可以將常量或列集指定為第二個參數。  
  
 Azure 機器學習支援 **"基本"** 類別中的以下功能：  

### <a name="abs"></a>Abs

返回所選列的絕對值。  
  
### <a name="atan2"></a>Atan2

傳回四象限反正切函數。  

選擇包含點座標的列。 對於對應于 x 座標的第二個參數，還可以指定常量。  

對應至 Matlab 中的 ATAN2 函數。  

### <a name="conj"></a>Conj

返回所選列中值的偶聯。  

### <a name="cuberoot"></a>CubeRoot

計算所選列中值的多維資料集根。  

### <a name="doublefactorial"></a>DoubleFactorial  
 計算所選列中值的雙因數。 雙因數是正常因數函數的延伸，表示為 x!!.  

### <a name="eps"></a>Eps

傳回目前的值與次高雙精度數字之間的差距大小。 對應至 Matlab 中的 EPS 函數。  
  
### <a name="exp"></a>Exp

返回 e 提升到所選列中值的功率。 這與 Excel EXP 函數相同。  

### <a name="exp2"></a>Exp2

返回參數的基-2 指數，求解 y = x = 2<sup>t，</sup>其中 t 是包含指數的值列。  

在**列集中**，選擇包含指數值 t 的列。

對於**Exp2，** 您可以指定第二個參數 x，它可以是常量或另一列值。 在第**二個參數類型**中，指示是提供乘數 x 作為常量，還是在列中提供值。  

例如，如果選擇具有乘數和指數值的{0,1,2,3,4,5}列，則函數將返回 {0、2、8、24、64 160）。  

### <a name="expminus1"></a>ExpMinus1 

返回所選列中值的負指數。  

### <a name="factorial"></a>階乘
返回所選列中值的因數。  

### <a name="hypotenuse"></a>Hypotenuse
計算三角形的斜用，其中一側的長度指定為值列，第二面的長度指定為常量或兩列。  

### <a name="ln"></a>Ln

返回所選列中值的自然對數。  

### <a name="lnplus1"></a>LnPlus1

返回所選列中值的自然對數加 1。  

### <a name="log"></a>Log

根據給定的指定基底，傳回選定資料行中之值的對數。  

您可以將基（第二個參數）指定為常量，也可以通過選擇另一列值來指定。  

### <a name="log10"></a>Log10

返回所選列的基本值 10 對數。  

### <a name="log2"></a>Log2

返回所選列的基本 2 對數值。  

### <a name="nthroot"></a>NthRoot
使用指定的 n 傳回值的第 n 個根。  

使用 **"列集"** 選項選擇要為其計算根目錄的列。  

在第**二個參數類型**中，選擇包含根的另一列，或指定用作根的常量。  

如果第二個參數是列，則列中的每個值都用作相應行的值 n。 如果第二個參數是常量，則在第**二個參數**文字方塊中鍵入 n 的值。
### <a name="pow"></a>Pow

計算所選列中每個值的 X 提升為 Y 的功率。  

首先，使用 **"列集"** 選項選擇包含**基礎**的列，該列應為浮點。  

在第**二個參數類型**中，選擇包含指數的列，或指定一個常量用作指數。  

如果第二個引數是資料行，則會以資料行中的每個值做為對應資料列的指數。 如果第二個參數是常量，則在第**二個參數**文字方塊中鍵入指數的值。  

### <a name="sqrt"></a>Sqrt

返回所選列中值的平方根。  

### <a name="sqrtpi"></a>SqrtPi

對於所選列中的每個值，將值乘以 pi，然後返回結果的平方根。  

### <a name="square"></a>Square

為所選列中的值平方。  

## <a name="comparison-operations"></a>比較運算  

隨時在 Azure 機器學習設計器中使用比較函數，需要相互測試兩組值。 例如，在管道中，您可能需要執行以下比較操作：  

- 根據閾值計算概率分數模型的列。
- 確定兩組結果是否相同。 對於每行不同的行，添加一個可用於進一步處理或篩選的 FALSE 標誌。  

### <a name="equalto"></a>EqualTo

如果值相同，則返回 True。  

### <a name="greaterthan"></a>GreaterThan

如果**列集中**的值大於指定的常量，或大於比較列中的相應值，則返回 True。  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

如果**列集中**的值大於或等於指定的常量，或大於或等於比較列中的相應值，則返回 True。  

### <a name="lessthan"></a>LessThan

如果**列集中**的值小於指定的常量，或者小於比較列中的相應值，則返回 True。  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

如果**列集中**的值小於或等於指定的常量，或小於或等於比較列中的相應值，則返回 True。  

### <a name="notequalto"></a>NotEqualTo

如果 [資料行集]**** 中的值不等於常數或比較資料行，則傳回 True，等於則傳回 False。  

### <a name="pairmax"></a>PairMax

傳回值，該值大於 [資料行集]**** 中的值或常數或比較資料行中的值。  

### <a name="pairmin"></a>PairMin

返回較小的值 —**列集中**的值或常量或比較列中的值  
  
##  <a name="arithmetic-operations"></a>算術運算   

包括基本算數運算：加法減法、除法和乘法。  由於大多數操作是二進位的，需要兩個數字，因此首先選擇該操作，然後選擇要在第一個和第二個參數中使用的列或數位。

選擇除法和減法列的順序可能看起來有悖常理;但是，為了便於理解結果，列標題提供操作名稱和使用列的順序。

作業|Num1|Num2|結果列|結果值|
----|----|----|----|----
|加|1|5|添加（Num2_Num1）| 4|
|乘|1|5|多（Num2_Num1）|5|
|減|1|5|減法（Num2_Num1）|4|
|減|0|1|減法（Num2_Num1）|0|
|除|1|5|除（Num2_Num1）|5|
|除|0|1|除（Num2_Num1）|Infinity|

### <a name="add"></a>加

使用**列集**指定源列，然後將**第二個參數**中指定的數位添加到這些值中。  

要在兩列中添加值，請使用**列集**選擇列或列，然後使用**第二個參數**選擇第二列。  

### <a name="divide"></a>除

按常量或**第二個參數**中定義的列值劃分**列中設置**的值。  換句話說，你先選擇分分，然後選擇股息。 輸出值是商。

### <a name="multiply"></a>乘

將 [資料行集]**** 中的值乘以指定的常數或資料行值。  

### <a name="subtract"></a>減

使用 **"列集"** 選項選擇其他列，指定要操作的值的列（*最小*值）。 然後，使用**第二個參數**下拉清單指定要減去的數位（子下拉*洪*）。 您可以選擇值的常量或列。

##  <a name="rounding-operations"></a>舍入操作 

Azure 機器學習設計器支援各種舍入操作。 對於許多操作，必須指定舍入時使用的精度量。 可以使用靜態精度級別（指定為常量），也可以應用從一列值中獲得的動態精度值。  

- 如果使用常量，則將 **"精度類型**"設置為**常量**，然後在 **"常量精度"** 文字方塊中將數位數位鍵入為整數。 如果鍵入非整數，模組不會引發錯誤，但結果可能出乎意料。  

- 要對資料集中的每行使用不同的精度值，請將**精度類型**設置為 **"列集**"，然後選擇包含適當精度值的列。  

### <a name="ceiling"></a>Ceiling

傳回 [資料行集]**** 中之值的最高限度。  

### <a name="ceilingpower2"></a>CeilingPower2

傳回 [資料行集]**** 中之值的平方最高限度。  

### <a name="floor"></a>樓層

傳回 [資料行集]**** 中之值的最低限度 (依據指定的精確度)。  

### <a name="mod"></a>Mod

傳回 [資料行集]**** 中之值的小數部分 (依據指定的精確度)。  

### <a name="quotient"></a>商式

傳回 [資料行集]**** 中之值的小數部分 (依據指定的精確度)。  

### <a name="remainder"></a>餘數

傳回 [資料行集]**** 中之值的餘數。  

### <a name="rounddigits"></a>RoundDigits

傳回 [資料行集]**** 中的值 (依據四捨五入規則捨入至指定的位數)。  

### <a name="rounddown"></a>RoundDown

傳回 [資料行集]**** 中的值 (捨去至指定的位數)。  

### <a name="roundup"></a>RoundUp

傳回 [資料行集]**** 中的值 (進位至指定的位數)。  

### <a name="toeven"></a>ToEven

傳回 [資料行集]**** 中的值 (捨入至最接近的偶數整數)。  

### <a name="toodd"></a>ToOdd

傳回 [資料行集]**** 中的值 (捨入至最接近的奇數整數)。  

### <a name="truncate"></a>Truncate

移除指定的精確度不允許的所有位數，截斷 [資料行集]**** 中的值。  
  
## <a name="special-math-functions"></a>特殊數學函數

此類別包括資料科學中常用的專用數學函數。 除非另有說明，否則函數是一個未編的，並返回所選列或列中每個值的指定計算。  

### <a name="beta"></a>Beta

傳回歐拉 beta 函數的值。  

### <a name="ellipticintegrale"></a>EllipticIntegralE
返回不完整的橢圓積分的值。  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

傳回完整橢圓積分 (K) 的值。  

### <a name="erf"></a>Erf

傳回誤差函數的值。  

誤差函數 (也稱為高斯誤差函數) 是一個 S 形的特殊函數，可在機率中用來說明擴散。  

### <a name="erfc"></a>Erfc

傳回互補誤差函數的值。  

Erfc 定義為 1 = erf（x）。  

### <a name="erfscaled"></a>ErfScaled

傳回調整誤差函數的值。  

調整版的誤差函數可用來避免算術反向溢位。  

### <a name="erfinverse"></a>ErfInverse

傳回反向 erf 函數的值。  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

返回指數積分 Ei 的值。  

### <a name="gamma"></a>色差補正

傳回 gamma 函數的值。  

### <a name="gammaln"></a>Gammaln 函數

傳回 gamma 函數的自然對數。  

### <a name="gammaregularizedp"></a>GammaRegularizedP

傳回正則化不完整 gamma 函數的值。  

此函數採用第二個參數，該參數可以作為常量或值列提供。  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

傳回反向正則化不完整 gamma 函數的值。  

此函數採用第二個參數，該參數可以作為常量或值列提供。  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

傳回正則化不完整 gamma 函數的值。  

此函數採用第二個參數，該參數可以作為常量或值列提供。  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

傳回反向廣義正則化不完整 gamma 函數的值。

此函數採用第二個參數，該參數可以作為常量或值列提供。  

### <a name="polygamma"></a>PolyGamma

傳回 polygamma 函數的值。  

此函數採用第二個參數，該參數可以作為常量或值列提供。

##  <a name="trigonometric-functions"></a>三角函式 

此類別 i 包括大多數重要的三角測量和逆三角函數。 所有三角函數都是一元的，不需要額外的引數。  

### <a name="acos"></a>Acos

計算柱值的弧線。  

### <a name="acosdegree"></a>阿科斯學位

計算列值的弧形（以度表示）。  

### <a name="acosh"></a>阿科什

計算列值的雙曲弧線。  

### <a name="acot"></a>Acot

計算柱值的弧形。  

### <a name="acotdegrees"></a>阿科特度

計算柱值的弧形（以度表示）。  

### <a name="acoth"></a>阿科斯

計算列值的雙曲弧形。  

### <a name="acsc"></a>阿csc

計算列值的弧形。  

### <a name="acscdegrees"></a>阿不安度

計算柱值的弧度（以度表示）。  
### <a name="asec"></a>阿塞塞

計算列值的弧形。  

### <a name="asecdegrees"></a>Asec度

計算列值的弧度（以度表示）。  

### <a name="asech"></a>Asech

計算列值的雙曲弧線。  

### <a name="asin"></a>Asin

計算列值的弧形。  

### <a name="asindegrees"></a>AsinDegrees

計算列值的弧形（以度表示）。  

### <a name="asinh"></a>Asinh

計算列值的雙曲弧。  

### <a name="atan"></a>Atan

計算列值的弧形。  

### <a name="atandegrees"></a>AtanDegrees

計算列值的弧形（以度表示）。  

### <a name="atanh"></a>Atanh

計算列值的雙曲弧線。  

### <a name="cos"></a>Cos

計算列值的可數。  

### <a name="cosdegrees"></a>CosDegrees

計算列值的可列子（以度表示）。  

### <a name="cosh"></a>Cosh

計算列值的雙曲性拋物。  

### <a name="cot"></a>Cot

計算列值的共和。  

### <a name="cotdegrees"></a>科特度

計算列值的共切值（以度表示）。  

### <a name="coth"></a>Coth
計算列值的雙曲同形。  

### <a name="csc"></a>Csc

計算列值的共分器。  

### <a name="cscdegrees"></a>CscDegrees

計算列值的共分值（以度表示）。  

### <a name="csch"></a>Csch

計算列值的雙曲同痛劑。  

### <a name="degreestoradians"></a>DegreesToRadians

將度數轉換為弧度。  

### <a name="sec"></a>Sec

計算列值的分離值。  

### <a name="asecdegrees"></a>aSec度

計算列值（以度表示）的分離值。  

### <a name="asech"></a>aSech

計算列值的雙曲分離。  

### <a name="sign"></a>簽署

返回列值的符號。  

### <a name="sin"></a>Sin

計算列值的子值。  

### <a name="sinc"></a>Sinc

計算列值的子值。  

### <a name="sindegrees"></a>SinDegrees

計算列值的子值（以度表示）。  

### <a name="sinh"></a>Sinh

計算列值的雙曲子。  

### <a name="tan"></a>Tan

計算列值的切線。  

### <a name="tandegrees"></a>TanDegrees

計算參數的切線（以度表示）。  

### <a name="tanh"></a>Tanh

計算列值的雙曲切線。  
  
## <a name="technical-notes"></a>技術說明

選擇多個列作為第二個運算子時要小心。 如果操作簡單，例如向所有列添加常量，則結果很容易理解。 

假設您的資料集有多個列，並將資料集添加到自身。 在結果中，每列都添加到自身，如下所示：  
  
|Num1|Num2|數位3|添加（Num1_Num1）|添加（Num2_Num2）|添加（Num3_Num3）|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

如果需要執行更複雜的計算，可以連結**應用數學運算**的多個實例。 例如，您可以使用一個 [套用數學運算]**** 執行個體新增兩個資料行，然後使用另一個 [套用數學運算]**** 執行個體將總和除以常數，以取得平均值。  
  
或者，使用以下模組之一一一次執行所有計算，使用 SQL、R 或 Python 腳本：
 
+ [執行 R 指令碼](execute-r-script.md)
+ [執行 Python 指令碼](execute-python-script.md)
+ [套用 SQL 轉換](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
