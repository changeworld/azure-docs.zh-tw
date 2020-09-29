---
title: 將使用者新增至 Azure Data Lake Analytics 帳戶
description: 瞭解如何使用 [新增使用者] 和 [Azure PowerShell]，將使用者正確新增至您的 Data Lake Analytics 帳戶。
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 4819b5cb252d2c646905d277fcbedc57a55fe692
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450212"
---
# <a name="adding-a-user-in-the-azure-portal"></a>在 Azure 入口網站中新增使用者

## <a name="start-the-add-user-wizard"></a>啟動新增使用者精靈
1. 透過 https://portal.azure.com 開啟您的 Azure Data Lake Analytics。
2. 按一下 [新增使用者精靈]****。
3. 在 [選取使用者]**** 步驟中，尋找您想要新增的使用者。 按一下 [選取]。
4. 在 [選取角色]**** 步驟中，選取 [Data Lake Analytics 開發人員]****。 此角色具備提交/監視/管理 U-SQL 作業時所需的一組最基本權限。 如果不打算讓群組管理 Azure 服務，請將其指派給此角色。
5. 在 [選取目錄權限]**** 步驟中，選取使用者將需要存取權的任何額外資料庫。 需要預設靜態資料庫（稱為 "master"）的讀取和寫入存取權，才能提交作業。 完成之後，請按一下 [確定]  。
6. 在名為 [指派選取的權限]**** 的最後一個步驟中，檢閱精靈將進行的變更。 按一下 [確定]。


## <a name="configure-acls-for-data-folders"></a>設定資料資料夾的 ACL
針對包含輸入資料和輸出資料的資料夾，視需要授與資料夾的 "R-X" 或 "RWX"。


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>您可以選擇將使用者新增至 Azure Data Lake Storage Gen1 的**讀者**角色。
1.  尋找您的 Azure Data Lake Storage Gen1 帳戶。
2.  按一下 [使用者]****。
3. 按一下 [新增] 。
4.  選取要指派此群組的 Azure 角色。
5.  指派給 [讀者] 角色。 這個角色有一組最低權限，可以瀏覽/管理儲存在 ADLSGen1 中的資料。 如果不打算讓群組管理 Azure 服務，請將其指派給此角色。
6.  輸入群組的名稱。
7.  按一下 [確定]。

## <a name="adding-a-user-using-powershell"></a>使用 PowerShell 來新增使用者

1. 依照下列指南中的指示進行操作：[如何安裝並設定 Azure PowerShell](/powershell/azure/) \(英文\)。
2. 下載 [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell 指令碼。
3. 執行 PowerShell 指令碼。 

以下是提供存取權給使用者的範例命令，可讓使用者提交作業、檢視新作業中繼資料，以及檢視舊中繼資料：

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
