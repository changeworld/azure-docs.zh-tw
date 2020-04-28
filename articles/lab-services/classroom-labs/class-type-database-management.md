---
title: 設定實驗室來教授關係資料庫的資料庫管理 |Microsoft Docs
description: 瞭解如何設定實驗室，以教授關係資料庫的管理。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469913"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>設定實驗室來教授關係資料庫的資料庫管理

本文說明如何在 Azure 實驗室服務中設定基本資料庫管理類別的實驗室。 資料庫概念是大專院校大部分的電腦科學系所會教授的其中一門入門課程。 結構化查詢語言 (SQL) （SQL）是國際標準。 SQL 是關聯資料庫管理的標準語言，包括新增、存取和管理資料庫中的內容。  最值得注意的是，其快速處理、經過證實的可靠性、便利性和使用彈性。

在本文中，我們將示範如何在具有 MySQL 資料庫伺服器和 SQL Server 2019 伺服器的實驗室中設定虛擬機器範本。  [MySQL](https://www.mysql.com/)是免費提供的開放原始碼關係資料庫管理系統（RDBMS）。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)是 Microsoft RDBMS 的最新版本。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。  您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需如何啟用 marketplace 映射的詳細資訊，請參閱[指定可供實驗室建立者使用的 marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
|Marketplace 映射| 啟用「Windows Server 2019 上的 SQL Server 2019 標準」映射，以在您的實驗室帳戶中使用。|

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。  如需如何建立教室實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| 中。 此大小最適合用於關係資料庫、記憶體內部快取及分析。|
|虛擬機器映像| Windows Server 2019 上的 SQL Server 2019 Standard|

## <a name="template-machine-configuration"></a>範本機器設定

若要在 Windows Server 2019 上安裝 MySQL，您可以遵循在[虛擬機器上安裝和執行 Mysql 社區伺服器](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)中所述的步驟。

SQL Server 2019 已預先安裝在建立新實驗室時所選擇的虛擬機器映射中。

## <a name="cost-estimate"></a>成本預估

讓我們來討論這個類別的可能成本預估。  我們將使用25名學生的課程。  已排程的類別時間有20小時。  此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。  我們選擇的虛擬機器大小為「中」，也就是42實驗室單位。

以下是此類別的可能成本預估範例：

25名\*學生（20個排程小時 + 10 個\*配額時數）0.42 美元/小時 = 315.00 美元

如需更多有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文逐步解說使用 MySQL 和 SQL Server 建立基本資料庫管理概念的實驗室所需的步驟。 您可以針對其他資料庫類別使用類似的設定。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
