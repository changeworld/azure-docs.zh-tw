---
title: 支援的語言
titleSuffix: Azure Data Science Virtual Machine
description: 資料科學虛擬機器上預先安裝的支援程式語言和相關工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8a73336ba0a324c65ee0de764d81a8e69ab6079c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87843261"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的語言 

資料科學虛擬機器 (DSVM) 提供數種預先建立的語言和開發工具，可讓您建立人工智慧 (AI) 應用程式。 以下是一些值得注意的專案。

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 版) 

| 類別 | 值 |
| ------------- | ------------- |
| 支援的語言版本 | Python 2.7 和3。7 |
| 支援的 DSVM 版本      | Windows Server 2016     |
| 它是如何在 DSVM 上設定/安裝的？  | 建立兩個全域 `conda` 環境： <br /> * `root` 位於的環境 `/anaconda/` 是 Python 3.7。 <br/> * `python2` 位於的環境 `/anaconda/envs/python2` 是 Python 2.7。       |
| 範例的連結      | 包含適用于 Python 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | PySpark、R、Julia。      |

> [!NOTE]
> 在2018年3月之前建立的 Windows Server 2016 組建包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda **根** 環境， **py37** 是 python 3.7 環境。

### <a name="how-to-use-and-run-it"></a>如何使用和執行    

* 在命令提示字元中執行：

  開啟命令提示字元，並根據您要執行的 Python 版本，使用下列其中一種方法：

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* 在 IDE 中使用：

  使用 Visual Studio Community 版本中安裝的適用於 Visual Studio 的 Python 工具 (PTVS) 。 依預設，在 PTVS 中自動設定的唯一環境是 Python 3.6。 

    > [!NOTE]
    > 若要將 PTVS 指向 Python 2.7，您必須在 PTVS 中建立自訂環境。 若要在 Visual Studio Community 版本中設定此環境路徑，請移至 [**工具**  ->  **python 工具**  ->  **python 環境**]，然後選取 [ **+ 自訂**]。 然後，將 [位置] 設定為 [ **c:\anaconda\envs\python2** ]，然後選取 [ **自動**偵測]。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以針對 python 3.7 將核心類型設定為 _python [Conda 根目錄]_ ，並針對 python 2.7 設定 _python [Conda env： python2]_ 。

* 安裝 Python 套件：

  DSVM 上的預設 Python 環境為全域環境，可由所有使用者讀取。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請以系統管理員身分使用命令來啟動至 root 或 python2 環境 `activate` 。 然後，您可以使用類似或的封裝 `conda` 管理員 `pip` 來安裝或更新套件。

## <a name="python-linux-edition"></a>Python (Linux 版本) 

| 類別 | 值 |
| ------------- | ------------- |
| 支援的語言版本 | Python 2.7 和3。5 |
| 支援的 DSVM 版本      | Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | 建立兩個全域 `conda` 環境： <br /> * `root` 位於的環境 `/anaconda/` 是 Python 2.7。 <br/> * `py35` 位於的環境 `/anaconda/envs/py35` 是 Python 3.5。       |
| 範例的連結      | 包含適用于 Python 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | PySpark、R、Julia      |

### <a name="how-to-use-and-run-it"></a>如何使用和執行    

* 在終端機中執行：

  開啟終端機，並根據您要執行的 Python 版本，執行下列其中一項作業：

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* 在 IDE 中使用：

  使用 Visual Studio Community 版中安裝的 PyCharm。 

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以針對 python 3.5 環境將核心類型設定為 python **[Conda 根目錄]** （python 2.7 和 **python [Conda env： py35]** ）。 

* 安裝 Python 套件：

  DSVM 上的預設 Python 環境為全域環境，可由所有使用者讀取。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請以 `source activate` 系統管理員或具有 sudo 許可權的使用者身分，使用命令來啟動至 root 或 py35 環境。 然後，您可以使用類似或的封裝 `conda` 管理員 `pip` 來安裝或更新套件。


## <a name="r"></a>R

| 類別 | 值 |
| ------------- | ------------- |
| 支援的語言版本 | Microsoft R Open 3.x (100% 與 CRAN-R) 相容<br /> Microsoft R Server 2.x Developer edition (可調整的企業級 R 平臺) |
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux：`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 範例的連結      | 包含適用于 R 的範例 Jupyter 筆記本。     |
| DSVM 上的相關工具      | SparkR、Python、Julia      |

### <a name="how-to-use-and-run-it"></a>如何使用和執行    

**Windows**：

* 在命令提示字元中執行：

  開啟命令提示字元，然後輸入 `R`：

* 在 IDE 中使用：

  使用安裝於 Visual Studio Community 版本或 RStudio 的「Visual Studio R 工具」(RTVS)。 這些可以在 [開始] 功能表或作為桌面圖示來使用。 

* 在 Jupyter 中使用

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以將核心類型設定為 **r** ，以使用 Jupyter R 核心 (IRKernel) 。

* 安裝 R 套件：

  R 安裝在可由所有使用者讀取的全域環境中的 DSVM 上。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 R。 然後，您可以執行 R 封裝管理員 `install.packages()` 來安裝或更新套件。

**Linux**：

* 在終端機中執行：

  開啟終端機並執行 `R` 。  

* 在 IDE 中使用：

  使用安裝在 Linux DSVM 上的 RStudio。  

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以將核心類型設定為 **r** ，以使用 Jupyter R 核心 (IRKernel) 。 

* 安裝 R 套件：

  R 安裝在可由所有使用者讀取的全域環境中的 DSVM 上。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 R。 然後，您可以執行 R 封裝管理員 `install.packages()` 來安裝或更新套件。


## <a name="julia"></a>Julia

| 類別 | 值 |
| ------------- | ------------- |
| 支援的語言版本 | 0.6 |
| 支援的 DSVM 版本      | Linux、Windows     |
| 它是如何在 DSVM 上設定/安裝的？  | Windows：安裝於 `C:\JuliaPro-VERSION`<br /> Linux：安裝於 `/opt/JuliaPro-VERSION`    |
| 範例的連結      | 包含適用于 Julia 的 Jupyter 筆記本範例。     |
| DSVM 上的相關工具      | Python、R      |

### <a name="how-to-use-and-run-it"></a>如何使用和執行    

**Windows**：

* 在命令提示字元中執行

  開啟命令提示字元並執行 `julia` 。
* 在 IDE 中使用：

  使用 `Juno` 與安裝在 DSVM 上的 JULIA IDE，並以桌面快捷方式提供使用。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以將核心類型設定為 **JULIA 版本**。

* 安裝 Julia 套件：

  預設的 Julia 位置是可由所有使用者讀取的全域環境。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 Julia。 然後，您可以執行 Julia 套件管理員命令，例如 `Pkg.add()` 安裝或更新套件。


**Linux**：
* 在終端機中執行：

  開啟終端機並執行 `julia` 。
* 在 IDE 中使用：

  使用 `Juno` ，並將 JULIA IDE 安裝在 DSVM 上，並以 **應用程式** 功能表快捷方式的形式提供。

* 在 Jupyter 中使用：

  開啟 Jupyter，然後選取 [ **新增** ] 以建立新的筆記本。 您可以將核心類型設定為 **JULIA 版本**。

* 安裝 Julia 套件：

  預設的 Julia 位置是可由所有使用者讀取的全域環境。 但只有系統管理員可以撰寫和安裝全域套件。 若要將套件安裝到全域環境，請使用上述其中一種方法來執行 Julia。 然後，您可以執行 Julia 套件管理員命令，例如 `Pkg.add()` 安裝或更新套件。

## <a name="other-languages"></a>其他語言

**C #**：可在 Windows 上使用，並可透過 Visual Studio Community 版或在上 `Developer Command Prompt for Visual Studio` 執行，您可以在其中執行 `csc` 命令。

**JAVA**： OpenJDK 適用于 DSVM 的 Linux 和 Windows 版本，而且是在路徑上設定的。 若要使用 JAVA，請 `javac` 在 `java` Windows 命令提示字元中或在 Linux 中的 bash shell 上輸入或命令。

**Node.js**： Node.js 適用于 DSVM 的 Linux 和 Windows 版本，而且是在路徑上設定的。 若要存取 Node.js，請 `node` 在 `npm` Windows 命令提示字元中或在 Linux 中的 bash shell 上輸入或命令。 在 Windows 上，會安裝 Node.js 工具的 Visual Studio 擴充功能，以提供圖形化 IDE 來開發 Node.js 應用程式。

**F #**：可在 Windows 上使用，並可透過 Visual Studio Community 版或在上 `Developer Command Prompt for Visual Studio` 執行，您可以在其中執行 `fsc` 命令。
