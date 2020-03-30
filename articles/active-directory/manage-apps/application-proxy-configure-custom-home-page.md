---
title: 已發佈應用的自訂主頁 - Azure AD 應用程式代理
description: 涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275606"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁

本文討論如何配置應用以將使用者定向到自訂主頁。 使用應用程式代理髮布應用時，將設置內部 URL，但有時這不是使用者應首先看到的頁面。 設置自訂主頁，以便使用者在訪問應用時獲得正確的頁面。 無論使用者是從 Azure 活動目錄訪問面板還是 Office 365 應用啟動器訪問應用，使用者都將看到您設置的自訂主頁。

當使用者啟動應用時，預設情況下會引導到已發佈應用的根域 URL。 登陸頁面通常設定為首頁 URL。 當您希望應用使用者登陸應用內的特定頁面時，請使用 Azure AD PowerShell 模組定義自訂主頁 URL。

下面是一個解釋公司為什麼設置自訂主頁的方案：

- 在公司網路中，使用者將登錄`https://ExpenseApp/login/login.aspx`並訪問你的應用。
- 由於應用程式代理需要在資料夾結構的頂層訪問其他資產（如圖像），因此使用`https://ExpenseApp`作為內部 URL 發佈應用。
- 預設外部 URL`https://ExpenseApp-contoso.msappproxy.net`是 ，它不將外部使用者帶到登錄頁。
- 您希望`https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`改為主頁 URL，以便外部使用者首先看到登錄頁。

> [!NOTE]
> 當您將已發佈應用程式的存取權提供給使用者時，應用程式會顯示在 [Azure AD 存取面板](../user-help/my-apps-portal-end-user-access.md)和 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)。

## <a name="before-you-start"></a>開始之前

設定首頁 URL 之前，請記住下列需求︰

- 指定的路徑必須是根域 URL 的子域路徑。

  例如，如果根域 URL 為`https://apps.contoso.com/app1/`，則配置的主頁 URL 必須從`https://apps.contoso.com/app1/`開頭。

- 若變更已發佈的應用程式，則變更可能會重設首頁 URL 的值。 當您未來更新應用程式時，您應該重新檢查並視需要更新首頁 URL。

您可以通過 Azure 門戶或使用 PowerShell 設置主頁 URL。

## <a name="change-the-home-page-in-the-azure-portal"></a>在 Azure 入口網站中變更首頁

要通過 Azure AD 門戶更改應用的主頁 URL，請按照以下步驟操作：

1. 以管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選擇**Azure 活動目錄**，然後**選擇應用註冊**。 將顯示已註冊應用的清單。
1. 從清單中選擇你的應用。 將顯示顯示已註冊應用詳細資訊的頁面。
1. 在 **"管理"** 下，選擇 **"品牌**"。
1. 使用新路徑更新**主頁 URL。**

   ![顯示主頁 URL 欄位的已註冊應用的品牌頁面](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. 選取 [儲存]****。

## <a name="change-the-home-page-with-powershell"></a>使用 PowerShell 變更首頁

要使用 PowerShell 配置應用的主頁，您需要：

1. 安裝 Azure AD PowerShell 模組。
1. 查找應用的 ObjectId 值。
1. 使用 PowerShell 命令更新應用的主頁 URL。

### <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

使用 PowerShell 定義自訂首頁 URL 之前，請先安裝 Azure AD PowerShell 模組。您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)下載此套件，其使用圖形 API 端點。

若要安裝套件，請遵循下列步驟：

1. 開啟標準 PowerShell 視窗，然後執行下列命令：

   ```powershell
   Install-Module -Name AzureAD
   ```

    若您以非系統管理員身分執行此命令，請使用 `-scope currentuser` 選項。

1. 在安裝過程中，選擇**Y**以從Nuget.org安裝兩個包。兩個包都是必需的。

### <a name="find-the-objectid-of-the-app"></a>查找應用的物件識別碼

通過按應用的顯示名稱或主頁搜索應用，可以獲取應用的 ObjectId。

1. 在相同的 PowerShell 視窗中，匯入 Azure AD 模組。

   ```powershell
   Import-Module AzureAD
   ```

1. 以租用戶系統管理員身分登入 Azure AD 模組。

   ```powershell
   Connect-AzureAD
   ```

1. 查找應用。 此示例使用 PowerShell 通過搜索顯示名稱 為`SharePoint`的應用來查找 ObjectId。

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   您應會取得如下所示的結果。 複製 ObjectId GUID 以在下一節中使用。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   或者，只需拉取所有應用的清單，搜索具有特定顯示名稱或主頁的應用的清單，並在找到應用後複製應用的 ObjectId。

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>更新首頁 URL

創建主頁 URL，並更新應用。該值。 繼續使用相同的 PowerShell 視窗，或者如果您使用的是新的 PowerShell 視窗，請使用 再次登錄到`Connect-AzureAD`Azure AD 模組。 接著，遵循下列步驟：

1. 創建變數以保存在上一節中複製的 ObjectId 值。 （將此 SharePoint 示例中用於的物件 Id 值替換為應用的 ObjectId 值。

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 通過運行以下命令確認應用正確。 輸出應與上一節中看到的輸出相同（[查找應用程式的 ObjectId）。](#find-the-objectid-of-the-app)

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

1. 更新主頁。

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 要確認更改成功，請再次從步驟 2 運行以下命令。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   對於我們的示例，輸出現在應如下所示：

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 重新開機應用以確認主頁按預期顯示為第一個螢幕。

> [!NOTE]
> 您對應用程式所做的任何變更都可能會重設首頁 URL。 如果您的首頁 URL 重設，請重複本節中的步驟重新設定。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)
- [教程：在 Azure 活動目錄中添加本地應用程式，以便通過應用程式代理進行遠端存取](application-proxy-add-on-premises-application.md)