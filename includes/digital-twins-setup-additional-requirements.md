---
author: baanders
description: 在 Azure 數位 Twins 設定中包含可能額外需求的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: c2668bdda5002ebd2a34b8a2ffa5885263aec0c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009631"
---
您的組織可能需要來自訂用帳戶擁有者/系統管理員的其他動作，才能成功設定應用程式註冊 (以及完成設定可用的 Azure 數位 Twins 實例) 。 所需的步驟可能會根據您組織的特定設定而有所不同。

以下是擁有者/系統管理員可能需要執行的一些常見潛在活動。 您可以從 Azure 入口網站中的 [ [*Azure AD App 註冊*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ] 頁面執行這些作業和其他作業。
* 授與系統管理員同意以進行應用程式註冊。 您的組織可能會在您訂用帳戶內的所有應用程式註冊 Azure AD 中，全域開啟要求的系統 *管理員同意* 。 如果是這樣，則擁有者/系統管理員必須在應用程式註冊的 [ *API 許可權* ] 頁面上為您的公司選取此按鈕，應用程式註冊才會生效：

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="API 許可權下 [授與系統管理員同意] 按鈕的入口網站視圖":::
  - 如果已成功授與同意，則 Azure 數位 Twins 的專案應該會顯示已授與_給**公司 (** _的*狀態*值) 
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="API 許可權下 [授與系統管理員同意] 按鈕的入口網站視圖":::
* 啟用公用用戶端存取
* 設定 web 和桌面存取的特定回復 Url
* 允許隱含 OAuth2 authentication 流程

如需應用程式註冊及其不同安裝選項的詳細資訊，請參閱 [*使用 Microsoft 身分識別平臺註冊應用程式*](https://docs.microsoft.com/graph/auth-register-app-v2)。

您現在已準備好開始使用 Azure 數位 Twins 實例、已獲指派管理該實例的許可權，以及已設定用戶端應用程式存取的許可權。