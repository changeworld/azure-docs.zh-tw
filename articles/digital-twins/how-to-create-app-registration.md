---
title: 建立應用程式註冊
titleSuffix: Azure Digital Twins
description: 瞭解如何建立 Azure AD 應用程式註冊，作為用戶端應用程式的驗證選項。
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209552"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>建立要搭配 Azure 數位 Twins 使用的應用程式註冊

使用 Azure 數位 Twins 實例時，通常會透過用戶端應用程式與該實例互動，例如自訂用戶端應用程式或例如 [ADT Explorer](quickstart-adt-explorer.md)的範例。 這些應用程式必須向 Azure 數位 Twins 進行驗證才能與它互動，而應用程式可使用的一些 [驗證機制](how-to-authenticate-client.md) 則牽涉到 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) **應用程式註冊**。

並非所有驗證案例都需要此功能。 但是，如果您使用的驗證策略或程式碼範例需要註冊應用程式（包括 **用戶端識別碼** 和租使用者 **識別碼**），本文將說明如何設定一個。

## <a name="using-azure-ad-app-registrations"></a>使用 Azure AD 應用程式註冊

[Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 是 Microsoft 的雲端式身分識別和存取管理服務。 在 Azure AD 中設定 **應用程式註冊** ，是將用戶端應用程式存取權授與 Azure 數位 Twins 的一種方式。

此應用程式註冊可讓您設定 [Azure 數位 Twins api](how-to-use-apis-sdks.md)的存取權限。 之後，用戶端應用程式可以使用註冊的 **用戶端和租使用者識別碼值**，向應用程式註冊進行驗證，因此會將設定的存取權限授與 api。

>[!TIP]
> 您可能會想要在每次需要時設定新的應用程式註冊， *或* 只進行一次，建立單一應用程式註冊，以在所有需要它的情況下共用。

## <a name="create-the-registration"></a>建立註冊

首先，流覽至 Azure 入口網站中的 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) (您可以使用此連結，或使用入口網站搜尋列) 來尋找它。 從 [服務] 功能表中選取 *應用程式註冊* ，然後選取 [ *+ 新增註冊*]。

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

在接下來的 [ *註冊應用程式* ] 頁面中，填入要求的值：
* **名稱**：要與註冊相關聯的 Azure AD 應用程式顯示名稱
* **支援的帳戶類型**： *僅 (預設目錄-單一租使用者) 中選取此組織目錄中的帳戶 *
* 重新**導向 URI**： Azure AD 應用程式*Azure AD 應用程式回復 URL* 。 將 *公用用戶端/原生 (mobile & desktop) * 的 URI `http://localhost` 。

當您完成時，請按 [ *註冊* ] 按鈕。

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

註冊完成設定之後，入口網站會將您重新導向至其詳細資料頁面。

## <a name="collect-client-id-and-tenant-id"></a>收集用戶端識別碼和租使用者識別碼

接下來，從其詳細資料頁面收集關於應用程式註冊的一些重要值：

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

記下**您**頁面上顯示的_**應用程式 (用戶端) 識別碼**_ 和_**目錄 (租使用者) 識別碼**_。 這些是用戶端應用程式將需要使用此註冊來向 Azure 數位 Twins 進行驗證的值。

## <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 數位 Twins API 許可權

接下來，使用 Azure 數位 Twins Api 的基準許可權，設定您所建立的應用程式註冊。

從應用程式註冊的入口網站頁面，選取功能表中的 [ *API 許可權* ]。 在 [下列許可權] 頁面上，按下 [ *+ 新增許可權* ] 按鈕。

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

在接下來的 [ *要求 API 許可權* ] 頁面中，切換至 [ *我的組織使用的 api* ] 索引標籤，並搜尋 *azure 數位 twins*。 從搜尋結果中選取 _**Azure 數位 Twins**_ ，以繼續指派 Azure 數位 Twins api 的許可權。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

>[!NOTE]
> 如果您的訂用帳戶在2020年7月) 之前，仍有先前公開預覽版服務 (的 Azure 數位 Twins 實例，則您必須改為搜尋並選取 _**Azure 智慧空間服務**_ 。 這是同一組 Api 的舊名稱 (請注意， *應用程式 (用戶端) 識別碼* 與上述螢幕擷取畫面中的相同) ，而且您的體驗不會在此步驟之外變更。
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

接下來，您將選取要為這些 Api 授與的許可權。 展開 [ **讀取 (1]) ** 許可權，然後核取顯示 [ *讀取* ] 的方塊，以授與此應用程式註冊讀取器和寫入器許可權。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

完成時點擊 *新增許可權* 。

### <a name="verify-success"></a>確認是否成功

在 [ *API 許可權* ] 頁面上，確認現在有 Azure 數位 Twins 的專案反映讀取/寫入權限：

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

您也可以在應用程式註冊的 *manifest.js*內確認 Azure 數位 Twins 的連線，這會在您新增 API 許可權時使用 Azure 數位 Twins 資訊自動更新。

若要這樣做，請從功能表中選取 [ *資訊清單* ]，以查看應用程式註冊的資訊清單程式碼。 滾動至程式碼視窗底部，然後在底下尋找這些欄位 `requiredResourceAccess` 。 這些值應該符合以下螢幕擷取畫面中的值：

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

如果缺少這些值，請重試 [新增 API 許可權一節](#provide-azure-digital-twins-api-permission)中的步驟。

## <a name="other-possible-steps-for-your-organization"></a>您組織的其他可能步驟

您的組織可能需要來自訂用帳戶擁有者/系統管理員的其他動作，才能成功設定應用程式註冊。 所需的步驟可能會根據您組織的特定設定而有所不同。

以下是訂用帳戶的擁有者/系統管理員可能需要執行的一些常見潛在活動。 您可以從 Azure 入口網站中的 [ [*Azure AD App 註冊*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ] 頁面執行這些作業和其他作業。
* 授與系統管理員同意以進行應用程式註冊。 您的組織可能會在您訂用帳戶內的所有應用程式註冊 Azure AD 中，全域開啟要求的系統 *管理員同意* 。 如果是這樣，則擁有者/系統管理員必須在應用程式註冊的 [ *API 許可權* ] 頁面上為您的公司選取此按鈕，應用程式註冊才會生效：

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::
  - 如果已成功授與同意，則 Azure 數位 Twins 的專案應該會顯示已授與_給**公司 (** _的*狀態*值) 
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::
* 啟用公用用戶端存取
* 設定 web 和桌面存取的特定回復 Url
* 允許隱含 OAuth2 authentication 流程

如需應用程式註冊及其不同安裝選項的詳細資訊，請參閱 [*使用 Microsoft 身分識別平臺註冊應用程式*](/graph/auth-register-app-v2)。

## <a name="next-steps"></a>後續步驟

在本文中，您會設定 Azure AD 的應用程式註冊，以使用 Azure 數位 Twins Api 來驗證用戶端應用程式。

接下來，請閱讀驗證機制的相關資訊，包括使用應用程式註冊的機制，以及其他不是：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)