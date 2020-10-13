---
title: 快速入門：從 Microsoft 身分識別平台中移除註冊的應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解如何移除使用 Microsoft 身分識別平台所註冊的應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612382"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>快速入門：移除使用 Microsoft 身分識別平台所註冊的應用程式

已使用 Microsoft 身分識別平台註冊應用程式的企業開發人員和軟體即服務 (SaaS) 提供者，可能需要移除應用程式的註冊。

在本快速入門中，您將了解如何：

* 移除您或組織所編寫的應用程式
* 移除其他組織所編寫的應用程式

## <a name="prerequisites"></a>Prerequisites

* 完成[快速入門：使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>移除您或組織所編寫的應用程式

您或組織所註冊的應用程式會同時由租用戶中的應用程式物件與服務主體物件來表示。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](./app-objects-and-service-principals.md)。

若要刪除應用程式，您必須列名為應用程式的擁有者或具有管理員權限。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [Azure Active Directory]**** 服務，然後選取 [應用程式註冊]****。 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀]**** 頁面。
1. 從 [概觀]**** 頁面上，選取 [刪除]****。
1. 選取 [是]**** 以確認要刪除應用程式。

## <a name="remove-an-application-authored-by-another-organization"></a>移除其他組織所編寫的應用程式

如果您正在檢視租用戶內容中的 [應用程式註冊]****，則出現在 [所有應用程式]**** 索引標籤下方的應用程式子集，均來自另一個租用戶，並且是在同意程序進行期間註冊到租用戶的。 更具體來說，它們僅由您租用戶中的服務主體物件表示，而沒有對應的應用程式物件。 如需有關應用程式物件與服務主體物件之差異的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件](./app-objects-and-service-principals.md)。

為了移除應用程式對目錄的存取權 (在授與同意之後)，公司系統管理員必須移除其服務主體。 系統管理員必須擁有全域系統管理員存取權，才能透過 Azure 入口網站移除應用程式或使用 [Azure AD PowerShell Cmdlet](https://go.microsoft.com/fwlink/?LinkId=294151) 移除存取權。

## <a name="next-steps"></a>後續步驟

深入了解 Microsoft 身分識別平台中的應用程式和服務主體物件：

> [!div class="nextstepaction"]
> [Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)