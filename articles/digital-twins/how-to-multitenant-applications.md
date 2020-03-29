---
title: 啟用多租戶應用程式 - Azure 數位孿生 |微軟文檔
description: 如何為 Azure Digital Twins 設定多租用戶 Azure Active Directory 應用程式。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264928"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>使用 Azure Digital Twins 啟用多租用戶應用程式

在 Azure Digital Twins 上建置解決方案的開發人員，可能會發現他們想要使用單一服務或解決方案來支援多個客戶。 事實上，「多租用戶」** 應用程式是最常見的 Azure Digital Twins 設定。

本文件描述如何設定 Azure Digital Twins 應用程式，以支援數個 Azure Active Directory 租用戶和客戶。

## <a name="multitenancy"></a>多租用戶

「多租用戶」** 資源是可支援多個客戶的單一佈建執行個體。 每個客戶都各自擁有自己的資料和權限。 每個客戶的體驗都彼此隔離，使其應用程式「檢視」皆不同。

若要深入了解多租用戶，請閱讀 [Azure 中的多租用戶應用程式](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)。

## <a name="problem-scenario"></a>問題案例

在此案例中，請考慮建立 Azure Digital Twins 解決方案的開發人員 (**開發人員**)，以及使用該解決方案的客戶 (**客戶**)：

- **開發人員**有包含 Azure Active Directory 租用戶的 Azure 訂用帳戶。
- **開發人員**會將 Azure Digital Twins 執行個體部署到他們的 Azure 訂用帳戶中。 Azure Active Directory 會在**開發人員**的 Azure Active Directory 租用戶自動建立服務主體。
- **開發人員**的 Azure Active Directory 租用戶內的使用者可以接著從 Azure Digital Twins [取得 OAuth 2.0 權杖](./security-authenticating-apis.md)。
- **開發人員**現在會建立與 Azure Digital Twins 管理 API 直接整合的行動應用程式。
- **開發人員**允許**客戶**使用該行動應用程式。
- **客戶**必須獲得授權，才能在**開發人員**的應用程式內使用 Azure Digital Twins 管理 API。

問題：

- 當**客戶**登入**開發人員**的應用程式時，應用程式無法使用 Azure Digital Twins 管理 API 為**客戶**的使用者取得權杖。
- Azure Active Directory 中會發出例外狀況，指出該 Azure Digital Twins 在**客戶**的目錄內無法識別。

## <a name="problem-solution"></a>問題解決方案

若要解決上述問題案例，需要採取下列動作以在**客戶**的 Azure Active Directory 租用戶內建立 Azure Digital Twins 服務主體：

- 如果**客戶**尚未有包含 Azure Active Directory 租用戶的 Azure 訂用帳戶：

  - **客戶**的 Azure Active Directory 租用戶管理員必須取得[隨用隨付 Azure 訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。
  - **客戶**的 Azure Active Directory 租用戶管理員接著必須[連結其租用戶與新訂用帳戶](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)。

- **客戶**的 Azure Active Directory 租用戶管理員必須在 [Azure 入口網站](https://portal.azure.com)上執行下列步驟：

  1. 在頂部 Azure 搜索欄位中搜索**訂閱**。 選擇**訂閱**。
  1. 選取有要用於**開發人員**之應用程式的 Azure Active Directory 租用戶的訂用帳戶。

     [![Azure Active Directory 訂用帳戶](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. 選擇**資來源提供者**。
  1. 搜尋 **Microsoft.IoTSpaces**。
  1. 選取 [註冊]****。

     [![Azure Active Directory 資源提供者](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>後續步驟

- 若要深入了解如何使用 Azure Digital Twins 的使用者定義函式，請閱讀[如何建立 Azure Digital Twins 使用者定義函數](./how-to-user-defined-functions.md)。

- 若要了解如何使用角色型存取控制進一步保護具有角色指派的應用程式，請參閱[如何建立及管理 Azure Digital Twins 的角色型存取控制](./security-create-manage-role-assignments.md)。
