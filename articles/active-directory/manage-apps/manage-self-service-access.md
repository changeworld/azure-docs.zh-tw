---
title: 如何設定自助式應用程式指派 | Microsoft Docs
description: 啟用自助應用程式存取以允許使用者尋找自己的應用程式
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a59343d4eafaa0e11f56636daa7471e748abf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763137"
---
# <a name="how-to-configure-self-service-application-assignment"></a>如何設定自助應用程式指派

在您的使用者可以從他們的我的應用程式存取面板自行探索應用程式之前，您必須啟用您想要允許使用者自行探索並要求存取權的任何應用程式的**自助式應用程式存取權**。 這種功能適用于從[Azure AD 資源庫](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)新增的應用程式、 [Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ，或透過[使用者或系統管理員同意](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)新增。 

此功能極有助於節省 IT 群組的時間和成本，非常建議在搭配 Azure Active Directory 進行現代化應用程式部署時使用。

您可以使用這項功能︰

-   讓使用者可以從[我的應用程式存取面板](https://myapps.microsoft.com/)自行探索應用程式，而不需要中斷 IT 群組。

-   將那些使用者新增至預先設定的群組，以便您可以查看哪些人要求存取權、移除存取權，以及管理指派給他們的角色。

-   可選擇讓商務核准者核准應用程式存取要求，以分擔 IT 群組的工作。

-   可選擇設定最多 10 人可核准對此應用程式的存取。

-   可選擇讓商務核准者直接從商務核准者的[應用程式存取面板](https://myapps.microsoft.com/)設定密碼，供那些使用者用來登入應用程式。

-   可選擇自動將自助式指派的使用者直接指派至應用程式角色。

> [!NOTE]
> 需要有 Azure Active Directory Premium （P1 或 P2）授權，使用者才能要求加入自助應用程式，並讓擁有者核准或拒絕要求。 若沒有 Azure Active Directory Premium 授權，使用者將無法新增自助應用程式。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>啟用自助應用程式存取以允許使用者尋找自己的應用程式

自助應用程式存取是一種絕佳的方式，可讓使用者自行探索應用程式，並選擇性地允許商務群組核准這些應用程式的存取權。 針對密碼單一登入應用程式，您也可以允許商務群組從他們自己的我的應用程式存取面板中，管理指派給這些使用者的認證。

若要啟用對應用程式的自助存取，請依照下列步驟執行：

1. 以全域管理員身分登入[Azure 入口網站](https://portal.azure.com)。

2. 選取 **Azure Active Directory**。 在左側導覽功能表中，選取 [企業應用程式]。

3. 從清單中選取應用程式。 如果您看不到此應用程式，請在搜尋方塊中開始輸入其名稱。 或使用篩選控制項來選取應用程式類型、狀態或可見度，然後選取 [套用]。

4. 在左側導覽功能表中，選取 [**自助**]。

5. 若要啟用此應用程式的自助式應用程式存取，請將 [要允許使用者要求此應用程式的存取權嗎?]**** 切換開關切換為 [是]****。

6. 在 [**要將指派的使用者新增至哪個群組？**] 旁，按一下 [**選取群組**]。 選擇群組，然後按一下 [**選取**]。 當使用者的要求獲得核准時，就會新增到這個群組。 當您查看此群組的成員資格時，您將能夠看到誰已透過自助存取權授與應用程式的存取權。
  
    > [!NOTE]
    > 此設定不支援從內部部署同步處理的群組。

7. **選擇性：** 若要在允許使用者存取之前要求商務核准，請先設定 [授與**此應用程式的存取權之前需要核准嗎？** ] 切換為 **[是]**。

8. **選擇性：對於僅使用密碼單一登入的應用程式，** 若要讓商務核准者指定針對核准的使用者傳送給此應用程式的密碼，請設定 [**允許核准者為此應用程式設定使用者的密碼嗎？** ] 切換為 **[是]**。

9. **選擇性：** 若要指定允許核准此應用程式存取權的商務核准者，請在 [**獲准核准存取此應用程式的人員**] 旁按一下 [**選取核准者**]，然後選取最多10個個別商務核准者。 然後按一下 [選取] 。

    >[!NOTE]
    >不支援群組。 您最多可以選取10個個別商務核准者。 如果您指定多個核准者，任何單一核准者都可以核准存取要求。

10. **選擇性：** **對於公開角色的應用程式**，若要將自助核准的使用者指派給角色，請**在 [此應用程式中應指派使用者的角色**] 旁按一下 [**選取角色**]，然後選擇要指派給這些使用者的角色。 然後按一下 [選取] 。

11. 按一下窗格頂端的 [儲存]**** 按鈕以完成此動作。

當您完成自助式應用程式設定之後，使用者可以流覽至其[我的應用程式存取面板](https://myapps.microsoft.com/)，然後按一下 [**新增自助應用程式**] 按鈕，以尋找啟用自助存取的應用程式。 商務核准者也會在其[我的應用程式存取面板](https://myapps.microsoft.com/)中看到通知。 您可以啟用電子郵件，通知他們有使用者已要求存取應用程式，需要他們核准。

## <a name="next-steps"></a>後續步驟
[設定 Azure Active Directory 進行自助服務群組管理](../users-groups-roles/groups-self-service-management.md)
