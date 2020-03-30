---
title: 設置實驗室，使用 Python 和 Jupyter 筆記本教授資料科學 |微軟文檔
description: 瞭解如何設置實驗室，使用 Python 和 Jupyter 筆記本教授資料科學。
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444106"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>設置實驗室，使用 Python 和 Jupyter 筆記本教授資料科學

本文概述了如何在實驗室服務中設置範本電腦，並配備教學生如何使用[Jupyter 筆記本](http://jupyter-notebook.readthedocs.io)所需的工具。  Jupyter Notebook 是開放原始碼專案，可讓您輕鬆地在稱為筆記本的單一畫布上結合 RTF 和可執行的 [Python](https://www.python.org/) 原始程式碼。  執行筆記本會產生線性的輸入和輸出記錄。  這些輸出可以包含文字、資訊資料表、散佈圖等等。

## <a name="lab-configuration"></a>實驗室組態

要設置此實驗，需要 Azure 訂閱和實驗室帳戶才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲取 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶。 有關創建新實驗室帳戶的詳細資訊，請參閱[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。  您還可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設置

為實驗室帳戶啟用下表中描述的設置。 有關如何啟用市場映射的詳細資訊，請參閱[指定實驗室建立者可用的應用商店映射](specify-marketplace-images.md)。

| 實驗室帳戶設置 | Instructions |
| ------------------- | ------------ |
| 市場映射 | 啟用[資料科學虛擬機器 - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映射，以便在實驗室帳戶中使用。 |

>[!TIP]
>本文將重點介紹使用 Windows Server 作業系統的範本電腦。  還可以使用[資料科學虛擬機器為 Linux （CentOS）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm)或資料科學虛擬機器為 Azure 應用商店中的[Linux （Ubuntu）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)映射設置資料科學類。

### <a name="lab-settings"></a>實驗室設置

設置教室實驗室時，請使用下表中的設置。  有關如何創建教室實驗室的詳細資訊，請參閱[設置教室實驗室教程](tutorial-setup-classroom-lab.md)。

| 實驗室設置 | 值/說明 |
| ------------ | ------------------ |
|虛擬機器大小| 小型 GPU（計算）。 此大小最適合計算密集型和網路密集型應用，如人工智慧和深度學習。 |
|虛擬機器映像| 資料科學虛擬機器 - Windows 2016|

## <a name="template-machine"></a>範本機

[資料科學虛擬機器 - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)映射提供了此類課程所需的必要深度學習框架和工具。  該圖像包括猶太筆記本和視覺工作室代碼。  [Jupyter 筆記本](http://jupyter-notebook.readthedocs.io)是一個 Web 應用程式，它允許資料科學家獲取原始資料、運行計算並在相同的環境中查看結果。  對於我們的範本電腦，Web 應用程式將在本地運行。  [Visual Studio Code](https://code.visualstudio.com/)是一種 IDE，在編寫和測試筆記本時提供了豐富的互動式體驗。  有關詳細資訊，請參閱[在視覺化工作室代碼中使用 Jupyter 筆記本](https://code.visualstudio.com/docs/python/jupyter-support)。

設置類的剩餘任務是提供本地筆記本。  有關如何使用 Azure 機器學習示例的說明，請參閱[如何使用 Jupyter 筆記本配置環境](../../machine-learning/how-to-configure-environment.md#jupyter)。  您還可以在範本電腦上提供您自己的筆記本。  發佈範本時，筆記本將複製到所有學生電腦。

## <a name="cost-estimate"></a>成本估算

讓我們來介紹此類的可能成本估算。  我們將使用一個25名學生的班級。  有 20 小時的計畫上課時間。  此外，每個學生在計畫上課時間之外的家庭作業或作業將獲得 10 小時的配額。  我們選擇的虛擬機器大小是小型 GPU（計算），即 139 個實驗室單元。

下面是此類的可能成本估算的示例：

25\*名學生（20 個預定小時 = \* 10 個配額\*小時） 139 個實驗室單位每小時 0.01 USD = 1042.5 USD

有關定價的更多詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

在本文中，我們介紹了為 Jupyter 筆記本類創建實驗室的步驟。 您可以將類似的設置用於其他機器學習類。

## <a name="next-steps"></a>後續步驟

後續步驟是設置任何實驗室的常見步驟。

- [創建和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)
