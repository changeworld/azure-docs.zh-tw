---
title: 支援的語言
titleSuffix: Azure Data Science Virtual Machine
description: 支援的程式語言和相關工具預先安裝在資料科學虛擬機器上。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283650"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的語言 

資料科學虛擬機器 （DSVM） 附帶了多種預構建語言和開發工具，用於構建人工智慧 （AI） 應用程式。 這裡有一些值得注意的。

## <a name="python-windows-server-2016-edition"></a>Python （Windows 伺服器 2016 版本）

|    |           |
| ------------- | ------------- |
| 支援語言版本 | Python 2.7 和 3.7 |
| 支援的 DSVM 版本      | Windows Server 2016     |
| 它是如何在 DSVM 上設定/安裝的？  | 創建了兩`conda`個全域環境： <br /> •`root`位於的`/anaconda/`環境為 Python 3.7。 <br/> •`python2`位於的`/anaconda/envs/python2`環境為 Python 2.7。       |
| 範例的連結      | 包括 Python 的朱派特筆記本示例。     |
| DSVM 的相關工具      | 皮斯派克，R，朱麗亞      |

> [!NOTE]
> 2018 年 3 月之前創建的 Windows 伺服器 2016 的版本中包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda**根**環境 **，py37**是 Python 3.7 環境。

### <a name="how-to-use-and-run-it"></a>如何使用和運行它    

* 在命令提示符下運行：

  打開命令提示並使用以下方法之一，具體取決於要運行的 Python 版本：

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* 在 IDE 中使用：

  使用 Visual Studio （PTVS） 的 Python 工具，安裝在視覺化工作室社區版本中。 預設情況下，在 PTVS 中自動設置的唯一環境是 Python 3.6。 

    > [!NOTE]
    > 要將 PTVS 指向 Python 2.7，必須在 PTVS 中創建自訂環境。 要在視覺化工作室社區版中設置此環境路徑，請轉到**工具** -> **Python 工具** -> **Python 環境**並選擇 **"自訂**"。 然後，將位置設置為**c：\anaconda\envs_python2，** 然後選擇 **"自動檢測**"。

* 在朱皮特中使用：

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將內核類型設置為 Python 3.7 的_Python [Conda 根]，_ 將 Python 2.7 的_Python [Conda env：python2] 設置為_Python 2.7。

* 安裝 Python 套裝軟體：

  DSVM 上的預設 Python 環境是所有使用者都能讀取的全域環境。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用`activate`命令作為管理員啟動到根或 python2 環境。 然後，您可以使用包管理器，也可以`conda`安裝或`pip`更新包。

## <a name="python-linux-edition"></a>Python （Linux 版本）

|    |           |
| ------------- | ------------- |
| 支援語言版本 | Python 2.7 和 3.5 |
| 支援的 DSVM 版本      | Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | 創建了兩`conda`個全域環境： <br /> * `root`位於 Python `/anaconda/` 2.7 的環境。 <br/> * `py35`位於 Python `/anaconda/envs/py35`3.5 的環境。       |
| 範例的連結      | 包括 Python 的朱派特筆記本示例。     |
| DSVM 的相關工具      | PySpark、R、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用和運行它    

* 在終端中運行：

  打開終端並執行以下操作之一，具體取決於要運行的 Python 版本：

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* 在 IDE 中使用：

  使用安裝在視覺工作室社區版的 PyCharm。 

* 在朱皮特中使用：

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將 Python 2.7 的內核類型設置為 Python 2.7 的 **"Conda Root"，** 將 Python 3.5 環境的**Python [Conda env：py35] 設置為**"Python"，也可以將內核類型設置為"Python 3.5 根"。 

* 安裝 Python 套裝軟體：

  DSVM 上的預設 Python 環境為全域環境，可由所有使用者讀取。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用`source activate`命令作為管理員或具有 sudo 許可權的使用者啟動到根或 py35 環境。 然後，您可以使用包管理器，也可以`conda`安裝或`pip`更新包。


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 支援語言版本 | 微軟 R 打開 3.x （100% 相容 CRAN-R）<br /> 微軟 R 伺服器 9.x 開發人員版本（可擴展的企業就緒 R 平臺）|
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux：`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 範例的連結      | 包括 R 的朱派筆記本示例。     |
| DSVM 的相關工具      | SparkR、Python、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用和運行它    

**視窗**：

* 在命令提示符下運行：

  開啟命令提示字元，然後輸入 `R`：

* 在 IDE 中使用：

  使用安裝於 Visual Studio Community 版本或 RStudio 的「Visual Studio R 工具」(RTVS)。 這些可在"開始"功能表或桌面圖示上使用。 

* 在朱皮特中使用

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將內核類型設置為**R**以使用聚居 R 內核 （IRKernel）。

* 安裝 R 包：

  R 安裝在所有使用者都能閱讀的全域環境中的 DSVM 上。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用上述方法之一運行 R。 然後，您可以運行 R 包管理器`install.packages()`來安裝或更新包。

**Linux**：

* 在終端中運行：

  打開終端並運行`R`。  

* 在 IDE 中使用：

  使用安裝在 Linux DSVM 上的 RStudio。  

* 在朱皮特中使用：

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將內核類型設置為**R**以使用聚居 R 內核 （IRKernel）。 

* 安裝 R 包：

  R 安裝在所有使用者都能閱讀的全域環境中的 DSVM 上。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用上述方法之一運行 R。 然後，您可以運行 R 包管理器`install.packages()`來安裝或更新包。


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 支援語言版本 | 0.6 |
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：安裝於 `C:\JuliaPro-VERSION`<br /> Linux：安裝於 `/opt/JuliaPro-VERSION`    |
| 範例的連結      | 包括朱麗亞的朱比特筆記本樣本。     |
| DSVM 的相關工具      | Python、R      |
### <a name="how-to-use-and-run-it"></a>如何使用和運行它    

**視窗**：

* 在命令提示符下運行

  打開命令提示符並運行`julia`。
* 在 IDE 中使用：

  與`Juno`DSVM 上安裝的 Julia IDE 一起使用，可作為桌面快捷方式提供。

* 在朱皮特中使用：

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將內核類型設置為**朱麗亞版本**。

* 安裝朱麗亞套裝軟體：

  預設 Julia 位置是所有使用者都能讀取的全域環境。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用上述方法之一運行 Julia。 然後，您可以運行 Julia 包管理器命令`Pkg.add()`，如安裝或更新包。


**Linux**：
* 在終端中運行：

  打開終端並運行`julia`。
* 在 IDE 中使用：

  使用`Juno`，在 DSVM 上安裝 Julia IDE，並作為**應用程式**功能表快捷方式提供。

* 在朱皮特中使用：

  打開 Jupyter 並選擇 **"新建"** 以創建新筆記本。 您可以將內核類型設置為**朱麗亞版本**。

* 安裝朱麗亞套裝軟體：

  預設 Julia 位置是所有使用者都能讀取的全域環境。 但是，只有管理員才能編寫和安裝全域包。 要將包安裝到全域環境，請使用上述方法之一運行 Julia。 然後，您可以運行 Julia 包管理器命令`Pkg.add()`，如安裝或更新包。

## <a name="other-languages"></a>其他語言

**C#**： 在 Windows 上可用，可通過 Visual Studio`Developer Command Prompt for Visual Studio`社區版本或在 中`csc`訪問，您可以在 其中運行該命令。

**JAVA**： OpenJDK 在 DSVM 的 Linux 和 Windows 版本中都有可用，並且設置在路徑上。 要使用 JAVA，請使用`javac` `java` Windows 中的命令提示符或 Linux 中的 bash shell 上鍵入 或 命令。

**Node.js**：Node.js 在 DSVM 的 Linux 和 Windows 版本中都有可用，並且設置在路徑上。 要訪問 Node.js，在`node` `npm` Windows 中的命令提示符或 Linux 中的 bash shell 上鍵入 或 命令。 在 Windows 上，安裝了 Node.js 工具的視覺化工作室擴展，以提供圖形 IDE 來開發 Node.js 應用程式。

**F#**： 在 Windows 上可用，可通過 Visual Studio`Developer Command Prompt for Visual Studio`社區版本或在 中`fsc`訪問，您可以在其中運行該命令。
