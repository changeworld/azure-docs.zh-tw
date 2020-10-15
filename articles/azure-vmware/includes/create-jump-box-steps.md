---
title: 建立 Azure VMware 解決方案跳躍箱
description: 建立 Azure VMware 解決方案跳躍箱的步驟。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578407"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. 在資源群組中，選取 [+新增] 然後搜尋並選取 [Microsoft Windows 10]，然後選取 [建立]。

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="新增適用於跳躍箱的新 Windows 10 VM。" border="true":::

1. 在欄位中輸入必要資訊，然後選取 [檢閱+建立]。 

   如需欄位詳細資訊，請參閱下列表格。

   | 欄位 | 值 |
   | --- | --- |
   | **訂用帳戶** | 此值已預先填入屬於資源群組的訂用帳戶。 |
   | **資源群組** | 系統會針對您在上一個教學課程中建立的目前資源群組預先填入值。  |
   | **虛擬機器名稱** | 輸入 VM 的唯一名稱。 |
   | **區域** | 選取 VM 的地理位置。 |
   | **可用性選項** | 將預設值保持選取狀態。 |
   | **映像** | 選取 VM 映像。 |
   | **大小** | 保留預設大小的值。 |
   | **驗證類型**  | 選取 [密碼]。 |
   | **使用者名稱** | 輸入使用者名稱以登入 VM。 |
   | **密碼** | 輸入密碼以登入 VM。 |
   | **確認密碼** | 輸入密碼以登入 VM。 |
   | **公用輸入連接埠** | 選取 [無]。 如果您選取無，表示只有在要存取 VM 時，才能使用 [JIT 存取](../../security-center/security-center-just-in-time.md#jit-configure)來控制存取權。  |


1. 驗證通過後，選取 [建立] 以啟動虛擬機器建立流程。

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="新增適用於跳躍箱的新 Windows 10 VM。" border="true":::