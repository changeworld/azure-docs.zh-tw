---
title: 支援的語言
titleSuffix: Azure Data Science Virtual Machine
description: 資料科學虛擬機器上預先安裝了支援的程式語言和相關工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283650"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的語言 

資料科學虛擬機器（DSVM）隨附數種預先建立的語言和開發工具，可用來建立人工智慧（AI）應用程式。 以下是一些值得注意的部分。

## <a name="python-windows-server-2016-edition"></a>Python （Windows Server 2016 版）

|    |           |
| ------------- | ------------- |
| 支援的語言版本 | Python 2.7 和3。7 |
| 支援的 DSVM 版本      | Windows Server 2016     |
| 它是如何在 DSVM 上設定/安裝的？  | 系統會`conda`建立兩個全域環境： <br /> * 位於`root`的環境`/anaconda/`是 Python 3.7。 <br/> * 位於`python2`的環境`/anaconda/envs/python2`是 Python 2.7。       |
| 範例的連結      | 包含適用于 Python 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | PySpark、R、Julia。      |

> [!NOTE]
> 在2018年3月之前建立的 Windows Server 2016 組建包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda 的**根**環境， **py37**是 python 3.7 環境。

### <a name="how-to-use-and-run-it"></a>如何使用並加以執行    

* 在命令提示字元中執行：

  開啟命令提示字元，並使用下列其中一種方法，視您想要執行的 Python 版本而定：

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* 在 IDE 中使用：

  使用 Visual Studio Community 版本中安裝的適用於 Visual Studio 的 Python 工具（PTVS）。 根據預設，在 PTVS 中自動設定的唯一環境是 Python 3.6。 

    > [!NOTE]
    > 若要將 PTVS 指向 Python 2.7，您必須在 PTVS 中建立自訂環境。 若要在 Visual Studio Community 版本中設定此環境路徑，請移至 [**工具** -> ] [**python 工具** -> ] [**python 環境**] 並選取 [ **+ 自訂**] 然後，將 [位置] 設定為 [ **c:\anaconda\envs\python2** ]，然後選取 [**自動**偵測]。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為 python 3.7 的_python [Conda Root]_ ，以及 python 2.7 的_python [Conda env： python2]_ 。

* 安裝 Python 套件：

  DSVM 上的預設 Python 環境是所有使用者都可讀取的全域環境。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請以系統管理員身分使用`activate`命令來啟動至 root 或 python2 環境。 然後，您可以使用套件管理員（例如`conda`或`pip` ）來安裝或更新套件。

## <a name="python-linux-edition"></a>Python （Linux 版）

|    |           |
| ------------- | ------------- |
| 支援的語言版本 | Python 2.7 和3。5 |
| 支援的 DSVM 版本      | Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | 系統會`conda`建立兩個全域環境： <br /> * `root`位於的環境`/anaconda/`是 Python 2.7。 <br/> * `py35`位於的環境`/anaconda/envs/py35`是 Python 3.5。       |
| 範例的連結      | 包含適用于 Python 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | PySpark、R、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用並加以執行    

* 在終端機中執行：

  開啟終端機並執行下列其中一項，視您想要執行的 Python 版本而定：

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* 在 IDE 中使用：

  使用安裝在 Visual Studio Community edition 中的 PyCharm。 

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為 python 2.7 的**python [Conda Root]** ，以及 python 3.5 環境的**python [Conda env： py35]** 。 

* 安裝 Python 套件：

  DSVM 上的預設 Python 環境為全域環境，可由所有使用者讀取。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請以系統管理員身分或以具有 sudo `source activate`許可權的使用者身分，使用命令來啟動至 root 或 py35 環境。 然後，您可以使用套件管理員（例如`conda`或`pip` ）來安裝或更新套件。


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 支援的語言版本 | Microsoft R Open 3.x （100% 與 CRAN-R 相容）<br /> Microsoft R Server 9. x Developer edition （可擴充的企業級 R 平臺）|
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux：`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 範例的連結      | 包含適用于 R 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | SparkR、Python、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用並加以執行    

**Windows**：

* 在命令提示字元中執行：

  開啟命令提示字元，然後輸入 `R`：

* 在 IDE 中使用：

  使用安裝於 Visual Studio Community 版本或 RStudio 的「Visual Studio R 工具」(RTVS)。 這些功能可在 [開始] 功能表上取得，或做為桌面圖示。 

* 在 Jupyter 中使用

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為**r** ，以使用 Jupyter R 核心（IRKernel）。

* 安裝 R 套件：

  R 會安裝在全域環境中的 DSVM 上，可由所有使用者讀取。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 R。 然後，您可以執行 R 封裝管理員`install.packages()`來安裝或更新套件。

**Linux**：

* 在終端機中執行：

  開啟終端機並執行`R`。  

* 在 IDE 中使用：

  使用安裝在 Linux DSVM 上的 RStudio。  

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為**r** ，以使用 Jupyter R 核心（IRKernel）。 

* 安裝 R 套件：

  R 會安裝在全域環境中的 DSVM 上，可由所有使用者讀取。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 R。 然後，您可以執行 R 封裝管理員`install.packages()`來安裝或更新套件。


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 支援的語言版本 | 0.6 |
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：安裝於 `C:\JuliaPro-VERSION`<br /> Linux：安裝於 `/opt/JuliaPro-VERSION`    |
| 範例的連結      | 包含適用于 Julia 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | Python、R      |
### <a name="how-to-use-and-run-it"></a>如何使用並加以執行    

**Windows**：

* 在命令提示字元中執行

  開啟命令提示字元並執行`julia`。
* 在 IDE 中使用：

  使用`Juno`與安裝在 DSVM 上的 Julia IDE，並以桌面快捷方式提供。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為**JULIA 版本**。

* 安裝 Julia 套件：

  預設 Julia 位置是所有使用者都可讀取的全域環境。 但只有系統管理員可以撰寫和安裝全域套件。 若要將封裝安裝到全域環境，請使用上述其中一種方法來執行 Julia。 然後，您可以執行 Julia 套件管理員命令（ `Pkg.add()`例如）來安裝或更新套件。


**Linux**：
* 在終端機中執行：

  開啟終端機並執行`julia`。
* 在 IDE 中使用：

  使用`Juno`，並在 DSVM 上安裝 Julia IDE，並以**應用程式**功能表快捷方式提供。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [**新增**] 以建立新的筆記本。 您可以將核心類型設定為**JULIA 版本**。

* 安裝 Julia 套件：

  預設 Julia 位置是所有使用者都可讀取的全域環境。 但只有系統管理員可以撰寫和安裝全域套件。 若要將封裝安裝到全域環境，請使用上述其中一種方法來執行 Julia。 然後，您可以執行 Julia 套件管理員命令（ `Pkg.add()`例如）來安裝或更新套件。

## <a name="other-languages"></a>其他語言

**C #**：可在 Windows 上使用，並可透過 Visual Studio Community edition `Developer Command Prompt for Visual Studio`或，在您可以執行`csc`命令的上存取。

**JAVA**： OpenJDK 適用于 DSVM 的 Linux 和 Windows 版本，而且是在路徑上設定的。 若要使用 JAVA，請`javac`在`java` Windows 中的命令提示字元或 Linux 中的 bash shell 輸入或命令。

**Node.js： node.js**適用于 DSVM 的 Linux 和 Windows 版本，而且是在路徑上設定的。 若要存取 node.js，請在 Windows `node`中`npm`的命令提示字元中，或在 Linux 中的 bash shell 輸入或命令。 在 Windows 上，會安裝適用于 node.js 工具的 Visual Studio 擴充功能，以提供圖形化 IDE 來開發您的 node.js 應用程式。

**F #**：可在 Windows 上使用，並可透過 Visual Studio Community 版本或`Developer Command Prompt for Visual Studio`在上存取，您可以`fsc`在其中執行命令。
