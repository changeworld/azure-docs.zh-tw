---
title: 使用 Azure Resource Manager 範本部署管理工具 - Azure
description: 如何使用 Azure Resource Manager 範本安裝使用者介面工具，以管理 Windows 虛擬桌面 (傳統) 資源。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 09e0b3bbac0bdc1d268aa7f24741aeb12a7d366d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462576"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本部署 Windows 虛擬桌面 (傳統) 管理工具

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。

依照本文中的指示，使用 Azure Resource Manager 範本部署 UI。

## <a name="important-considerations"></a>重要考量

因為應用程式需要獲得同意才能與 Windows 虛擬桌面互動，此工具不支援企業對企業 (B2B) 案例。 每個 Azure Active Directory (AAD) 租用戶的訂閱帳戶都需要它自己的獨立管理工具部署。

這個管理工具很簡單。 Microsoft 將提供重要安全性與品質更新。 [原始程式碼位於 GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)\(英文\)。 我們鼓勵客戶與合作夥伴根據其業務需求來自訂此工具。

下列瀏覽器皆與管理工具相容：
- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本 (僅限 macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>部署管理工具需要具備的條件

部署管理工具之前，您需要 Azure Active Directory (Azure AD) 使用者建立應用程式註冊，並部署管理 UI。 此使用者必須：

- 停用 Azure Multi-Factor Authentication (MFA)
- 擁有在您的 Azure 訂用帳戶中建立資源的權限
- 擁有建立 Azure AD 應用程式的權限。 遵循[必要權限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)中的指示，依下列步驟檢查您的使用者是否具有必要的權限。

部署及設定管理工具之後，建議您要求使用者啟動管理 UI，以確保可正常運作。 啟動管理 UI 的使用者必須具備角色指派，才能檢視或編輯 Windows 虛擬桌面租用戶。

## <a name="deploy-the-management-tool"></a>部署管理工具

在您開始之前，請確定您已透過瀏覽 Azure Active Directory (AAD) 呈現之 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com) 來同意伺服器與用戶端應用程式。

依照下列指示部署 Azure 資源管理範本：

1. 移至 [GitHub Azure RDS 範本頁面](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) \(英文\)。
2. 將範本部署至 Azure。
    - 若您是在「企業」訂用帳戶中部署，請向下捲動並選取 [部署至 Azure]****。
    - 若您是在「雲端解決方案提供者」訂用帳戶中部署，請依照下列指示部署到 Azure：
        1. 向下捲動並以滑鼠右鍵按一下 [部署至 Azure]****，然後選取 [複製連結位置]****。
        2. 開啟文字編輯器 (例如 [記事本])，並在該處貼上連結。
        3. 在 <https://portal.azure.com/> 後方、主題標籤 (#) 前面，輸入 at 符號 (@)，後面接著租用戶網域名稱。 格式範例如下：<https://portal.azure.com/@Contoso.onmicrosoft.com#create/>。
        4. 以具有「雲端解決方案提供者」訂用帳戶系統管理員/參與者權限的使用者身分登入 Azure 入口網站。
        5. 將您之前複製到文字編輯器的連結貼到網址列。
3. 輸入參數時，執行下列步驟：
    - 針對 **isServicePrincipal** 參數，請選取 [false]****。
    - 對於認證，請輸入已停用多重要素驗證的 Azure AD 認證。 這些認證會用來建立 Azure AD 應用程式和 Azure 資源。 若要深入了解，請參閱[部署管理工具需要的條件](#what-you-need-to-deploy-the-management-tool)。
    - 針對 **applicationName**，請使用將在您 Azure Active Directory 中註冊的唯一名稱。 此名稱也會用於 Web 應用程式 URL。 例如，您可以使用類似 "Apr3UX" 的名稱。
4. 提供參數之後，接受條款及條件，然後選取 [購買]****。

## <a name="provide-consent-for-the-management-tool"></a>為管理工具提供同意

在 GitHub Azure Resource Manager 範本完成之後，您將會發現資源群組包含兩個應用程式服務與一個應用程式服務方案 (在 Azure 入口網站中)。

在您登入並使用管理工具之前，必須同意與管理工具關聯的新 Azure Active Directory 應用程式。 同意即表示管理工具可代表目前已登入入口網站的使用者發出 Windows 虛擬桌面管理呼叫。

> [!div class="mx-imgBorder"]
> ![顯示當您同意 UI 管理工具時提供之權限的螢幕擷取畫面。](../media/management-ui-delegated-permissions.png)

若要判斷您可以使用哪個使用者登入工具，請移至您的 [Azure Active Directory 使用者設定頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)並記下 [使用者可同意應用程式代表自己存取公司資料]**** 的值。

> [!div class="mx-imgBorder"]
> ![顯示使用者是否可以授與同意給應用程式 (僅針對其使用者) 的螢幕擷取畫面。](../media/management-ui-user-consent-allowed.png)

- 若該值設定為 [是]****，您可以使用 Azure Active Directory 中的任何使用者登入並僅針對該使用者提供同意。 不過，若您稍後使用不同的使用者登入管理工具，您必須重新執行相同的同意程序。
- 若該值設定為 [否]****，您必須使用 Azure Active Directory 中的「全域管理員」登入並針對該目錄中的所有使用者提供管理員同意。 不會有任何其他使用者看到同意提示。


決定要同意的使用者後，請依照下列指示同意使用工具：

1. 移至您的 Azure 資源，使用您在範本中提供的名稱選取 Azure App Services 資源 (例如，Apr3UX) 並流覽至與其相關聯的 URL;例如，  `https://rdmimgmtweb-210520190304.azurewebsites.net` 。
2. 使用適當的 Azure Active Directory 使用者帳戶登入。
3. 若使用「全域管理員」來驗證，您現在可以選取 [代表您的組織同意]**** 核取方塊。 選取 [接受]**** 以提供同意。

   > [!div class="mx-imgBorder"]
   > ![顯示使用者或系統管理員將看到之完整同意頁面的螢幕擷取畫面。](../media/management-ui-consent-page.png)

這現在會將您帶到管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

為組織或指定使用者提供同意之後，您可以隨時存取管理工具。

依照下列指示啟動工具：

1. 選取具有您在範本中提供之名稱 (例如 Apr3UX) 的 Azure App Services 資源並瀏覽到與它關聯的 URL，例如 `https://rdmimgmtweb-210520190304.azurewebsites.net`。
2. 使用您的 Windows 虛擬桌面認證登入。
3. 當系統提示您選擇租用戶群組時，請從下拉式清單選取 [預設租用戶群組]****。
4. 選取 [預設租用戶群組]**** 時，視窗的左側應該會出現一個功能表。 在此功能表上，尋找租用戶群組的名稱並選取。

  > [!NOTE]
  > 若您有自訂租用戶群組，請手動輸入名稱，不要從下拉式清單選擇。

## <a name="report-issues"></a>回報問題

如果管理工具或其他 Windows 虛擬桌面工具發生任何問題，請依照[遠端桌面服務的 Azure Resource Manager 範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的指示，在 GitHub 上回報問題。

## <a name="next-steps"></a>後續步驟

既然您已學到如何部署並連線到管理工具，您可以學習如何使用 Azure 服務來監視服務問題與健康情況諮詢。 若要深入了解，請參閱我們的[設定服務警示教學課程](set-up-service-alerts-2019.md)。