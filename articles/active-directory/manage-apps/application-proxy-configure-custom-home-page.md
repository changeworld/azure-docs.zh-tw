---
title: 已發佈應用程式的自訂首頁-Azure AD 應用程式 Proxy
description: 涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155a91ded6c814e2d868e8edd4572459460d006f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642056"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁

本文討論如何設定應用程式，以將使用者導向至自訂的首頁。 當您使用應用程式 Proxy 發佈應用程式時，您會設定內部 URL，但有時這不是使用者應該先看到的頁面。 設定自訂的首頁，讓使用者在存取應用程式時取得正確的頁面。 無論使用者是從 Azure Active Directory 我的應用程式或 Microsoft 365 應用程式啟動器存取應用程式，使用者都會看到您設定的自訂首頁。

當使用者啟動應用程式時，預設會將他們導向已發佈應用程式的根域 URL。 登陸頁面通常設定為首頁 URL。 當您想要讓應用程式使用者在應用程式內的特定頁面上進行時，請使用 Azure AD PowerShell 模組來定義自訂的首頁 URL。

以下是一個案例，說明貴公司設定自訂首頁的原因：

- 在您的公司網路內，使用者會進入登 `https://ExpenseApp/login/login.aspx` 入並存取您的應用程式。
- 因為您有其他資產 (例如，應用程式 Proxy 需要在資料夾結構最上層存取的映射) ，所以您會使用 `https://ExpenseApp` 做為內部 URL 來發佈應用程式。
- 預設外部 URL 是 `https://ExpenseApp-contoso.msappproxy.net` ，其不會將外部使用者帶至登入頁面。
- 您想要 `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` 改為將設定為首頁 URL，讓外部使用者先看到登入頁面。

> [!NOTE]
> 當您將已發佈應用程式的存取權授與使用者時，應用程式會顯示在 [我的應用程式](../user-help/my-apps-portal-end-user-access.md) 和 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)中。

## <a name="before-you-start"></a>開始之前

設定首頁 URL 之前，請記住下列需求︰

- 您指定的路徑必須是根域 URL 的子域路徑。

  例如，如果根域 URL 是 `https://apps.contoso.com/app1/` ，您所設定的首頁 url 必須以開頭 `https://apps.contoso.com/app1/` 。

- 若變更已發佈的應用程式，則變更可能會重設首頁 URL 的值。 當您未來更新應用程式時，您應該重新檢查並視需要更新首頁 URL。

您可以透過 Azure 入口網站或使用 PowerShell 來設定首頁 URL。

## <a name="change-the-home-page-in-the-azure-portal"></a>在 Azure 入口網站中變更首頁

若要透過 Azure AD 入口網站變更應用程式的首頁 URL，請遵循下列步驟：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 **Azure Active Directory**，然後 **應用程式註冊**。 註冊的應用程式清單隨即出現。
1. 從清單中選擇您的應用程式。 顯示已註冊應用程式詳細資料的頁面隨即出現。
1. 在 [ **管理**] 底下，選取 [ **品牌**]。
1. 使用新路徑更新 **首頁 URL**  。

   ![顯示 [首頁 URL] 欄位之已註冊應用程式的商標頁面](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. 選取 [儲存]****。

## <a name="change-the-home-page-with-powershell"></a>使用 PowerShell 變更首頁

若要使用 PowerShell 來設定應用程式的首頁，您需要：

1. 安裝 Azure AD PowerShell 模組。
1. 尋找應用程式的 ObjectId 值。
1. 使用 PowerShell 命令來更新應用程式的首頁 URL。

### <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

使用 PowerShell 定義自訂首頁 URL 之前，請先安裝 Azure AD PowerShell 模組。您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)下載此套件，其使用圖形 API 端點。

若要安裝套件，請遵循下列步驟：

1. 開啟標準 PowerShell 視窗，然後執行下列命令：

   ```powershell
   Install-Module -Name AzureAD
   ```

    若您以非系統管理員身分執行此命令，請使用 `-scope currentuser` 選項。

1. 在安裝期間，選取 [ **Y** ] 以從 Nuget.org 安裝兩個套件。這兩個套件都是必要的。

### <a name="find-the-objectid-of-the-app"></a>尋找應用程式的 ObjectId

您可以使用應用程式的顯示名稱或首頁來搜尋應用程式，以取得應用程式的 ObjectId。

1. 在相同的 PowerShell 視窗中，匯入 Azure AD 模組。

   ```powershell
   Import-Module AzureAD
   ```

1. 以租用戶系統管理員身分登入 Azure AD 模組。

   ```powershell
   Connect-AzureAD
   ```

1. 尋找應用程式。 此範例會藉由搜尋顯示名稱為的應用程式，使用 PowerShell 來尋找 ObjectId `SharePoint` 。

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   您應會取得如下所示的結果。 複製 ObjectId GUID 以在下一節中使用。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   或者，您可以直接提取所有應用程式的清單、在清單中搜尋具有特定顯示名稱或首頁的應用程式，然後在找到應用程式之後，複製應用程式的 ObjectId。

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>更新首頁 URL

建立首頁 URL，並以該值更新您的應用程式。 繼續使用相同的 PowerShell 視窗，或如果您使用的是新的 PowerShell 視窗，請使用重新登入 Azure AD 模組 `Connect-AzureAD` 。 接著，依照下列步驟執行：

1. 建立變數，以保存您在上一節中複製的 ObjectId 值。  (以您的應用程式 ObjectId 值取代此 SharePoint 範例中所使用的 ObjectId 值。 ) 

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 藉由執行下列命令來確認您有正確的應用程式。 輸出應該與您在上一節中看到的輸出相同 ([尋找應用程式](#find-the-objectid-of-the-app)) 的 ObjectId。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 建立空白應用程式物件以存放您要進行的變更。

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. 將首頁 URL 設定為您想要的值。 此值必須是已發佈應用程式的子網域路徑。 例如，如果您將首頁 URL 從 `https://sharepoint-iddemo.msappproxy.net/` 變更為 `https://sharepoint-iddemo.msappproxy.net/hybrid/`，則應用程式使用者會直接前往自訂首頁。

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. 進行首頁的更新。

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 若要確認變更成功，請再次執行步驟2中的下列命令。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   在我們的範例中，輸出現在看起來應該如下所示：

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 重新開機應用程式，以確認首頁如預期般顯示為第一個畫面。

> [!NOTE]
> 您對應用程式所做的任何變更都可能會重設首頁 URL。 如果您的首頁 URL 重設，請重複本節中的步驟重新設定。

## <a name="next-steps"></a>接下來的步驟

- [使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)
- [教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)
