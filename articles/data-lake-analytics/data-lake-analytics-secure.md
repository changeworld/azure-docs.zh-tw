---
title: 為多個使用者保護 Azure Data Lake Analytics
description: 深入了解如何設定多個使用者，以在 Azure Data Lake Analytics 中執行作業。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 52bdd80dba1d5de150f403199fbd5c14d188a410
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220137"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>設定使用者對於 Azure Data Lake Analytics 中作業資訊的存取權 

在 Azure Data Lake Analytics 中，您可以使用多個使用者帳戶或服務主體來執行作業。 

為了讓那些使用者看到詳細的作業資訊，使用者必須能夠讀取作業資料夾的內容。 作業資料夾位於 `/system/` 目錄。 

如果未設定必要的權限，使用者可能會看到錯誤訊息：`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>設定使用者對於作業資訊的存取權

您可以使用 [新增使用者精靈]**** 在資料夾上設定 ACL。 如需詳細資訊，請參閱[新增使用者](data-lake-analytics-manage-use-portal.md#add-a-new-user)。

如果您需要更細微的控制，或者需要針對權限撰寫指令碼，請保護資料夾，如下所示：

1. 在根資料夾上 (透過存取 ACL) 授與**執行**權限：
   - /
   
2. 在包含作業資料夾的資料夾上，(透過存取 ACL 和預設 ACL) 授與**執行**和**讀取**權限。 例如，在 2018 年 5 月 25 日執行的特定作業，必須存取下列資料夾：
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>後續步驟
[新增使用者](data-lake-analytics-manage-use-portal.md#add-a-new-user)
