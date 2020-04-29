---
title: Azure 實驗室服務的範例課程類型 | Microsoft Docs
description: 提供一些您可以使用 Azure 實驗室服務為其設定實驗室的課程類型。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79296722"
---
# <a name="class-types-overview---azure-lab-services"></a>課程類型概觀 - Azure 實驗室服務

Azure 實驗室服務可讓您在雲端中快速設定教室實驗室環境。 本節中的文章提供如何使用 Azure 實驗室服務設定數種課堂實驗室的指引。

## <a name="deep-learning-in-natural-language-processing"></a>自然語言處理中的深度學習

您可以使用 Azure 實驗室服務，設定以自然語言處理 (NLP) 中的深度學習為焦點的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。 參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。

如需如何設定此類實驗室的詳細資訊，請參閱[設定以自然語言處理中的深度學習為焦點的實驗室](class-type-deep-learning-natural-processing.md)。

## <a name="shell-scripting-on-linux"></a>Linux 上的殼層指令碼

您可以設定實驗室來教授 Linux 上的殼層指令碼。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

如需如何設定此類實驗室的詳細資訊，請參閱 [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)。

## <a name="ethical-hacking"></a>道德入侵

您可以針對專注在道德入侵之鑑識調查方面的課程設定實驗室。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩個巢狀虛擬機器，其中一個具有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像，另一個則具有 [Kali Linux](https://www.kali.org/) 映像。 Metasploitable 虛擬機器可運用於惡意探索。  Kali Linux 虛擬機器則可供存取執行鑑識工作所需的工具。

如需如何設定此類實驗室的詳細資訊，請參閱[設定實驗室以指導道德入侵課程](class-type-ethical-hacking.md)。

## <a name="database-management"></a>資料庫管理
資料庫概念是大專院校大部分的電腦科學系所會教授的其中一門入門課程。 您可以在 Azure 實驗室服務中設定適用於基本資料庫管理課程的實驗室。 例如，您可以在具有 [MySQL](https://www.mysql.com/) 資料庫伺服器或 [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 伺服器的實驗室中設定虛擬機器範本。

如需如何設定此類型實驗室的詳細資訊，請參閱[設定實驗室來教授關聯式資料庫的資料庫管理](class-type-database-management.md)。

## <a name="python-and-jupyter-notebooks"></a>Python 和 Jupyter 筆記本
您可以在 Azure 實驗室服務中使用所需工具設定範本機器，以教授學生如何使用 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io)。 Jupyter Notebook 是開放原始碼專案，可讓您輕鬆地在稱為筆記本的單一畫布上結合 RTF 和可執行的 [Python](https://www.python.org/) 原始程式碼。 執行筆記本會產生線性的輸入和輸出記錄。  這些輸出可以包含文字、資訊資料表、散佈圖等等。

如需如何設定此類實驗室的詳細資訊，請參閱[使用 Python 和 Jupyter Notebook 設定實驗室來教授資料科學](class-type-jupyter-notebook.md)。

## <a name="mobile-app-development-with-android-studio"></a>使用 Android Studio 進行行動應用程式開發
您可以在 Azure 實驗室服務中設定實驗室，以教授入門的行動應用程式開發課程。 此課程著重於可發佈至 [Google Play 商店](https://play.google.com/store/apps)的 Android 行動應用程式。  學生會了解如何使用 [Android Studio](https://developer.android.com/studio) 來建置應用程式。  [Visual Studio 的 Android 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)可用來在本機測試應用程式。

如需如何設定此類實驗室的詳細資訊，請參閱[使用 Android Studio 設定實驗室來教授行動應用程式開發](class-type-mobile-dev-android-studio.md)。


## <a name="next-steps"></a>後續步驟

查看下列文章：

- [使用 Azure 實驗室服務，設定以自然語言處理中的深度學習為焦點的實驗室](class-type-deep-learning-natural-processing.md)
- [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)
- [道德入侵](class-type-ethical-hacking.md)