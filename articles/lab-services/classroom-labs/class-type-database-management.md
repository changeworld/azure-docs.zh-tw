---
title: 設置實驗室以教授關係資料庫的資料庫管理 |微軟文檔
description: 瞭解如何設置實驗室來教授關係資料庫的管理。
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469913"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>設置實驗室以教授關係資料庫的資料庫管理

本文介紹如何在 Azure 實驗室服務中為基本資料庫管理類設置實驗室。 資料庫概念是大專院校大部分的電腦科學系所會教授的其中一門入門課程。 結構化查詢語言 （SQL） 是一種國際標準。 SQL 是關係資料庫管理的標準語言，包括添加、訪問和管理資料庫中的內容。  它以其快速的處理、經驗證的可靠性、易用性和使用靈活性而聞名。

在本文中，我們將演示如何在具有 MySQL 資料庫伺服器和 SQL Server 2019 伺服器的實驗室中設置虛擬機器範本。  [MySQL](https://www.mysql.com/)是一個免費提供的開源關係資料庫管理系統 （RDBMS）。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)是微軟 RDBMS 的最新版本。

## <a name="lab-configuration"></a>實驗室組態

要設置此實驗，需要 Azure 訂閱和實驗室帳戶才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲取 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶。 有關創建新實驗室帳戶的詳細資訊，請參閱[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。  您還可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設置

為實驗室帳戶啟用下表中描述的設置。 有關如何啟用市場映射的詳細資訊，請參閱[指定可供實驗室建立者使用的市場映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 實驗室帳戶設置 | Instructions |
| ------------------- | ------------ |
|市場映射| 啟用"Windows 伺服器 2019 標準 2019 標準"映射，以便在您的實驗室帳戶中使用。|

### <a name="lab-settings"></a>實驗室設置

設置教室實驗室時，請使用下表中的設置。  有關如何創建教室實驗室的詳細資訊，請參閱[設置教室實驗室教程](tutorial-setup-classroom-lab.md)。

| 實驗室設置 | 值/說明 |
| ------------ | ------------------ |
|虛擬機器大小| 中。 此大小最適合關係資料庫、記憶體緩存和分析。|
|虛擬機器映像| SQL 伺服器 2019 標準在 Windows 伺服器 2019|

## <a name="template-machine-configuration"></a>範本機配置

要在 Windows 伺服器 2019 上安裝 MySQL，可以按照在[虛擬機器上安裝和運行 MySQL 社區伺服器](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)中所述的步驟。

SQL Server 2019 預先安裝在創建新實驗室時選擇的虛擬機器映射中。

## <a name="cost-estimate"></a>成本估算

讓我們來介紹此類的可能成本估算。  我們將使用一個25名學生的班級。  有 20 小時的計畫上課時間。  此外，每個學生在計畫上課時間之外的家庭作業或作業將獲得 10 小時的配額。  我們選擇的虛擬機器大小是中等的，即 42 個實驗室單元。

下面是此類的可能成本估算的示例：

25\*名學生（20 個預定小時 + \* 10 個配額小時） 0.42 USD 每小時 = 315.00 USD

有關定價的更多詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文介紹了使用 MySQL 和 SQL Server 為基本資料庫管理概念創建實驗室所需的步驟。 可以為其他資料庫類使用類似的設置。

## <a name="next-steps"></a>後續步驟

後續步驟是設置任何實驗室的常見步驟。

- [創建和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)
