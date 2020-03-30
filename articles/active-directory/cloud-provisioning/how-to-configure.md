---
title: Azure AD 連接雲預配新代理配置
description: 本文介紹如何安裝雲資源調配。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620980"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>為 Azure AD 連接基於雲的預配創建新配置

安裝代理後，需要登錄到 Azure 門戶並配置 Azure 活動目錄 （Azure AD） 連接雲預配。 按照以下步驟啟用代理。

## <a name="configure-provisioning"></a>配置預配
要配置預配，請按照以下步驟操作。

1.  在 Azure 門戶中，選擇**Azure 活動目錄**。
1.  選取 [Azure AD Connect]****。
1.  選擇 **"管理預配（預覽）"。**

    ![管理預配（預覽）](media/how-to-configure/manage1.png)

1.  選擇 **"新配置**"。
1.  在配置螢幕上，本地域已預填充。
1.  輸入**通知電子郵件**。 當預配不正常時，將通知此電子郵件。
1.  將選擇器移到**啟用**，然後選擇 **"保存**"。

    ![Azure AD 預配（預覽）](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>特定使用者和組的範圍預配
您可以使用本地活動目錄組或組織單位對代理進行限定以同步特定使用者和組。 不能在配置中配置組和組織單位。 

1.  在 Azure 門戶中，選擇**Azure 活動目錄**。
1.  選取 [Azure AD Connect]****。
1.  選擇 **"管理預配（預覽）"。**
1.  在 **"配置"** 下，選擇您的配置。

    ![[設定] 區段](media/how-to-configure/scope1.png)

1.  在 [設定]**** 底下選取 [所有使用者]****，以變更組態規則的範圍。

    ![所有使用者選項](media/how-to-configure/scope2.png)

1. 在右側，您可以更改範圍以僅包括安全性群組。 輸入組的可分辨名稱，然後選擇 **"添加**"。

    ![選定的安全性群組選項](media/how-to-configure/scope3.png)

1.  或者，您可以更改範圍以僅包括特定的組織單位。 選擇 **"完成"** 並**保存**。  
2.  更改範圍後，應[重新開機預配](#restart-provisioning)以啟動更改的立即同步。

    ![選定的組織單位選項](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新開機預配 
如果不想等待下一次計畫運行，請使用 **"重新開機預配**"按鈕觸發預配運行。 
1.  在 Azure 門戶中，選擇**Azure 活動目錄**。
1.  選取 [Azure AD Connect]****。
1.  選擇 **"管理預配（預覽）"。**
1.  在 **"配置"** 下，選擇您的配置。

    ![用於重新開機預配的配置選擇](media/how-to-configure/scope1.png)

1.  在頂部，選擇 **"重新開機預配**"。

## <a name="remove-a-configuration"></a>刪除配置
要刪除配置，請按照以下步驟操作。

1.  在 Azure 門戶中，選擇**Azure 活動目錄**。
1.  選取 [Azure AD Connect]****。
1.  選擇 **"管理預配（預覽）"。**
1.  在 **"配置"** 下，選擇您的配置。

    ![要刪除配置的配置選擇](media/how-to-configure/scope1.png)

1.  在配置螢幕的頂部，選擇 **"刪除**"。

    ![刪除按鈕](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>刪除配置之前沒有確認。 在選擇 **"刪除**"之前，請確保這是要執行的操作。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
