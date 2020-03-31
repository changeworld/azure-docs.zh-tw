---
title: 為 Azure 紅帽打開 Shift 創建 Azure AD 租戶
description: 下面瞭解如何創建 Azure 活動目錄 （Azure AD） 租戶以承載 Microsoft Azure 紅帽 OpenShift 群集。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243688"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>為 Azure 紅帽打開 Shift 創建 Azure AD 租戶

Microsoft Azure 紅帽 OpenShift 需要[Azure 活動目錄 （Azure AD）](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租戶才能在其中創建群集。 *租戶*是 Azure AD 的專用實例，組織或應用開發人員在通過註冊 Azure、Microsoft Intune 或 Microsoft 365 來創建與 Microsoft 的關係時收到該實例。 每個 Azure AD 租戶都與其他 Azure AD 租戶不同且不同，並且有自己的工作、學校標識和應用註冊。

如果還沒有 Azure AD 租戶，請按照這些說明創建一個。

## <a name="create-a-new-azure-ad-tenant"></a>建立新的 Azure AD 租用戶

要創建租戶：

1. 使用要與 Azure 紅帽 OpenShift 群集關聯的帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 打開[Azure 活動目錄邊欄選項卡](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)以創建新租戶（也稱為新的*Azure 活動目錄*）。
3. 提供**組織名稱**。
4. 提供**初始功能變數名稱**。 這將*onmicrosoft.com*附加在它。 您可以在此處重用*組織名稱*的值。
5. 選擇將創建租戶的國家或地區。
6. 按一下 **[建立]**。
7. 創建 Azure AD 租戶後，選擇 **"按一下此處"以管理新的目錄**連結。 新租戶名稱應顯示在 Azure 門戶的右上角：  

    ![顯示右上角租戶名稱的門戶螢幕截圖][tenantcallout]  

8. 記下*租戶 ID，* 以便以後可以指定創建 Azure 紅帽 OpenShift 群集的位置。 在門戶中，現在應看到新租戶的 Azure 活動目錄概述邊欄選項卡。 選擇**屬性**並複製**目錄 ID**的值。 我們將引用此值，如`TENANT`創建 Azure[紅帽 OpenShift 群集](tutorial-create-cluster.md)教程。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>資源

有關[Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)的詳細資訊，請查看 Azure[活動目錄文檔](https://docs.microsoft.com/azure/active-directory/)。

## <a name="next-steps"></a>後續步驟

瞭解如何創建服務主體、生成用戶端機密和身份驗證回檔 URL，以及創建新的 Active Directory 使用者以測試 Azure 紅帽 OpenShift 群集上的應用。

[建立 Azure AD 應用程式物件和使用者](howto-aad-app-configuration.md)
