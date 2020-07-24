---
author: baanders
description: 包含檔案，以取得 Azure 數位 Twins 設定中可能的其他需求
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b490b4304dd74d8266b24d0ea5af58726b14d747
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125767"
---
您的組織可能需要有來自訂用帳戶擁有者的其他動作，才能成功設定應用程式註冊（因此，因此會完成設定可用的 Azure 數位 Twins 實例）。 所需的步驟可能會根據您組織的特定設定而有所不同。

以下是擁有者可能需要執行的一些常見可能活動。 這些和其他作業可以從 Azure 入口網站的 [ [*Azure AD App 註冊*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)] 頁面執行。
* 授與系統管理員同意應用程式註冊。 您的組織可能會在您的訂用帳戶內所有應用程式註冊的 Azure AD 中，全域開啟必要的系統*管理員同意*。 若是如此，擁有者必須在應用程式註冊的 [ *API 許可權*] 頁面上為您的公司選取此按鈕，應用程式註冊才會生效：

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="API 許可權底下 [授與系統管理員同意] 按鈕的入口網站視圖":::
  - 如果已成功授與同意，則 Azure 數位 Twins 的專案應該會顯示 [已授與] 的*狀態值* _ **（您的公司）** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="以 API 許可權授與公司系統管理員同意的入口網站視圖":::
  - 擁有者可能會想要針對每個建立的應用程式註冊重複此流程，或執行一次，並針對訂用帳戶中的所有 Azure 數位 Twins 實例建立單一的共用應用程式註冊。 在第二個案例中，擁有者應該將應用程式註冊的*用戶端識別碼*和*租使用者識別碼*，與需要使用應用程式註冊的開發人員共用。 （這就是在 Microsoft 自己的租使用者中完成此作業的方式）。
* 啟動公用用戶端存取
* 為 web 和桌上型電腦存取設定特定的回復 Url
* 允許隱含 OAuth2 驗證流程

如需應用程式註冊和其不同選項的詳細資訊，請參閱[*使用 Microsoft 身分識別平臺註冊應用程式*](https://docs.microsoft.com/graph/auth-register-app-v2)。

您現在已有 Azure 數位 Twins 實例準備就緒，已指派許可權來管理它，並已設定用戶端應用程式存取它的許可權。