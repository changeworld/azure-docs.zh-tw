---
title: 設定實驗室以使用 Python 和 Jupyter 筆記本來教授資料科學 |Microsoft Docs
description: 瞭解如何設定實驗室，以使用 Python 和 Jupyter 筆記本來教授資料科學。
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
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257720"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>設定實驗室以使用 Python 和 Jupyter 筆記本來教授資料科學

本文概述如何在實驗室服務中設定範本機器，以及讓學生學習如何使用[Jupyter 筆記本](http://jupyter-notebook.readthedocs.io)所需的工具。  Jupyter Notebook 是開放原始碼專案，可讓您輕鬆地在稱為筆記本的單一畫布上結合 RTF 和可執行的 [Python](https://www.python.org/) 原始程式碼。  執行筆記本會產生線性的輸入和輸出記錄。  這些輸出可以包含文字、資訊資料表、散佈圖等等。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。  您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需如何啟用 marketplace 映射的詳細資訊，請參閱[指定可供實驗室建立者使用的 marketplace 映射](specify-marketplace-images.md)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
| Marketplace 映射 | 啟用[資料科學虛擬機器-Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)映射，以便在您的實驗室帳戶中使用。 |

>[!TIP]
>本文將著重于設定使用 Windows Server 作業系統的範本機器。  您也可以使用 Azure Marketplace 中的[資料科學虛擬機器 For Linux （Ubuntu）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)影像，設定具有 Python 和 Jupyter 筆記本的資料科學類別。

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。  如需如何建立教室實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| 小型 GPU （計算）。 此大小最適合用於計算密集型和網路密集型應用程式，例如人工智慧和深度學習。 |
|虛擬機器映像| 資料科學虛擬機器-Windows 2016|

## <a name="template-machine"></a>範本機器

[資料科學虛擬機器 Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)映射提供這類類別所需的必要深度學習架構和工具。  映射包含 Jupyter 筆記本和 Visual Studio Code。  [Jupyter 筆記本](http://jupyter-notebook.readthedocs.io)是一種 web 應用程式，可讓資料科學家採用原始資料、執行計算，並在相同的環境中查看結果。  針對我們的範本機器，web 應用程式將會在本機執行。  [Visual Studio Code](https://code.visualstudio.com/)是在撰寫和測試筆記本時提供豐富互動體驗的 IDE。  如需詳細資訊，請參閱[在 Visual Studio Code 中使用 Jupyter 筆記本](https://code.visualstudio.com/docs/python/jupyter-support)。

設定類別的其餘工作是提供本機筆記本。  如需如何使用 Azure Machine Learning 範例的指示，請參閱[如何設定具有 Jupyter 筆記本的環境](../../machine-learning/how-to-configure-environment.md#jupyter)。  您也可以在範本機器上提供自己的筆記本。  發佈範本時，會將筆記本複製到所有學生電腦。

## <a name="cost-estimate"></a>成本預估

讓我們來討論這個類別的可能成本預估。  我們將使用25名學生的課程。  已排程的類別時間有20小時。  此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。  我們選擇的虛擬機器大小為小型 GPU （計算），也就是139實驗室單位。

以下是此類別的可能成本預估範例：

25名\*學生（20個已排程的時數\* + 10 個\*配額小時）139個實驗室單位每小時0.01 美元 = 1042.5 美元

如需更多有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

在本文中，我們逐步解說了建立 Jupyter 筆記本類別的實驗室的步驟。 您可以針對其他機器學習類別使用類似的設定。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
