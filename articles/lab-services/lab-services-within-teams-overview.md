---
title: Microsoft 小組內的 Azure 實驗室服務
description: 概述如何在 Microsoft 小組內使用 Azure 實驗室服務。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433918"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft 小組內的 Azure 實驗室服務

您可以使用 **Azure 實驗室服務** 小組應用程式，在 Microsoft 小組內利用 Azure 實驗室服務。 擁有實驗室帳戶擁有者/參與者/creator 存取權的任何小組擁有者都可以建立實驗室，並將虛擬機器布建到小組的每個人。

本文概述在小組內使用 Azure 實驗室服務的優點，並提供其他文章的連結，以取得如何在小組內建立及管理實驗室的指示。 

> [!NOTE]
>**Azure 實驗室服務** 小組應用程式只能新增至小組，不能新增至個別聊天或群組聊天室。

## <a name="benefits"></a>優點

Azure Lab Services 與 Microsoft 團隊的整合可協助教師設定課堂環境，並在小組內提供虛擬實驗室環境 (類別) ： 

* 教師可以設定實驗室，讓學生可以從小組內部存取其 Vm，而不需要離開小組，也不需要流覽至 [Azure 實驗室服務網站](https://labs.azure.com)。
* 單一登入 (從小組到 Azure 實驗室服務的 SSO) 。
* 小組和實驗室擁有者不需要在兩個不同的系統中維護類別名冊-實驗室使用者清單是從小組成員資格自動填入，且每隔24小時會自動執行一次同步處理。 
* 初始發佈範本 VM 之後，實驗室容量 (也就是實驗室中的 Vm 數目) 會根據小組成員資格的使用者新增/刪除，自動調整。 
* 小組和實驗室擁有者只會查看與小組相關的實驗室，而學生只會查看針對特定小組布建的 Vm。 
* 系統會將使用者自動註冊至實驗室，並在實驗室發佈之後，于第一次登入時自動指派 Vm。 教育工作者不需要傳送邀請，且學生不需要另外註冊實驗室。  

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [開始使用並在小組內建立實驗室](how-to-get-started-create-lab-within-teams.md)
- [管理小組內的實驗室使用者清單](how-to-manage-user-lists-within-teams.md)
- [管理小組內實驗室的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [建立及管理小組內的實驗室排程](how-to-create-schedules-within-teams.md)
- [存取小組內的 VM –學生版視圖](how-to-access-vm-for-students-within-teams.md)
