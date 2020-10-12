---
title: 開發工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解資料科學虛擬機器上提供的工具和整合式開發環境。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42a595246f45b2c7423aaf77c95f17243cf8813c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012378"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器上的開發工具

資料科學虛擬機器 (DSVM) 將數個熱門工具組合在高生產力的整合式開發環境中， (IDE) 。 以下是 DSVM 提供的一些工具。

## <a name="visual-studio-community-edition"></a>Visual Studio Community 版本

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows： Visual Studio 2017、Windows 2019： Visual Studio 2019      |
| 典型的使用案例      | 軟體發展    |
| 如何在 DSVM 上設定和安裝？      | 資料科學工作負載 (Python 和 R 工具)、Azure 工作負載 (Hadoop、Data Lake)、Node.js、SQL Server 工具、[適用於 Visual Studio Code 的 Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用和執行      |  () 的桌面快捷方式 `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe` 。 使用桌面圖示或 [ **開始** ] 功能表，以圖形方式開啟 Visual Studio。 搜尋程式 (Windows 標誌鍵 + S)，後面接著 **Visual Studio**。 在該處，您可以使用像是 C#、Python、R 及 Node.js 等語言來建立專案。   |
| DSVM 上的相關工具      |     Visual Studio Code、RStudio、Juno  |

> [!NOTE]
> 您可能會收到一則訊息，表示您的評估期間已過期。 請輸入您的 Microsoft 帳戶認證。 或建立新的免費帳戶，以取得 Visual Studio Community 的存取權。

## <a name="visual-studio-code"></a>Visual Studio Code 

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 程式碼編輯器和 Git 整合   |
| 如何使用和執行      | Windows 中的桌面快捷方式 (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) 、桌面快捷方式或終端機 (`code`) 在 Linux 中    |
| DSVM 上的相關工具      |     Visual Studio、RStudio、Juno journey  |

## <a name="rstudio-desktop"></a>RStudio Desktop

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用和執行      | Windows 上的桌面快捷方式 (`C:\Program Files\RStudio\bin\rstudio.exe`) ， `/usr/bin/rstudio` 在 Linux 上的桌面快捷方式 ()       |
| DSVM 上的相關工具      |   Visual Studio、Visual Studio Code、Juno journey      |

## <a name="rstudio-server"></a>RStudio Server

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 這是什麼？   | 適用於 R 的網頁型 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用和執行      | 使用 _systemctl 啟用服務，啟用 rstudio-伺服器_，然後使用 _systemctl 啟動 rstudio-伺服器_啟動服務。 然後以 HTTP：/your-vm-ip：8787登入 RStudio 伺服器 \/ 。       |
| DSVM 上的相關工具      |   Visual Studio、Visual Studio Code、RStudio 桌面      |

## <a name="juno"></a>Juno 

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | Julia 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  Julia 開發     |
| 如何使用和執行      | Windows 上的桌面快捷方式 (`C:\JuliaPro-0.5.1.1\Juno.bat`) ， `/opt/JuliaPro-VERSION/Juno` 在 Linux 上的桌面快捷方式 ()       |
| DSVM 上的相關工具      |   Visual Studio、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | Python 語言的用戶端 IDE    |
| 支援的 DSVM 版本      | Windows 2019、Linux      |
| 典型的使用案例      |  Python 開發     |
| 如何使用和執行      | Windows 上的桌面快捷方式 (`C:\Program Files\tk`) 。 Linux 上的桌面快捷方式 (`/usr/bin/pycharm`)       |
| DSVM 上的相關工具      |   Visual Studio、Visual Studio Code、RStudio      |
