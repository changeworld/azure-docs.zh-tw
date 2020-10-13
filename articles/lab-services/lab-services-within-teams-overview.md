---
title: Microsoft 小組內的 Azure 實驗室服務
description: 概述如何在 Microsoft 小組內使用 Azure 實驗室服務。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 8b70ab3cbf1a342646f6a2897550c4bf8f7601a7
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946621"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft 小組內的 Azure 實驗室服務

Azure 實驗室服務擁有者/建立者現在可以在 Microsoft 小組中建立實驗室。

本文將概述小組整合的優點，並提供其他文章的連結，以取得如何在小組內管理 Azure 實驗室服務的指示。 

## <a name="benefits"></a>優點

Azure Lab Services 與 Microsoft 團隊的整合可協助教師設定課堂環境，並在小組內提供虛擬實驗室環境 (類別) ： 

* 教師可以設定教室實驗室，讓學生可以從小組內部存取其 Vm，而不需要離開小組，也不需要流覽至 [Azure 實驗室服務網站](https://labs.azure.com)。
* 單一登入 (從小組到 Azure 實驗室服務的 SSO) 。
* 小組和實驗室擁有者不需要在兩個不同的系統中維護類別名冊-實驗室使用者清單是從小組成員資格自動填入，且每隔24小時會自動執行一次同步處理。 
* 初始發佈範本 VM 之後，實驗室容量 (也就是實驗室中的 Vm 數目) 會根據小組成員資格的使用者新增/刪除，自動調整。 
* 小組和實驗室擁有者只會查看與小組相關的實驗室，而學生只會查看針對特定小組布建的 Vm。 
* 系統會將使用者自動註冊至實驗室，並在實驗室發佈之後，于第一次登入時自動指派 Vm。 教育工作者不需要傳送邀請，且學生不需要另外註冊實驗室。  

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [開始使用並從小組建立實驗室服務實驗室](how-to-get-started-create-lab-within-teams.md)
- [從小組管理實驗室服務使用者清單](how-to-manage-user-lists-within-teams.md)
- [從小組管理實驗室服務中的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [從小組建立實驗室服務排程](how-to-create-schedules-within-teams.md)
- [從小組存取實驗室服務中的 VM (student view) ](how-to-access-vm-for-students-within-teams.md)
