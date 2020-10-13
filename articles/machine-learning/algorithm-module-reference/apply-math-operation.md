---
title: 套用數學運算
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [套用數學運算] 模組，將數學運算套用至管線中的資料行值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 04ce45f428604275696d83938708bcee0c6c023f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536761"
---
# <a name="apply-math-operation"></a>套用數學運算

本文描述 Azure Machine Learning 表設計工具的模組。

使用「套用數學運算」來建立在輸入資料集中套用至數值資料行的計算。 

數學運算包括算術函式、三角函數、舍入函式，以及在資料科學中使用的特殊函式，例如 gamma 和 error 函數。  

在您定義作業並執行管線之後，這些值會加入至您的資料集。 根據您設定模組的方式，您可以：

+ 將結果附加至您的資料集 (在確認作業) 的結果時很有用。
+ 將資料行值取代為新的計算值。
+ 為結果產生新的資料行，而不顯示原始資料。 

在這些類別中尋找您需要的作業：  

- [基本](#basic-math-operations)  
  
     **基本**分類中的函式可以用來操作值的單一值或資料行。 例如，您可能會取得資料行中所有數位的絕對值，或計算資料行中每個值的平方根。  
  
-   [比較](#comparison-operations)  
  
      **Compare**類別中的函數全都用於比較：您可以對兩個數據行中的值進行成對的比較，也可以將資料行中的每個值與指定的常數進行比較。 例如，您可以比較資料行，以判斷兩個資料集中的值是否相同。 或者，您可以使用常數（例如允許的最大值）來尋找數值資料行中的極端值。  
  
-   [作業](#arithmetic-operations)  
  
     **作業**類別目錄包含基本數學函數：加法、減法、乘法和除法。 您可以使用這兩個數據行或常數。 例如，您可以將資料行 A 中的值加入至資料行 B 的值。或者，您可以從資料行 A 中的每個值減去常數，例如先前計算的平均值。  
  
-   [四捨五入](#rounding-operations)  
  
     **舍入**類別包含各種不同的函式，可執行四捨五入、上限、樓層和截斷等各種程度的精確度。 您可以指定小數和整數的有效位數層級。  
  
-   [特殊](#special-math-functions)  
  
     **特殊**類別包含在資料科學中特別使用的數學函數，例如橢圓積分和高斯誤差函數。  
  
-   [三角](#trigonometric-functions)  
  
     **三角**分類包含所有標準三角函數。 例如，您可以將弧度轉換為度數，或計算函數，例如弧度或度數的正切函數。
     這些函式是一元的，也就是說，它們會採用單一資料行的值做為輸入、套用三角函數，然後將值的資料行傳回做為結果。 請確定輸入資料行是適當的類型，且包含指定作業的正確數值型別。   

## <a name="how-to-configure-apply-math-operation"></a>如何設定套用數學運算  

[套用 **數學運算** ] 模組需要至少包含一個包含數位之資料行的資料集。 數位可以是離散或連續的，但必須是數值資料類型，而不是字串。

您可以將相同的作業套用至多個數值資料行，但是所有資料行都必須位於相同的資料集中。 

此模組的每個實例一次只能執行一種類型的作業。 若要執行複雜的數學運算，您可能需要將 [套用 **數學運算** ] 模組的數個實例連結在一起。  
  
1.  將 [套用 **數學運算** ] 模組新增至您的管線。

1. 連接至少包含一個數值資料行的資料集。  

1.  選取一或多個要執行計算的來源資料行。   
  
    - 您選擇的任何資料行都必須是數值資料類型。 
    - 資料的範圍必須適用于所選的數學運算。 否則可能會發生錯誤或 NaN (不是數字) 結果。 例如，Ln (-1.0) 是不正確作業，而且會產生值 `NaN` 。
  
1.  選取 [ **類別** ]，以選取要執行的數學運算 **類型** 。
    
1. 從該類別的清單中選擇特定的作業。
  
1.  設定每一種作業類型所需的其他參數。  
  
1.  使用 [ **輸出模式]** 選項來指出您要如何產生數學運算： 

    - **Append**。 當做輸入使用的所有資料行都包含在輸出資料集內，再加上一個附加資料行，其中包含數學運算的結果。
    - **就地**進行。 當做輸入使用之資料行中的值會取代為新的計算值。 
    - **僅限結果**。 傳回單一資料行，其中包含數學運算的結果。
  
1.  提交管線。  
  
## <a name="results"></a>結果

如果您使用 **附加** 或 **僅限結果** 選項來產生結果，則傳回資料集的資料行標題會指出作業和所使用的資料行。 例如，如果您使用 **Equals** 運算子來比較兩個數據行，結果看起來會像這樣：  
  
-   **等於 (Col2_Col1) **，表示您已針對 Col1 測試 Col2。  
-   **等於 (Col2_ $ 10) **，表示您比較資料行2與常數10。  

即使您使用 **就地** 選項，也不會刪除或變更來源資料。原始資料集中的資料行仍然可在設計工具中使用。 若要查看原始資料，您可以連接 [ [加入資料行](add-columns.md) ] 模組，並將它聯結至 [套用 **數學運算**] 的輸出。  
    
## <a name="basic-math-operations"></a>基本數學運算 

**基本**分類中的函式通常會採用資料行中的單一值、執行預先定義的作業，並傳回單一值。 針對某些函數，您可以將常數或資料行集指定為第二個引數。  
  
 Azure Machine Learning 支援 **基本** 類別中的下列功能：  

### <a name="abs"></a>Abs

傳回所選資料行的絕對值。  
  
### <a name="atan2"></a>Atan2

傳回四象限反正切函數。  

選取包含點座標的資料行。 針對對應至 x 座標的第二個引數，您也可以指定常數。  

對應至 MATLAB 中的 ATAN2 函數。  

### <a name="conj"></a>Conj

傳回所選資料行中值的共軛。  

### <a name="cuberoot"></a>CubeRoot

針對所選資料行中的值計算 cube 根。  

### <a name="doublefactorial"></a>DoubleFactorial  
 針對所選資料行中的值計算雙階乘。 雙階乘是一般階乘函數的延伸，表示為 `x!!`。  

### <a name="eps"></a>Eps

傳回目前的值與次高雙精度數字之間的差距大小。 對應至 MATLAB 中的 EPS 函數。  
  
### <a name="exp"></a>Exp

傳回所選資料行中值的乘冪。 此函數與 Excel EXP 函數相同。  

### <a name="exp2"></a>Exp2

傳回引數的以2為底數的指數，並針對 y = x * 2<sup>t</sup> 進行求解，其中 t 是包含指數的值資料行。  

在 [資料  **行集**] 中，選取包含指數值 t 的資料行。

若為 **Exp2** ，您可以指定第二個引數 x，它可以是常數或值的另一個資料行。 在 [ **第二個引數類型**] 中，指出您是否要將乘數 x 提供為常數，或資料行中的值。  

例如，如果您選取具有乘數和指數值的資料行 {0,1,2,3,4,5} ，函數會傳回 {0，2，8，24，64 160) 。  

### <a name="expminus1"></a>ExpMinus1 

傳回所選資料行中值的負指數。  

### <a name="factorial"></a>階乘
傳回所選資料行中值的階乘。  

### <a name="hypotenuse"></a>Hypotenuse
計算三角形的斜邊，其中某一端的長度指定為值的資料行，而第二個端的長度指定為常數或兩個數據行。  

### <a name="ln"></a>自然對數

傳回所選資料行中之值的自然對數。  

### <a name="lnplus1"></a>LnPlus1

傳回所選資料行中值的自然對數加1。  

### <a name="log"></a>Log

根據給定的指定基底，傳回選定資料行中之值的對數。  

您可以指定基底 (第二個引數) 做為常數，或是選取另一個資料行的值。  

### <a name="log10"></a>Log10

傳回所選資料行以10為底數的值。  

### <a name="log2"></a>Log2

傳回所選資料行的底數（以2為底數）值。  

### <a name="nthroot"></a>NthRoot
使用您指定的 n，傳回值的第 n 個根。  

使用 **ColumnSet** 選項，選取您要計算其根的資料行。  

在 [ **第二個引數類型**] 中，選取包含根的另一個資料行，或指定要做為根的常數。  

如果第二個引數是資料行，則會使用資料行中的每個值做為對應資料列的 n 值。 如果第二個引數為常數，請在 [ **第二個引數** ] 文字方塊中輸入 n 的值。
### <a name="pow"></a>Pow

針對所選資料行中的每個值，計算 X 的乘冪。  

首先，使用**ColumnSet**選項，選取包含**基底**（應為浮點數）的資料行。  

在 [ **第二個引數類型**] 中，選取包含指數的資料行，或指定要做為指數的常數。  

如果第二個引數是資料行，則會以資料行中的每個值做為對應資料列的指數。 如果第二個引數為常數，請在 [ **第二個引數** ] 文字方塊中輸入指數的值。  

### <a name="sqrt"></a>Sqrt

傳回所選資料行中值的平方根。  

### <a name="sqrtpi"></a>SqrtPi

針對所選資料行中的每個值，將值乘以 pi，然後傳回結果的平方根。  

### <a name="square"></a>Square

將所選資料行中的值平方。  

## <a name="comparison-operations"></a>比較運算  

每當您需要針對彼此測試兩組值時，請使用 Azure Machine Learning 設計工具中的比較函數。 例如，在管線中，您可能需要執行下列比較作業：  

- 針對臨界值評估機率分數模型的資料行。
- 判斷兩組結果是否相同。 針對不同的資料列，加入可用於進一步處理或篩選的 FALSE 旗標。  

### <a name="equalto"></a>EqualTo

如果值相同，則傳回 True。  

### <a name="greaterthan"></a>GreaterThan

如果 [資料 **行集** ] 中的值大於指定的常數，或大於比較資料行中的對應值，則傳回 True。  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

如果 [資料 **行集** ] 中的值大於或等於指定的常數，或大於或等於比較資料行中的對應值，則傳回 True。  

### <a name="lessthan"></a>LessThan

如果 [資料 **行集** ] 中的值小於指定的常數，或小於比較資料行中的對應值，則傳回 True。  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

如果 [資料 **行集** ] 中的值小於或等於指定的常數，或小於或等於比較資料行中的對應值，則傳回 True。  

### <a name="notequalto"></a>NotEqualTo

如果 [資料行集]**** 中的值不等於常數或比較資料行，則傳回 True，等於則傳回 False。  

### <a name="pairmax"></a>PairMax

傳回值，該值大於 [資料行集]**** 中的值或常數或比較資料行中的值。  

### <a name="pairmin"></a>PairMin

傳回小於或等於常數或比較資料行中的值（資料 **行集** 的值）或值  
  
##  <a name="arithmetic-operations"></a>算術運算   

包含基本算數運算：加法和減法、除法和乘法。  因為大部分的作業都是二進位，需要兩個數字，所以您必須先選擇運算，然後選擇要在第一個和第二個引數中使用的資料行。

您選擇除法和減法資料行的順序可能會違反直覺;不過，為了讓您更容易瞭解結果，資料行標題會提供作業名稱，以及使用資料行的順序。

作業|Num1|Num2|結果資料行|結果值|
----|----|----|----|----
|加法|1|5|新增 (Num2_Num1) | 4|
|乘法|1|5|多個 (Num2_Num1) |5|
|減|1|5| (Num2_Num1 減去) |4|
|減|0|1| (Num2_Num1 減去) |0|
|部門|1|5|將 (除以 Num2_Num1) |5|
|部門|0|1|將 (除以 Num2_Num1) |Infinity|

### <a name="add"></a>加

使用資料 **行集**來指定來源資料行，然後將 **第二個引數**中指定的數位加入至這些值。  

若要在兩個數據行中加入值，請使用資料 **行集**選擇一個資料行，然後使用 **第二個引數**選擇第二個數據行。  

### <a name="divide"></a>除以

將資料 **行集中** 的值除以常數，或除以 **第二個引數**中定義的資料行值。  換句話說，您會先挑選除數，然後再挑選被除數。 輸出值為商。

### <a name="multiply"></a>乘以

將 [資料行集]**** 中的值乘以指定的常數或資料行值。  

### <a name="subtract"></a>減去

使用 [資料**行集**] 選項選擇不同的資料行，以指定要在 (*被減數*) 上操作的值資料行。 然後，使用**第二個引數**下拉式清單，指定要 (*減數*) 減去的數位。 您可以選擇常數或值的資料行。

##  <a name="rounding-operations"></a>舍入作業 

Azure Machine Learning 設計工具支援各種舍入作業。 對於許多作業而言，您必須指定進位時要使用的精確度量。 您可以使用指定為常數的靜態精確度層級，也可以套用從值資料行取得的動態有效位數值。  

- 如果您使用常數，請將 [ **精確度類型** ] 設為 [ **常數** ]，然後在 [ **常數精確度** ] 文字方塊中輸入數位做為整數的位數。 如果您輸入非整數，模組不會引發錯誤，但結果可能是非預期的結果。  

- 若要針對資料集中的每個資料列使用不同的精確度值，請將 [ **精確度類型** ] 設定為 [ **ColumnSet**]，然後選擇包含適當精確度值的資料行。  

### <a name="ceiling"></a>Ceiling

傳回 [資料行集]**** 中之值的最高限度。  

### <a name="ceilingpower2"></a>CeilingPower2

傳回 [資料行集]**** 中之值的平方最高限度。  

### <a name="floor"></a>樓層

傳回 [資料行集]**** 中之值的最低限度 (依據指定的精確度)。  

### <a name="mod"></a>Mod

傳回 [資料行集]**** 中之值的小數部分 (依據指定的精確度)。  

### <a name="quotient"></a>商數

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

此類別包含通常用於資料科學的特殊數學函數。 除非另有說明，否則函數為一元，並針對所選資料行中的每個值傳回指定的計算。  

### <a name="beta"></a>Beta

傳回歐拉 beta 函數的值。  

### <a name="ellipticintegrale"></a>EllipticIntegralE
傳回不完整的橢圓整數值。  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

傳回完整橢圓積分 (K) 的值。  

### <a name="erf"></a>Erf

傳回誤差函數的值。  

誤差函數 (也稱為高斯誤差函數) 是一個 S 形的特殊函數，可在機率中用來說明擴散。  

### <a name="erfc"></a>Erfc

傳回互補誤差函數的值。  

`Erfc` 定義為1– erf (x) 。  

### <a name="erfscaled"></a>ErfScaled

傳回調整誤差函數的值。  

調整版的誤差函數可用來避免算術反向溢位。  

### <a name="erfinverse"></a>ErfInverse

傳回反向函數的值 `erf` 。  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

傳回指數整數 Ei 的值。  

### <a name="gamma"></a>色差補正

傳回 gamma 函數的值。  

### <a name="gammaln"></a>Gammaln 函數

傳回 gamma 函數的自然對數。  

### <a name="gammaregularizedp"></a>GammaRegularizedP

傳回正則化不完整 gamma 函數的值。  

此函式會採用第二個引數，可提供做為常數或值的資料行。  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

傳回反向正則化不完整 gamma 函數的值。  

此函式會採用第二個引數，可提供做為常數或值的資料行。  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

傳回正則化不完整 gamma 函數的值。  

此函式會採用第二個引數，可提供做為常數或值的資料行。  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

傳回反向廣義正則化不完整 gamma 函數的值。

此函式會採用第二個引數，可提供做為常數或值的資料行。  

### <a name="polygamma"></a>PolyGamma

傳回 polygamma 函數的值。  

此函式會採用第二個引數，可提供做為常數或值的資料行。

##  <a name="trigonometric-functions"></a>三角函式 

此類別會 iIncludes 大部分的重要三角函數和反三角函數。 所有三角函數都是一元的，不需要額外的引數。  

### <a name="acos"></a>Acos

計算資料行值的反余弦值。  

### <a name="acosdegree"></a>AcosDegree

計算資料行值的反余弦值（以度為單位）。  

### <a name="acosh"></a>Acosh

計算資料行值的雙曲反余弦值。  

### <a name="acot"></a>Acot

計算資料行值的 arccotangent。  

### <a name="acotdegrees"></a>AcotDegrees

計算資料行值的 arccotangent （以度為單位）。  

### <a name="acoth"></a>Acoth

計算資料行值的雙曲 arccotangent。  

### <a name="acsc"></a>Acsc

計算資料行值的 arccosecant。  

### <a name="acscdegrees"></a>AcscDegrees

計算資料行值的 arccosecant （以度為單位）。  
### <a name="asec"></a>Asec

計算資料行值的反正割。  

### <a name="asecdegrees"></a>AsecDegrees

計算資料行值的反正割（以度為單位）。  

### <a name="asech"></a>Asech

計算資料行值的雙曲反正割。  

### <a name="asin"></a>Asin

計算資料行值的反正弦值。  

### <a name="asindegrees"></a>AsinDegrees

計算資料行值的反正弦值（以度為單位）。  

### <a name="asinh"></a>Asinh

計算資料行值的雙曲反正弦值。  

### <a name="atan"></a>Atan

計算資料行值的反正切值。  

### <a name="atandegrees"></a>AtanDegrees

計算資料行值的反正切值（以度為單位）。  

### <a name="atanh"></a>Atanh

計算資料行值的雙曲反正切值。  

### <a name="cos"></a>Cos

計算資料行值的余弦值。  

### <a name="cosdegrees"></a>CosDegrees

計算資料行值的余弦值（以度為單位）。  

### <a name="cosh"></a>Cosh

計算資料行值的雙曲余弦值。  

### <a name="cot"></a>Cot

計算資料行值的餘切值。  

### <a name="cotdegrees"></a>CotDegrees

計算資料行值的餘切值（以度為單位）。  

### <a name="coth"></a>Coth
計算資料行值的雙曲餘切值。  

### <a name="csc"></a>Csc

計算資料行值的余割。  

### <a name="cscdegrees"></a>CscDegrees

計算資料行值的余割（以度為單位）。  

### <a name="csch"></a>Csch

計算資料行值的雙曲余割。  

### <a name="degreestoradians"></a>DegreesToRadians

將角度轉換為弧度。  

### <a name="sec"></a>Sec

計算資料行值的正割。  

### <a name="asecdegrees"></a>aSecDegrees

計算資料行值的正割（以度為單位）。  

### <a name="asech"></a>aSech

計算資料行值的雙曲正割。  

### <a name="sign"></a>簽署

傳回資料行值的正負號。  

### <a name="sin"></a>Sin

計算資料行值的正弦。  

### <a name="sinc"></a>Sinc

計算資料行值的正弦余弦值。  

### <a name="sindegrees"></a>SinDegrees

計算資料行值的正弦值（以度為單位）。  

### <a name="sinh"></a>Sinh

計算資料行值的雙曲正弦值。  

### <a name="tan"></a>Tan

計算資料行值的正切值。  

### <a name="tandegrees"></a>TanDegrees

計算引數的正切值（以度為單位）。  

### <a name="tanh"></a>Tanh

計算資料行值的雙曲正切值。  
  
## <a name="technical-notes"></a>技術說明

當您選取一個以上的資料行做為第二個運算子時，請務必小心。 如果作業很簡單，例如將常數加入至所有資料行，則可輕鬆瞭解結果。 

假設您的資料集有多個資料行，而且您將資料集加入至本身。 在結果中，每個資料行都會加入至本身，如下所示：  
  
|Num1|Num2|Num3|新增 (Num1_Num1) |新增 (Num2_Num2) |新增 (Num3_Num3) |
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

如果您需要執行更複雜的計算，則可以將 [套用 **數學運算**] 的多重實例鏈起來。 例如，您可以使用一個 [套用數學運算]**** 執行個體新增兩個資料行，然後使用另一個 [套用數學運算]**** 執行個體將總和除以常數，以取得平均值。  
  
或者，您也可以使用下列其中一個模組，以使用 SQL、R 或 Python 腳本一次執行所有計算：
 
+ [執行 R 指令碼](execute-r-script.md)
+ [執行 Python 指令碼](execute-python-script.md)
+ [套用 SQL 轉換](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
