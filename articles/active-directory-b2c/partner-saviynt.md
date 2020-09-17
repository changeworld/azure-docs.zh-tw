---
title: 使用 Azure Active Directory B2C 設定 Saviynt 的教學課程
titleSuffix: Azure AD B2C
description: 本教學課程說明如何使用 Saviynt 來設定跨應用程式整合的 Azure Active Directory B2C，以簡化 IT 現代化，並提升安全性、治理和合規性。 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d80a1ba515aa137eba57051f080b4a2b4f311072
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708576"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Saviynt 的教學課程

在此範例教學課程中，我們會提供有關如何將 Azure Active Directory (AD) B2C 與 [Saviynt](https://saviynt.com/)整合的指引。 Saviynt 的安全性管理員平臺可在單一整合平臺中，提供現今企業所需的可見度、安全性和治理。 Saviynt 納入了應用程式風險和治理、基礎結構管理、特殊許可權帳戶管理和客戶風險分析。

在此範例教學課程中，您將設定 Saviynt，以針對 Azure AD B2C 使用者提供更精細的存取控制型委派系統管理。 Saviynt 會執行下列檢查，以判斷使用者是否有權管理 Azure AD B2C 的使用者。

- 功能層級安全性，以判斷使用者是否可以執行特定作業。 例如，建立使用者、更新使用者、重設使用者密碼等等。

- 欄位層級安全性，用來判斷使用者是否可以在使用者管理作業期間，讀取/寫入另一位使用者的特定屬性。 例如，服務台代理程式只能更新電話號碼，而其他所有屬性都是唯讀的。

- 資料層級安全性，以判斷使用者是否可以對特定使用者執行特定作業。 例如，UK 地區的服務台系統管理員只能管理 UK 使用者。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- [Azure AD B2C 的租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。 租使用者已連結至您的 Azure 訂用帳戶。

- Saviynt[訂](https://saviynt.com/contact-us/)用帳戶

## <a name="scenario-description"></a>案例描述

Saviynt 整合包含下列元件：

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) –企業對客戶身分識別即服務，可讓您自訂客戶註冊、登入及管理其設定檔的方式。

- [Saviynt](https://saviynt.com/) –身分識別管理平臺，可為使用者生命週期管理和 Azure AD B2C 使用者的存取治理提供更細緻的委派系統管理。  

- [MICROSOFT GRAPH api](https://docs.microsoft.com/graph/use-the-api) –此 Api 提供 Saviynt 的介面，以管理 Azure AD B2C 中的 Azure AD B2C 使用者和其存取權。

下列架構圖顯示執行。

![顯示 saviynt 架構圖表的影像](./media/partner-saviynt/saviynt-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 委派的系統管理員會透過 Saviynt 啟動管理 Azure AD B2C 使用者操作。
| 2. | 如果委派的系統管理員可以進行特定作業，Saviynt 會以其授權引擎進行驗證。
| 3. | Saviynt 的授權引擎傳送授權成功/失敗回應。
| 4. | Saviynt 可讓委派的系統管理員進行必要的操作。
| 5. | Saviynt 會叫用 Microsoft Graph API 以及使用者屬性，以管理中的使用者 Azure AD B2C
| 6. | Microsoft Graph API 會接著建立/更新/刪除 Azure AD B2C 中的使用者。
| 7. | Azure AD B2C 將會傳送成功/失敗的回應。
| 8. | Microsoft Graph API 接著會將回應傳回給 Saviynt。

## <a name="onboard-with-saviynt"></a>使用 Saviynt 上線

1. 若要建立 Saviynt 帳戶，請聯絡 [Saviynt](https://saviynt.com/contact-us/)

2. 建立委派的系統管理原則，並將使用者指派為具有各種角色的 [委派系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-concept-delegation) 。

## <a name="configure-azure-ad-b2c-with-saviynt"></a>使用 Saviynt 設定 Azure AD B2C

### <a name="creating-an-azure-ad-application-for-saviynt"></a>建立 Saviynt 的 Azure AD 應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com/#home)。

2. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

3. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD B2C**]。

4. 選取**應用程式註冊**  >  **新的註冊**。

5. 輸入應用程式的名稱。 例如，Saviynt，然後選取 [ **建立**]。

6. 移至 [ **API 許可權** ]，然後選取 [ **+ 新增許可權]。**

7. [要求 API 權限] 頁面隨即顯示。 選取 [ **Microsoft api** ] 索引標籤，然後選取 [ **Microsoft Graph** ] 作為常用的 Microsoft api。

8. 移至下一個頁面，然後選取 [ **應用程式許可權**]。

9. 選取 [ **目錄**]，然後選取 [目錄]、[ **全部** ] 和 [目錄]。 **所有** 核取方塊。

10. 選取 [ **新增許可權**]。 檢查新增的許可權。

11. 選取 **[授與系統管理員同意來儲存預設目錄**]  >  ** **。

12. 移至 [ **憑證和密碼** ]，然後選取 [ **+ 新增用戶端密碼**]。 輸入用戶端密碼描述、選取到期選項，然後選取 [ **新增**]。

13. 秘密金鑰會產生並顯示在 [用戶端密碼] 區段中。

    >[!NOTE]
    > 您稍後將需要用戶端密碼。

14. 移至 **總覽** ，並取得 **用戶端識別碼** 和 **租使用者識別碼**。

15. 需要租使用者識別碼、用戶端識別碼和用戶端密碼，才能完成 Saviynt 中的設定。

### <a name="enabling-saviynt-to-delete-users"></a>啟用 Saviynt 以刪除使用者

下列步驟說明如何在 Azure AD B2C 中啟用 Saviynt 以執行使用者刪除作業。

>[!NOTE]
>[將服務主體的存取權授與系統管理員角色之前，請先評估風險。](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

1. 在 Windows 工作站/伺服器上安裝最新版的 MSOnline PowerShell 模組。

2. 連接至 AzureAD PowerShell 模組，然後執行下列命令：

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>測試解決方案

流覽至您的 Saviynt 應用程式租使用者，並測試使用者生命週期管理和存取治理使用案例。

## <a name="next-steps"></a>下一步

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

- [建立 web API 應用程式](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application)
