---
title: 使用 Azure 實驗室服務設定以深度學習為焦點的實驗室 |Microsoft Docs
description: 瞭解如何設定實驗室來教授 Linux 上的 shell 腳本。
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
ms.openlocfilehash: 0dab5f3dcdfb6ddabbd94960dcf8a8bf2bce98af
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890763"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>使用 Azure 實驗室服務，設定以自然語言處理中的深度學習為焦點的實驗室
本文說明如何使用 Azure 實驗室服務，設定以自然語言處理（NLP）深度學習為焦點的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。  

參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。 

## <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要 Azure 訂用帳戶才能開始使用。 如果您沒有 Azure 訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/)，再開始進行。 擁有 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的實驗室帳戶。 請參閱下列教學課程，以建立新的實驗室帳戶：[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。
 
建立實驗室帳戶之後，請啟用實驗室帳戶中的下列設定： 

| 實驗室帳戶設定 | Instructions |
| ----------- | ------------ |  
| Marketplace 映射 | 啟用資料科學虛擬機器 for Linux （Ubuntu）映射以在實驗室帳戶中使用。  如需相關指示，請參閱下列文章：[指定可供實驗室建立者使用的 marketplace 映射](specify-marketplace-images.md)。 | 

遵循[此教學](tutorial-setup-classroom-lab.md)課程來建立新的實驗室，並套用下列設定：

| 實驗室設定 | 值/指示 | 
| ------------ | ------------------ |
| 虛擬機器（VM）大小 | 小型 GPU （計算）。 此大小最適合用於計算密集型和網路密集型應用程式，例如人工智慧和深度學習。 |
| VM 映射 | [適用于 Linux （Ubuntu）的資料科學虛擬機器](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。 此映射提供適用于機器學習和資料科學的深度學習架構和工具。 若要查看此映射上已安裝工具的完整清單，請參閱下列文章： [DSVM 中包含的內容？](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)。 |
| 啟用遠端桌面連線 | <p>啟用此設定可讓教師和學生使用遠端桌面（RDP）連接到其虛擬機器（VM）。</p><p>**重要**：啟用此設定只會在 Linux 電腦上開啟**RDP**埠。 如果已在虛擬機器映射上安裝並設定 RDP，您/學生可以透過 RDP 連線到 Vm，而不需要執行任何額外的步驟。 <p>如果 VM 映射未安裝和設定 RDP，您必須在第一次使用 SSH 連線到 Linux 機器，並安裝 RDP 和 GUI 套件，讓您/學生可以稍後再使用 RDP 連線到 Linux 電腦。 如需詳細資訊，請參閱[安裝和設定遠端桌面以連接至 Azure 中的 LINUX VM](../../virtual-machines/linux/use-remote-desktop.md)。 然後，您可以發行映射，讓學生可以透過 RDP 連線到學生的 Linux Vm。  |

適用于 Linux 的資料科學虛擬機器映射會提供這類類別所需的必要深度學習架構和工具。 因此，範本機器建立之後，您就不需要進一步自訂它。 它可以發佈供學生使用。 選取 [範本] 頁面上的 [**發佈**] 按鈕，將範本發佈至實驗室。  

## <a name="cost"></a>成本
如果您想要估計此實驗室的成本，可以使用下列範例： 

針對25名學生的課程，其中有20小時的排程類別時間和10小時的家庭作業或指派配額，實驗室的價格為-25 個學生 * （20 + 10）小時 * 139 個實驗室單位 * 0.01 美元/小時 = 1042.5 美元

如需更多有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文逐步引導您建立自然語言處理類別的實驗室。 您可以針對其他深度學習類別使用類似的設定。

## <a name="next-steps"></a>後續步驟
下一步是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 

