---
title: 快速入門：存取應用程式的 Web API - Microsoft 身分識別平台 | Azure
description: 在本快速入門中，您將設定向 Microsoft 身分識別平台註冊的應用程式，以納入用來存取 Web API 的重新導向 URI、認證或權限。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082317"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>快速入門：設定用戶端應用程式以存取 Web API

在本快速入門中，您會新增重新導向 URI、認證或權限來存取應用程式的 Web API。 Web 或機密用戶端應用程式必須建立安全認證，才能參與需要驗證的授權授與流程。 Azure 入口網站支援的預設驗證方法為用戶端識別碼 + 祕密金鑰。 應用程式會在此過程中取得存取權杖。

同意架構要先確保用戶端取得必要權限所需的權限授與，用戶端才可以存取資源應用程式所公開的 Web API (例如 Microsoft Graph API)。 根據預設，所有應用程式都可以從 Microsoft Graph API 要求權限。

## <a name="prerequisites"></a>Prerequisites

* 完成[快速入門：向 Microsoft 身分識別平台註冊應用程式](quickstart-register-app.md)。
* 檢閱 [Microsoft 身分識別平台端點中的權限和同意](v2-permissions-and-consent.md)。
* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登入 Azure 入口網站，然後選取應用程式

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取您的帳戶。 將您的入口網站工作階段設定為您想要的 Azure AD 租用戶。
1. 搜尋並選取 [Azure Active Directory]  。 在 [管理]  底下選取 [應用程式註冊]  。
1. 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀]  或主要註冊頁面。

請使用下列程序來將您的應用程式設定為可存取 Web API。

## <a name="add-redirect-uris-to-your-application"></a>將重新導向 URL 新增至應用程式

您可以將自訂的重新導向 URI 和建議的重新導向 URI 新增至您的應用程式。 若要新增 Web 和公用用戶端應用程式的自訂重新導向 URI：

1. 從應用程式的 [概觀]  頁面，選取 [驗證]  。
1. 找出**重新導向 URI**。 您可能需要選取 [切換至舊版體驗]  。
1. 選取您要建立的應用程式類型：**Web** 或**公用用戶端/原生 (行動和傳統型)** 。
1. 輸入應用程式的重新導向 URI。

   * 若為 Web 應用程式，請提供應用程式的基底 URL。 例如，`http://localhost:31544` 可能是本機電腦上所執行 Web 應用程式的 URL。 使用者會使用此 URL 來登入 Web 用戶端應用程式。
   * 若為公用應用程式，請提供 Azure AD 用來傳回權杖回應的 URI。 輸入應用程式特定的值，例如 `https://MyFirstApp`。
1. 選取 [儲存]  。

若要選擇建議的公用用戶端重新導向 URI，請遵循下列步驟：

1. 從應用程式的 [概觀]  頁面，選取 [驗證]  。
1. 找到 [針對公用用戶端 (行動、傳統型) 而建議的重新導向 URI]  。 您可能需要選取 [切換至舊版體驗]  。
1. 為您的應用程式選取一個或多個重新導向 URI。 您也可以輸入自訂重新導向 URI。 如果您不確定要使用的內容，請查看程式庫文件。
1. 選取 [儲存]  。

重新導向 URI 會有某些限制。 如需詳細資訊，請參閱[重新導向 URI/回覆 URL 的限制](https://docs.microsoft.com/azure/active-directory/develop/reply-url)。

> [!NOTE]
> 試用新的**驗證**設定體驗，您可以在其中根據您要設為目標的平台或裝置來設定應用程式的設定。
>
> 若要查看此檢視，請從預 [驗證]  網頁中選取 [試用新的體驗]  。
>
> ![按一下 [試用新的體驗] 以查看平台設定檢視](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> 這會帶您前往[新的 [平臺設定]  頁面](#configure-platform-settings-for-your-application)。

### <a name="configure-advanced-settings-for-your-application"></a>設定應用程式的進階設定

根據您所註冊的應用程式，您可能需要設定一些額外的設定，例如：

* **登出 URL**。
* 針對單一頁面應用程式，您可以啟用**隱含授與**，並選取您想要授權端點發出的權杖。
* 針對在**預設用戶端類型**區段中使用整合式 Windows 驗證、裝置程式碼流程或使用者名稱/密碼取得權杖的傳統型應用程式，請將 [將應用程式視為公用用戶端]  設定為 [是]  。
* 針對使用 Live SDK 與 Microsoft 帳戶服務整合的繼承應用程式，請設定 **Live SDK 支援**。 新的應用程式不需要此設定。
* **預設用戶端類型**。
* **支援的帳戶類型**。

### <a name="modify-supported-account-types"></a>修改支援的帳戶類型

**支援的帳戶類型**會指定誰可以使用應用程式或存取 API。

如果您在註冊應用程式時設定了支援的帳戶類型，只有在下列情況下，才可以使用應用程式資訊清單編輯器來變更此設定：

* 您可以將帳戶類型從 **AzureADMyOrg** 或 **AzureADMultipleOrgs** 變更為 **AzureADandPersonalMicrosoftAccount**，反之亦然，或是
* 您可以將帳戶類型從 **AzureADMyOrg** 變更為 **AzureADMultipleOrgs**，反之亦然。

若要變更現有應用程式註冊所支援的帳戶類型，請更新 `signInAudience` 金鑰。 如需詳細資訊，請參閱[設定應用程式資訊清單](reference-app-manifest.md#configure-the-app-manifest)。

## <a name="configure-platform-settings-for-your-application"></a>設定應用程式的平台設定

![根據平台或裝置設定應用程式的設定](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

若要根據平台或裝置來設定應用程式設定，您的目標是：

1. 在 [平台設定]  頁面中，選取 [新增平台]  ，然後從可用的選項中選擇。

   ![顯示 [設定平台] 頁面](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. 根據您選取的平台輸入設定資訊。

   | 平台                | 組態設定            |
   |-------------------------|-----------------------------------|
   | **Web**              | 輸入應用程式的**重新導向 URI**。 |
   | **iOS / macOS**              | 輸入應用程式的 [套件組合識別碼]  ，您可以在 XCode 中的 Info.plist 或 [組建設定] 中找到此識別碼。 新增套件組合識別碼會自動建立應用程式的重新導向 URI。 |
   | **Android**          | 提供應用程式的**套件名稱**，您可以在 androidmanifest.xml 檔案中找到。<br/>產生並輸入**簽章雜湊**。 新增簽章雜湊會自動建立應用程式的重新導向 URI。  |
   | **行動應用程式與傳統型應用程式**  | 選擇性。 如果您要建立桌面和裝置的應用程式，請選取其中一個**建議的重新導向 URI**。<br/>選擇性。 輸入**自訂重新導向 URI**，作為回應驗證要求時，Azure AD 將會重新導向使用者的位置。 例如，針對您想要進行互動的 .NET Core 應用程式，請使用 `http://localhost`。 |

   > [!NOTE]
   > 在 Active Directory 同盟服務 (AD FS) 和 Azure AD B2C 上，您也必須指定連接埠號碼。  例如： `http://localhost:1234` 。 

   > [!IMPORTANT]
   > 對於未使用最新 Microsoft 驗證程式庫 (MSAL) 或未使用訊息代理程式的行動裝置應用程式，您必須在 [傳統型 + 裝置]  中設定這些應用程式的重新導向 URI。

視您選擇的平台而定，可能會有您可以設定的其他設定。 針對 **Web** 應用程式，您可以：

* 新增更多重新導向 URI
* 設定[隱含授與]  以選取您想要由授權端點發出的權杖：

  * 針對單一頁面應用程式，請選取 [存取權杖]  和 [識別碼權杖] 
  * 針對 Web 應用程式，選取 [識別碼權杖] 

## <a name="add-credentials-to-your-web-application"></a>將認證新增至 Web 應用程式

若要將認證新增至您的 Web 應用程式，請新增憑證或建立用戶端密碼。 若要新增憑證：

1. 從應用程式的 [概觀]  頁面，選取 [憑證和祕密]  區段。
1. 選取 [上傳憑證]  。
1. 選取您想要上傳的檔案。 檔案必須是下列其中一種檔案類型：.cer、.pem、.crt。
1. 選取 [新增]  。

若要新增用戶端密碼：

1. 從應用程式的 [概觀]  頁面，選取 [憑證和祕密]  區段。
1. 選取 [新增用戶端密碼]  。
1. 新增用戶端密碼的描述。
1. 選取持續時間。
1. 選取 [新增]  。

> [!NOTE]
> 儲存組態變更後，最右側的資料行就會包含用戶端密碼值。 **請務必複製此值**，以供在用戶端應用程式的程式碼中使用，因為您一旦離開此頁面就無法再存取此金鑰。

## <a name="add-permissions-to-access-web-apis"></a>新增用來存取 Web API 的權限

依預設會選取[圖形 API 登入及讀取使用者設定檔權限](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)。 針對每一個 Web API，您可以從[兩種權限類型](developer-glossary.md#permissions)中做選擇：

* **應用程式權限**。 您的用戶端應用程式本身需要直接存取 Web API (沒有使用者內容)。 這種類型的權限需要系統管理員同意。 傳統型和行動用戶端應用程式無法使用此權限。
* **委派權限**。 您的用戶端應用程式需要存取 Web API 作為已登入的使用者，但其存取權受到選取權限的限制。 這種類型的權限可由使用者授與，除非權限需要系統管理員的同意。

  > [!NOTE]
  > 新增對應用程式的委派權限並不會自動將同意授與租用戶內的使用者。 使用者仍必須手動同意在執行階段新增的委派權限，除非者系統管理員代表所有使用者授與同意。

若要新增權限以從用戶端存取資源 API：

1. 從應用程式的 [概觀]  頁面，選取 [API 權限]  。
1. 在 [已設定的權限]  底下，選取 [新增權限]  。
1. 根據預設，此檢視可讓您選取 **Microsoft API**。 選取您感興趣的 API 區段：

    * **Microsoft API**。 可讓您選取 Microsoft API (例如 Microsoft Graph) 的權限。
    * **我組織使用的 API**。 可讓您針對組織公開的 API 或組織已整合的 API 選取其權限。
    * **我的 API**。 可讓您針對您公開的 API 選取權限。

1. 在選取 API 後，您就會看到 [要求 API 權限]  頁面。 如果 API 同時公開了委派權限和應用程式權限，請選取應用程式所需的權限類型。
1. 完成時，選取 [新增權限]  。

您會返回 [API 權限]  頁面。 權限已儲存並新增至資料表。

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>了解 API 權限和系統管理員同意 UI

### <a name="configured-permissions"></a>已設定權限

此區段會顯示已在應用程式物件上明確設定的權限。 這些權限包含在應用程式所需的資源存取清單中。 您可以在此資料表中新增或移除權限。 身為系統管理員，您也可以授與/撤銷一組 API 權限或個別權限的系統管理員同意。

### <a name="other-permissions-granted"></a>已授與的其他權限

如果您的應用程式已在租用戶中註冊，您可能會看到一個額外的區段，標題為**授與租用戶的其他權限**。 本節顯示已授與租用戶，但尚未在應用程式物件上明確設定的權限。 這些是以動態方式要求和同意的權限。 只有在至少有一個適用的權限時，才會顯示此區段。

您可以將這一節中所顯示的一組 API 權限或個別權限新增至 [已設定權限]  區段。 身為系統管理員，您也可以在本節中撤銷個別 API 或權限的系統管理員同意。

### <a name="admin-consent-button"></a>系統管理員同意按鈕

如果您的應用程式已在租用戶中註冊，您會看到 [針對租用戶授與系統管理員同意]  按鈕。 如果您不是系統管理員，或未針對應用程式設定權限，則會停用此功能。
此按鈕可讓系統管理員對於應用程式上設定的權限授與系統管理員同意。 按一下 [系統管理員同意] 按鈕會啟動新的視窗，其中會出現同意提示，顯示所有已設定的權限。

> [!NOTE]
> 權限在針對應用程式進行設定與顯示在同意提示上之間有延遲。 如果您在同意提示中看不到所有已設定權限，請將其關閉然後重新啟動。

如果您有已授與但未設定的權限，系統管理員同意按鈕會提示您處理這些權限。 您可以將它們新增至已設定權限，或將其移除。

同意提示會提供 [接受]  或 [取消]  的選項。 選取 [接受]  以授與系統管理員同意。 如果您選取 [取消]  ，系統就不會授與系統管理員同意。 錯誤訊息會指出您已拒絕同意。

> [!NOTE]
> 授與系統管理員同意 (在同意提示中選取 [接受]  )，與系統管理員同意狀態反映在入口網站之間有延遲。

## <a name="next-steps"></a>後續步驟

前進到下一篇文章，以了解如何公開 Web API。
> [!div class="nextstepaction"]
> [快速入門：設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)

* 若要了解代表已註冊的應用程式和它們之間關係的兩個 Azure AD 物件，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。

* 若要了解使用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。

* [快速入門：使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)

* [快速入門：修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)

* [快速入門：移除向 Microsoft 身分識別平台註冊的應用程式](quickstart-remove-app.md)
