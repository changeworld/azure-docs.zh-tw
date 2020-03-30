---
title: 如何設定自助式應用程式指派 | Microsoft Docs
description: 啟用自助應用程式存取以允許使用者尋找自己的應用程式
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409111"
---
# <a name="how-to-configure-self-service-application-assignment"></a>如何設定自助應用程式指派

在使用者可以從其"我的應用"訪問面板中自行發現應用程式之前，您需要啟用**自助服務應用程式**對您希望允許使用者自行發現和請求訪問的任何應用程式的存取權限。 此功能可用於從[Azure AD 庫](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)[、Azure AD 應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)添加或通過[使用者或管理員同意](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)添加的應用程式。 

此功能極有助於節省 IT 群組的時間和成本，非常建議在搭配 Azure Active Directory 進行現代化應用程式部署時使用。

您可以使用這項功能︰

-   允許使用者從["我的應用"訪問面板](https://myapps.microsoft.com/)中自行發現應用程式，而無需打擾 IT 組。

-   將那些使用者新增至預先設定的群組，以便您可以查看哪些人要求存取權、移除存取權，以及管理指派給他們的角色。

-   可選擇讓商務核准者核准應用程式存取要求，以分擔 IT 群組的工作。

-   可選擇設定最多 10 人可核准對此應用程式的存取。

-   可選擇讓商務核准者直接從商務核准者的[應用程式存取面板](https://myapps.microsoft.com/)設定密碼，供那些使用者用來登入應用程式。

-   可選擇自動將自助式指派的使用者直接指派至應用程式角色。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>啟用自助應用程式存取以允許使用者尋找自己的應用程式

自助服務應用程式訪問是允許使用者自行發現應用程式以及允許業務組批准對這些應用程式的訪問的好方法。 對於應用程式上的密碼單一簽名，您還可以允許業務組管理從他們自己的"我的應用"訪問面板分配給這些使用者的憑據。

若要啟用對應用程式的自助存取，請依照下列步驟執行：

1. 以全域管理員身份登錄到[Azure 門戶](https://portal.azure.com)。

2. 選擇**Azure 活動目錄**。 在左側導航功能表中，選擇**企業應用程式**。

3. 從清單中選擇應用程式。 如果看不到應用程式，請開始在搜索框中鍵入其名稱。 或者使用篩選器控制項選擇應用程式類型、狀態或可見度，然後選擇 **"應用**"。

4. 在左側導航功能表中，選擇**自助服務**。

5. 若要啟用此應用程式的自助式應用程式存取，請將 [要允許使用者要求此應用程式的存取權嗎?]**** 切換開關切換為 [是]****。

6. 旁邊**應向哪個組分配使用者？** **Select group** 選擇組，然後按一下 **"選擇**"。 當使用者的請求獲得批准時，他們將被添加到此組。 查看此組的成員身份時，您將能夠看到誰已被授予通過自助服務訪問訪問應用程式。
  
    > [!NOTE]
    > 此設置不支援從本地同步的組。

7. **可選：** 要在允許使用者訪問之前需要業務批准，請先在**授予對此應用程式存取權限之前設置"需要審批"？** 切換為 **"是**"。

8. **可選：對於僅使用密碼單一符號的應用程式，** 允許業務核准者為已批准的使用者指定發送到此應用程式的密碼，請設置 **"允許核准者"以為此應用程式設定使用者的密碼？** **Yes**

9. **可選：** 要指定允許批准訪問此應用程式的業務審批人，請在**允許誰批准訪問此應用程式旁邊，** 按一下 **"選擇核准者**"，然後選擇最多 10 個單獨的業務核准者。 然後按一下 [選取] ****。

    >[!NOTE]
    >不支援群組。 您最多可以選擇 10 個單獨的業務審批人。 如果指定多個審批人，則任何單個審批人都可以批准訪問請求。

10. **可選：****對於公開角色的應用程式**，要將自助服務批准的使用者分配給角色，**在此應用程式中應分配使用者到哪個角色旁邊？，** 按一下 **"選擇角色**"，然後選擇這些使用者應為其分配的角色。 然後按一下 [選取] ****。

11. 按一下窗格頂端的 [儲存]**** 按鈕以完成此動作。

完成自助服務應用程式佈建後，使用者可以導航到其["我的應用"訪問面板](https://myapps.microsoft.com/)，然後按一下"**添加自助服務應用"** 按鈕，以查找通過自助服務訪問啟用的應用。 業務核准者還會在其["我的應用"訪問面板](https://myapps.microsoft.com/)中看到通知。 您可以啟用電子郵件，通知他們有使用者已要求存取應用程式，需要他們核准。

## <a name="next-steps"></a>後續步驟
[設定 Azure Active Directory 進行自助服務群組管理](../users-groups-roles/groups-self-service-management.md)
