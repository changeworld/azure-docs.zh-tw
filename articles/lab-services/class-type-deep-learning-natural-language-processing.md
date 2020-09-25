---
title: 使用 Azure 實驗室服務設定以深度學習為主的實驗室 | Microsoft Docs
description: 瞭解如何使用 Azure 實驗室服務，設定以自然語言處理 (NLP) 的深度學習為焦點的實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251451"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 實驗室服務，設定以深度學習自然語言處理為主的實驗室
本文將說明如何使用 Azure 實驗室服務，設定以深度學習自然語言處理 (NLP) 為主的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。  

參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。 

## <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要 Azure 訂用帳戶才能開始進行。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 擁有 Azure 訂用帳戶之後，您就可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的實驗室帳戶。 請參閱下列教學課程來建立新的實驗室帳戶：[設定實驗室帳戶的教學課程](tutorial-setup-lab-account.md)。
 
建立實驗室帳戶之後，請在實驗室帳戶中啟用下列設定： 

| 實驗室帳戶設定 | Instructions |
| ----------- | ------------ |  
| Marketplace 映像 | 啟用適用於 Linux (Ubuntu) 的資料科學虛擬機器映像，以在實驗室帳戶中使用。  如需指示，請參閱下列文章：[指定實驗室建立者可用的 Marketplace 映像](specify-marketplace-images.md)。 | 

請遵循[本教學課程](tutorial-setup-classroom-lab.md)來建立新的實驗室，並套用下列設定：

| 實驗室設定 | 值/指示 | 
| ------------ | ------------------ |
| 虛擬機器 (VM) 大小 | **小型 GPU (計算) **。 此大小最適用於計算密集型及網路密集型應用程式，例如人工智慧及深度學習。 |
| VM 映像 | [適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。 此映像提供機器學習服務和資料科學的深度學習架構和工具。 若要查看此映像上已安裝工具的完整清單，請參閱下列文章：[DSVM 包含哪些內容？](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| 啟用遠端桌面連線 | <p>資料科學映像已設為使用 X2Go，讓教師和學生皆可使用 GUI 遠端桌面進行連線。  X2Go *不*需要啟用 [啟用遠端桌面連線] 設定。  只有在您選擇改用 RDP 時，才需要啟用此設定。

>**重要**：雖然我們建議搭配使用 X2Go 與資料科學映像，但如果您選擇改用 RDP，則需要在第一次使用時，以 SSH 連線到 Linux VM 並安裝 RDP 和 GUI 套件。  然後，您/學生可以稍後使用 RDP 連線到 Linux VM。  如需詳細資訊，請參閱[為 Linux VM 啟用圖形化遠端桌面](how-to-enable-remote-desktop-linux.md)。

適用於 Linux 的資料科學虛擬機器映像會提供這類課程所需的必要深度學習架構和工具。 因此，範本機器建立之後，您就不需要進一步進行自訂。 其可以發佈給學生使用。 在範本頁面上選取 [發佈] 按鈕，即可將範本發佈至實驗室。  

## <a name="cost"></a>成本
如果您想要估計此實驗室的成本，可以使用下列範例： 

針對 25 名學生的課程，其中 20 小時是已排定課程的時間，10 小時是家庭作業或指派工作的配額，此實驗室的價格為：25 個學生 * (20 + 10) 小時 * 139 個實驗室單位 * 0.01 美元/小時 = 1042.5 美元

如需定價的詳細資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文已逐步引導您建立自然語言處理課程的實驗室。 您可以針對其他深度學習課程使用類似的設定。

## <a name="next-steps"></a>後續步驟
接下來是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 

