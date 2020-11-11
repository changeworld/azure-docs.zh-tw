---
title: Azure 實驗室服務的範例課程類型 | Microsoft Docs
description: 提供一些您可以使用 Azure 實驗室服務為其設定實驗室的課程類型。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c2caac35125c3f69c2257750bac417971af5dbd
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490979"
---
# <a name="class-types-overview---azure-lab-services"></a>課程類型概觀 - Azure 實驗室服務

Azure 實驗室服務可讓您在雲端中快速設定教室實驗室環境。 本節中的文章提供如何使用 Azure 實驗室服務設定數種課堂實驗室的指引。

## <a name="big-data-analytics"></a>巨量資料分析
您可以設定 GPU 實驗室，以教授大型資料分析類別。 藉由這種類型的課程，學員將瞭解如何處理大量資料，並套用機器和統計學習演算法來衍生資料見解。 學生的主要目標是瞭解如何使用資料分析工具，例如 Apache Hadoop 的開放原始碼軟體套件，它提供儲存、管理及處理大量資料的工具。 

如需有關如何設定此類實驗室的詳細資訊，請參閱 [使用 HortonWorks Data Platform 的 Docker 部署設定適用于大型資料分析的實驗室](class-type-big-data-analytics.md)。

## <a name="database-management"></a>資料庫管理
資料庫概念是大專院校大部分的電腦科學系所會教授的其中一門入門課程。 您可以在 Azure 實驗室服務中設定適用於基本資料庫管理課程的實驗室。 例如，您可以在具有 [MySQL](https://www.mysql.com/) 資料庫伺服器或 [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 伺服器的實驗室中設定虛擬機器範本。

如需如何設定此類型實驗室的詳細資訊，請參閱[設定實驗室來教授關聯式資料庫的資料庫管理](class-type-database-management.md)。

## <a name="deep-learning-in-natural-language-processing"></a>自然語言處理中的深度學習
您可以使用 Azure 實驗室服務，設定以自然語言處理 (NLP) 中的深度學習為焦點的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。 參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。

如需如何設定此類實驗室的詳細資訊，請參閱[設定以自然語言處理中的深度學習為焦點的實驗室](class-type-deep-learning-natural-language-processing.md)。

## <a name="ethical-hacking"></a>道德入侵
您可以針對專注在道德入侵之鑑識調查方面的課程設定實驗室。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩個巢狀虛擬機器，其中一個具有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像，另一個則具有 [Kali Linux](https://www.kali.org/) 映像。 Metasploitable 虛擬機器可運用於惡意探索。  Kali Linux 虛擬機器則可供存取執行鑑識工作所需的工具。

如需如何設定此類實驗室的詳細資訊，請參閱[設定實驗室以指導道德入侵課程](class-type-ethical-hacking.md)。

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html)（代表矩陣實驗室）是 [MathWorks](https://www.mathworks.com/)的程式設計平臺。  它結合了運算能力和視覺效果，使其成為數學、工程、物理和化學領域中的熱門工具。

如需有關如何設定此類實驗室的詳細資訊，請參閱設定 [實驗室以教授 MATLAB](class-type-matlab.md)。

## <a name="project-lead-the-way-pltw"></a>專案負責人 (PLTW 的方式) 
[專案負責人的 (PLTW) ](https://www.pltw.org/) 是一種非贏利組織，在美國的電腦科學、工程和生物醫學科學中提供 PreK-12 的課程。  在每個 PLTW 類別中，學生都使用各種軟體應用程式作為實際操作學習體驗的一部分。

如需有關如何設定這些實驗室類型的詳細資訊，請參閱以 [類別的方式為專案負責人設定實驗室](class-type-pltw.md)。

## <a name="python-and-jupyter-notebooks"></a>Python 和 Jupyter 筆記本
您可以在 Azure 實驗室服務中使用所需工具設定範本機器，以教授學生如何使用 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io)。 Jupyter Notebook 是開放原始碼專案，可讓您輕鬆地在稱為筆記本的單一畫布上結合 RTF 和可執行的 [Python](https://www.python.org/) 原始程式碼。 執行筆記本會產生線性的輸入和輸出記錄。  這些輸出可以包含文字、資訊資料表、散佈圖等等。

如需如何設定此類實驗室的詳細資訊，請參閱[使用 Python 和 Jupyter Notebook 設定實驗室來教授資料科學](class-type-jupyter-notebook.md)。

## <a name="shell-scripting-on-linux"></a>Linux 上的殼層指令碼
您可以設定實驗室來教授 Linux 上的殼層指令碼。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

如需如何設定此類實驗室的詳細資訊，請參閱 [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)。

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks 電腦輔助設計 (CAD)
您可以設定 GPU 實驗室，讓工程學生存取 [SolidWorks](https://www.solidworks.com/)。  SolidWorks 提供 3D CAD 環境來模型化穩固的物件。  透過 SolidWorks，工程師可以輕鬆地建立、視覺化、模擬及記錄其設計。

如需如何設定此類實驗室的詳細資訊，請參閱 [使用 SolidWorks 設定工程類別的實驗室](class-type-solidworks.md)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [使用 Azure 實驗室服務，設定以深度學習自然語言處理為主的實驗室](class-type-deep-learning-natural-language-processing.md)
- [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)
- [道德入侵](class-type-ethical-hacking.md)