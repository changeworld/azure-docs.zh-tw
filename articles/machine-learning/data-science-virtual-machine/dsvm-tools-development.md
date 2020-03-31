---
title: 開發工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解資料科學虛擬機器上可用的工具和整合式開發環境。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282676"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器上的開發工具

資料科學虛擬機器 （DSVM） 在高生產率的整合式開發環境 （IDE） 中捆綁了幾個常用工具。 以下是 DSVM 提供的一些工具。

## <a name="visual-studio-community-edition"></a>Visual Studio Community 版本

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 通用 IDE      |
| 支援的 DSVM 版本      | 視窗： 視覺工作室 2017， Windows 2019 ： 視覺工作室 2019      |
| 典型的使用案例      | 軟體發展    |
| 它是如何在 DSVM 上配置和安裝的？      | 資料科學工作負載 (Python 和 R 工具)、Azure 工作負載 (Hadoop、Data Lake)、Node.js、SQL Server 工具、[適用於 Visual Studio Code 的 Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用和運行它      | 桌面快捷方式`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`（ 。 使用桌面圖示或 **"開始"** 功能表以圖形方式打開視覺化工作室。 搜尋程式 (Windows 標誌鍵 + S)，後面接著 **Visual Studio**。 在該處，您可以使用像是 C#、Python、R 及 Node.js 等語言來建立專案。   |
| DSVM 的相關工具      |     Visual Studio Code、RStudio、Juno  |

> [!NOTE]
> 您可能會收到一則訊息，表示您的評估期間已過期。 請輸入您的 Microsoft 帳戶認證。 或建立新的免費帳戶，以取得 Visual Studio Community 的存取權。

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 通用 IDE      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 程式碼編輯器和 Git 整合   |
| 如何使用和運行它      | 桌面快捷方式`C:\Program Files (x86)\Microsoft VS Code\Code.exe`（ ） 在 Windows、`code`桌面快捷方式或終端 （ ） 在 Linux 中    |
| DSVM 的相關工具      |     視覺工作室， RStudio， 朱諾  |

## <a name="rstudio-desktop"></a>RStudio 桌面

|    |           |
| ------------- | ------------- |
| 這是什麼？   | R 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用和運行它      | 桌面快捷方式`C:\Program Files\RStudio\bin\rstudio.exe`（ ） 在 Windows`/usr/bin/rstudio`上， 桌面快捷方式 （ ） 在 Linux 上      |
| DSVM 的相關工具      |   視覺工作室， 視覺工作室代碼， 朱諾      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| 這是什麼？   | R 語言的用戶端 IDE   |
| 這是什麼？   | 適用於 R 的網頁型 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用和運行它      | 啟用服務與_系統ctl啟用rstudio-伺服器_，然後啟動服務與_系統ctl啟動rstudio-伺服器_。 然後登錄到 RStudio 伺服器，網址為\/HTTP： /您的 vm-ip：8787。       |
| DSVM 的相關工具      |   視覺工作室，視覺工作室代碼，RStudio桌面      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Julia 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  Julia 開發     |
| 如何使用和運行它      | 桌面快捷方式`C:\JuliaPro-0.5.1.1\Juno.bat`（ ） 在 Windows`/opt/JuliaPro-VERSION/Juno`上， 桌面快捷方式 （ ） 在 Linux 上      |
| DSVM 的相關工具      |   視覺工作室， 視覺工作室代碼， RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Python 語言的用戶端 IDE    |
| 支援的 DSVM 版本      | 視窗 2019， Linux      |
| 典型的使用案例      |  Python 開發     |
| 如何使用和運行它      | Windows 上的`C:\Program Files\tk`桌面快捷方式 （ ） Linux 上的`/usr/bin/pycharm`桌面快捷方式 （ ）      |
| DSVM 的相關工具      |   視覺工作室， 視覺工作室代碼， RStudio      |
