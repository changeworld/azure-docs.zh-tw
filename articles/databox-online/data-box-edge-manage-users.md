---
title: Azure Data Box Edge 管理使用者 | Microsoft Docs
description: 描述如何使用 Azure 入口網站來管理 Azure Data Box Edge 上的使用者。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78946131"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>使用 Azure 入口網站來管理 Azure Data Box Edge 上的使用者

本文說明如何管理 Azure Data Box Edge 上的使用者。 您可以透過 Azure 入口網站或本機 Web UI 來管理 Azure Data Box Edge。 使用 Azure 入口網站來新增、修改或刪除使用者。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 加入使用者
> * 修改使用者
> * 刪除使用者

## <a name="about-users"></a>關於使用者

使用者可以具有唯讀或完整權限。 如名稱所指，唯讀使用者只能檢視共用資料。 完整權限的使用者可以讀取共用資料、寫入至這些共用，以及修改或刪除共用資料。

 - **完整權限的使用者** - 具有完整存取權的本機使用者。
 - **唯讀使用者** - 具有唯讀存取權的本機使用者。 這些使用者會與允許唯讀作業的共用相關聯。

在共用建立期間建立使用者時，首先會定義使用者權限。 目前不支援修改共用層級許可權。

## <a name="add-a-user"></a>加入使用者

在 Azure 入口網站中執行下列步驟，以新增使用者。

1. 在 Azure 入口網站中，移至您的 Data Box Edge 資源，然後移至 [概觀 > 使用者]。 選取命令列上的 [+ 新增使用者]。

    ![選取新增使用者](media/data-box-edge-manage-users/add-user-1.png)

2. 針對您要新增的使用者指定使用者名稱和密碼。 確認密碼，然後選取 [新增]。

    ![指定使用者名稱和密碼](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 系統會保留下列使用者，不得加以使用：系統管理員、EdgeUser、EdgeSupport、HcsSetupUser、WDAGUtilityAccount、CLIUSR、DefaultAccount、來賓。  

3. 開始及完成使用者建立時，會顯示通知。 建立使用者之後，從命令列中選取 [重新整理]，以檢視更新後的使用者清單。


## <a name="modify-user"></a>修改使用者

建立使用者之後，您就可以變更與使用者相關聯的密碼。 從使用者清單中選取。 輸入並確認新密碼。 儲存變更。
 
![修改使用者](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>刪除使用者

在 Azure 入口網站中執行下列步驟，以刪除使用者。


1. 在 Azure 入口網站中，移至您的 Data Box Edge 資源，然後移至 [概觀 > 使用者]。

    ![選取要刪除的使用者](media/data-box-edge-manage-users/delete-user-1.png)

2. 從使用者清單中選取使用者，然後選取 [刪除]。  

   ![選取 [刪除]](media/data-box-edge-manage-users/delete-user-2.png)

3. 出現提示時，確認刪除。 

   ![Confirm delete](media/data-box-edge-manage-users/delete-user-3.png)

使用者清單會更新，以反映出已刪除的使用者。

![更新使用者清單](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](data-box-edge-manage-bandwidth-schedules.md)。
