---
title: 建立 Azure Red Hat OpenShift 的 Azure AD 租使用者
description: 以下說明如何建立 Azure Active Directory （Azure AD）租使用者，以裝載您的 Microsoft Azure Red Hat OpenShift 叢集。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84694900"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>建立 Azure Red Hat OpenShift 的 Azure AD 租使用者

Microsoft Azure Red Hat OpenShift 需要一個[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租使用者，以在其中建立您的叢集。 *租*使用者是組織或應用程式開發人員在透過註冊 Azure、Microsoft Intune 或 Microsoft 365 建立與 Microsoft 的關聯性時，所收到的專用 Azure AD 實例。 每個 Azure AD 租使用者彼此不同，並與其他 Azure AD 租使用者分開，並具有自己的工作和學校身分識別和應用程式註冊。

如果您還沒有 Azure AD 租使用者，請遵循這些指示來建立一個。

## <a name="create-a-new-azure-ad-tenant"></a>建立新的 Azure AD 租用戶

若要建立租使用者：

1. 使用您想要與 Azure Red Hat OpenShift 叢集建立關聯的帳戶來登入[Azure 入口網站](https://portal.azure.com/)。
2. 開啟 [ [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) ] 分頁，以建立新的租使用者（也稱為新的*Azure Active Directory*）。
3. 提供**組織名稱**。
4. 提供**初始功能變數名稱**。 這會附加*onmicrosoft.com* 。 您可以在這裡重複使用 [*組織名稱*] 的值。
5. 選擇將建立租使用者的國家或地區。
6. 按一下 [建立]。
7. 建立 Azure AD 租使用者之後，請選取 [**按一下這裡以管理您的新目錄**] 連結。 您的新租使用者名稱應該會顯示在 Azure 入口網站的右上方：  

    ![入口網站的螢幕擷取畫面，其中顯示右上方的租使用者名稱][tenantcallout]  

8. 記下 [*租使用者識別碼*]，讓您稍後可以指定要建立 Azure Red Hat OpenShift 叢集的位置。 在入口網站中，您現在應該會看到新租使用者的 [Azure Active Directory 總覽] 分頁。 選取 [**屬性**]，並複製**目錄識別碼**的值。 我們將 `TENANT` 在[建立 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集教學課程中參考此值。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>資源

如需[Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租使用者的詳細資訊，請參閱[Azure Active Directory 檔](https://docs.microsoft.com/azure/active-directory/)。

## <a name="next-steps"></a>後續步驟

瞭解如何建立服務主體、產生用戶端密碼和驗證回呼 URL，以及建立新的 Active Directory 使用者，以在您的 Azure Red Hat OpenShift 叢集上測試應用程式。

[建立 Azure AD 應用程式物件和使用者](howto-aad-app-configuration.md)
