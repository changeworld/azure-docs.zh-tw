---
title: 使用 Azure 實驗室服務設置側重于深度學習的實驗室 |微軟文檔
description: 瞭解如何設置實驗室在 Linux 上教授 shell 腳本。
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
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109390"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 實驗室服務，設定以自然語言處理中的深度學習為焦點的實驗室
本文介紹如何使用 Azure 實驗室服務設置側重于自然語言處理 （NLP） 深度學習的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。  

參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。 

## <a name="lab-configuration"></a>實驗室組態
要設置此實驗，需要 Azure 訂閱才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲得 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶，也可以使用現有的實驗室帳戶。 有關創建新實驗室帳戶，請參閱以下教程：[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。
 
創建實驗室帳戶後，在實驗室帳戶中啟用以下設置： 

| 實驗室帳戶設置 | Instructions |
| ----------- | ------------ |  
| 市場映射 | 啟用適用于 Linux （Ubuntu） 映射的資料科學虛擬機器，以便在實驗室帳戶中使用。  有關說明，請參閱以下文章：[指定可供實驗室建立者使用的市場映射](specify-marketplace-images.md)。 | 

請按照[本教程](tutorial-setup-classroom-lab.md)創建新實驗室並應用以下設置：

| 實驗室設置 | 值/說明 | 
| ------------ | ------------------ |
| 虛擬機器 （VM） 大小 | 小型 GPU（計算）。 此大小最適合計算密集型和網路密集型應用，如人工智慧和深度學習。 |
| VM 映射 | [用於 Linux （Ubuntu） 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 此圖像為機器學習和資料科學提供了深度學習框架和工具。 要查看此映射上已安裝工具的完整清單，請參閱以下文章[：DSVM 上包含哪些內容？](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| 啟用遠端桌面連線 | 啟用。 <p>啟用此設置將允許教師和學生使用遠端桌面 （RDP） 連接到虛擬機器 （VM）。</p><p>**重要提示**：RDP 已在用於 Linux 映射的資料科學虛擬機器上安裝和配置。 因此，教師/學生可以通過 RDP 連接到 VM，而無需執行任何其他步驟。 此外，如果您需要連接到圖形桌面，此映射已在虛擬機器上安裝[了 X2Go 伺服器](https://wiki.x2go.org/doku.php/doc:newtox2go)。 學生必須在其本地電腦上安裝 X2Go 用戶端，並且必須使用用戶端進行連接。 如需詳細資訊，請參閱下列指南： <ul><li>[如何存取 Linux 適用的資料科學虛擬機器](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[連接到範本 VM 以安裝 RDP 和 GUI 包](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

用於 Linux 映射的資料科學虛擬機器提供了此類課程所需的必要深度學習框架和工具。 因此，在範本電腦創建後，無需進一步自訂它。 它可以發佈供學生使用。 選擇範本頁上的 **"發佈"** 按鈕，將範本發佈到實驗室。  

## <a name="cost"></a>成本
如果要估計本實驗的成本，可以使用以下示例： 

對於有 25 小時的預定上課時間和 10 小時作業或作業配額的班級，實驗室的價格為 - 25 名學生 = （20 + 10） 小時 = 139 實驗室單位 = 每小時 0.01 USD = 1042.5 USD

有關定價的更多詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文將引導您完成為自然語言處理類創建實驗室的步驟。 您可以將類似的設置用於其他深度學習課程。

## <a name="next-steps"></a>後續步驟
設置任何實驗室通常採取以下步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)。 

