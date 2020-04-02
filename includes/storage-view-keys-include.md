---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521261"
---
要從 Azure 入口網站檢視並複製儲存帳戶存取金鑰或連接字串,請執行以下操作:

1. 導覽[到 Azure 門戶](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在 **「設定」** 下,選擇 **「存取鍵**」 。。 您的帳戶存取金鑰隨即出現，此外也會顯示每個金鑰的完整連接字串。
4. 尋找 [金鑰 1]**** 下方的 [金鑰]**** 值，然後按一下 [複製]**** 按鈕以複製帳戶金鑰。
5. 或者，您也可以複製整個連接字串。 尋找 [金鑰1]**** 下方的 [連接字串]**** 值，然後按一下 [複製]**** 按鈕來複製連接字串。

    ![展示如何在 Azure 門戶中查看存取金鑰的螢幕截圖](media/storage-view-keys-include/portal-connection-string.png)

可以使用任一鍵訪問 Azure 存儲,但通常最好使用第一個密鑰,並在旋轉密鑰時保留第二個鍵的使用。

要查看或讀取帳戶的訪問密鑰,使用者必須是服務管理員,或者必須分配包含**Microsoft.儲存/存儲帳戶/清單鍵/操作**的 RBAC 角色。 包含此操作的某些內建 RBAC 角色是**擁有者**,**參與者**與**儲存帳戶金鑰操作者服務角色**。 有關服務管理員角色的詳細資訊,請參閱[經典訂閱管理員角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。 有關 Azure 儲存的內建角色的詳細資訊,請參閱[Azure RBAC 的 Azure 內建角色中的](../articles/role-based-access-control/built-in-roles.md#storage)**存儲**部分。
