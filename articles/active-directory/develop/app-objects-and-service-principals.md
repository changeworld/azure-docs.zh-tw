---
title: Azure AD 中的應用程式 & 服務主體 |蔚藍
titleSuffix: Microsoft identity platform
description: 了解 Azure Active Directory 中應用程式物件與服務主體物件之間的關聯性。
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: db25e0a9ebe01a45c594fe214efcdd3551cf6c14
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449360"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory 中的應用程式和服務主體物件

本文說明 Azure Active Directory 中的應用程式註冊、應用程式物件和服務主體：它們的內容、使用方式，以及它們彼此之間的關聯方式。 另外也會顯示多租使用者範例案例，以說明應用程式的應用程式物件和對應的服務主體物件之間的關聯性。

## <a name="application-registration"></a>應用程式註冊
為了將身分識別和存取管理功能委派給 Azure AD，必須向 Azure AD [租](developer-glossary.md#tenant)使用者註冊應用程式。 當您向 Azure AD 註冊應用程式時，您會建立應用程式的身分識別設定，讓它能夠與 Azure AD 整合。 當您在 [Azure 入口網站][AZURE-Portal]中註冊應用程式時，您可以選擇它是單一租使用者 (只能在您的租使用者中存取) 或可在其他租使用者中存取多租使用者 () ，也可以選擇性地設定將存取權杖傳送至 (的重新導向 URI) 。

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="[註冊應用程式] 窗格 Azure 入口網站的螢幕擷取畫面":::

當您完成應用程式註冊時，您會有應用程式的全域唯一實例 (應用程式物件) 存在於您的主租使用者或目錄中。  您也會有應用程式的全域唯一識別碼， (應用程式或用戶端識別碼) 。  在入口網站中，您可以新增秘密或憑證和範圍來讓您的應用程式運作、在登入對話方塊中自訂應用程式的商標等等。

如果您在入口網站中註冊應用程式，則會自動在您的主租使用者中建立應用程式物件和服務主體物件。  如果您使用 Microsoft Graph Api 來註冊/建立應用程式，則建立服務主體物件是個別的步驟。

## <a name="application-object"></a>應用程式物件
Azure AD 應用程式是由其唯一一個應用程式物件所定義，該物件位於應用程式註冊所在的 Azure AD 租使用者中 (稱為應用程式的「home」租使用者) 。  應用程式物件會用來做為範本或藍圖，以建立一或多個服務主體物件。  在使用應用程式的每個租使用者中建立服務主體。 與物件導向程式設計中的類別類似，應用程式物件具有一些靜態屬性，這些屬性會套用到所有已建立的服務主體， (或應用程式實例) 。

應用程式物件會描述應用程式的三個層面：服務如何發出權杖以存取應用程式、應用程式可能需要存取的資源，以及應用程式可以採取的動作。

[Azure 入口網站][AZURE-Portal]中的**應用程式註冊**分頁會用來列出和管理您 home 租使用者中的應用程式物件。

![應用程式註冊分頁](./media/app-objects-and-service-principals/app-registrations-blade.png)

Microsoft Graph [應用程式實體][MS-Graph-App-Entity] 會定義應用程式物件屬性的架構。

## <a name="service-principal-object"></a>服務主體物件
若要存取受到 Azure AD 租用戶保護的資源，需要存取權的實體必須以安全性主體呈現。 這項需求適用于使用者 (使用者主體) 和 (服務主體) 的應用程式。 安全性主體會定義 Azure AD 租用戶中使用者/應用程式的存取原則和權限。 此動作可啟用核心功能，例如登入期間的使用者/應用程式驗證，以及資源存取期間的授權。

服務主體是單一租使用者或目錄中全域應用程式物件的本機標記法或應用程式實例。 服務主體是從應用程式物件建立的實體實例，並繼承該應用程式物件的特定屬性。  服務主體會建立于使用應用程式的每個租使用者中，並參考全域唯一的應用程式物件。  服務主體物件會定義應用程式實際上可在特定租使用者中進行的動作、誰可以存取應用程式，以及應用程式可以存取哪些資源。

當應用程式擁有權限可存取租用戶中的資源時 (通過註冊時或[同意](developer-glossary.md#consent))，服務主體物件就會隨即建立。 您也可以使用 [Azure PowerShell](howto-authenticate-service-principal-powershell.md)、 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)、 [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http)、 [Azure 入口網站][AZURE-Portal]和其他工具，在租使用者中建立服務主體物件。  使用入口網站時，當您註冊應用程式時，會自動建立服務主體。

入口網站中的 [ **企業應用程式** ] 分頁會用來列出和管理租使用者中的服務主體。 您可以看到服務主體的許可權、使用者同意的許可權、使用者已完成該同意、登入資訊等。

![企業應用程式分頁](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

Microsoft Graph [ServicePrincipal 實體][MS-Graph-Sp-Entity] 會定義服務主體物件屬性的架構。

## <a name="relationship-between-application-objects-and-service-principals"></a>應用程式物件和服務主體之間的關聯性

應用程式物件是應用程式的 *全域* 標記法，可跨所有租使用者使用，而服務主體是在特定租使用者中使用的 *本機* 標記法。

應用程式物件就像範本，可從中「衍生」** 通用和預設屬性，用以建立相對應的服務主體物件。 因此，應用程式物件與軟體應用程式之間存在 1:1 關聯性，而與其對應的服務主體物件之間存在一對多關聯性。

每一個會用到應用程式的租用戶中必須建立服務主體，才能讓它建立身分識別來登入及/或存取租用戶所保護的資源。 單一租用戶的應用程式只能有一個服務主體 (在其主租用戶中)，並在應用程式註冊期間建立和同意使用。 如果是多租用戶 Web 應用程式/API，則在使用者已同意使用它的每個租用戶中，還會建立服務主體。

> [!NOTE]
> 您對應用程式物件所做的任何變更也只會反映於它在應用程式的主要租用戶 (其註冊所在租用戶) 中的服務主體物件。 就多租用戶應用程式而言，對應用程式物件所做的變更，必須等到透過[應用程式存取面板](https://myapps.microsoft.com)移除存取權再重新授權之後，才會反映在任何取用者租用戶的服務主體物件中。
>
> 也請注意，依預設，原生應用程式會註冊為多租用戶。

## <a name="example"></a>範例

下圖說明應用程式的應用程式物件與對應的服務主體物件之間的關係，是以一個稱為「HR 應用程式」 **** 的範例多租用戶應用程式為背景。 此範例案例中有三個 Azure AD 租用戶︰

- **Adatum** -開發**HR 應用程式**的公司所使用的租使用者
- **Contoso** -contoso 組織所使用的租使用者，也就是**HR 應用程式**的取用者
- **Fabrikam** - Fabrikam 組織所使用的租用戶，其亦會取用 **HR 應用程式**

![應用程式物件與服務主體物件之間的關聯性](./media/app-objects-and-service-principals/application-objects-relationship.svg)

在此範例案例中：

| 步驟 | 描述 |
|------|-------------|
| 1    | 在應用程式的主要租用戶中建立應用程式和服務主體物件的程序。 |
| 2    | 當 Contoso 和 Fabrikam 的系統管理員完成同意，系統就會在其公司的 Azure AD 租用戶中建立服務主體物件，並指派系統管理員所授與的權限。 也請注意，HR 應用程式可能會設定/設計為允許由使用者同意以進行個人使用。 |
| 3    | HR 應用程式的取用者租用戶 (Contoso 和 Fabrikam) 都分別擁有自己的服務主體物件。 每個均代表他們在執行階段的應用程式執行個體使用，其中皆受到個別系統管理員所同意的權限控管。 |

## <a name="next-steps"></a>後續步驟

- 您可以使用 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 來查詢應用程式和服務主體物件。
- 您可以使用 Microsoft Graph API、 [Azure 入口網站的][AZURE-Portal] 應用程式資訊清單編輯器，或 [Azure AD PowerShell Cmdlet](/powershell/azure/?view=azureadps-2.0)（以其 OData [應用程式實體][MS-Graph-App-Entity]代表）來存取應用程式的應用程式物件。
- 您可以透過 Microsoft Graph API 或 [Azure AD PowerShell Cmdlet](/powershell/azure/?view=azureadps-2.0)（以其 OData [ServicePrincipal 實體][MS-Graph-Sp-Entity]代表）來存取應用程式的服務主體物件。

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com