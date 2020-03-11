---
title: 利用 Azure 入口網站開始使用 Azure AD 驗證
description: 了解如何使用 Azure 入口網站存取 Azure Active Directory (Azure AD) 驗證，以使用 Azure 媒體服務 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 03643ea3f96448e5ec9693cd8ce9589472e8e3f2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082739"
---
# <a name="use-the-azure-portal-to-get-credentials-to-access-media-services-api"></a>使用 Azure 入口網站取得認證以存取媒體服務 API  

當您使用 Azure AD authentication 來存取 Azure 媒體服務 API 時，您有兩個驗證選項：

- **服務主體驗證**。 驗證服務。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式：Web 應用程式、函數應用程式、邏輯應用程式、API 或微服務。
- **使用者驗證**。 驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。 

本文說明如何使用 Azure 入口網站取得認證，以存取媒體服務 API。

## <a name="prerequisites"></a>Prerequisites

- 一個 Azure 帳戶。 如果您沒有帳戶，請先從 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 
- 媒體服務帳戶。 如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure 媒體服務帳戶](create-account-portal.md)。

## <a name="api-access"></a>API 存取 

[ **API 存取**] 頁面可讓您選取要用來連線到 API 的驗證方法。 此頁面也會提供連接到 API 所需的值。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的媒體服務帳戶。
2. 選取如何連線到媒體服務 API。
3. 在 **[連線至媒體服務 API]** 底下，選取您想要連線的媒體服務 API 版本（V3 是最新版本的服務）。

## <a name="service-principal-authentication--recommended"></a>服務主體驗證（建議）

使用 Azure Active Directory （Azure AD）應用程式和密碼來驗證服務。 對於呼叫媒體服務 API 的任何仲介層服務，這是建議的做法。 範例包括 Web Apps、函數、Logic Apps、Api 和微服務。 這是建議的驗證方法。

### <a name="manage-your-azure-ad-app-and-secret"></a>管理您的 Azure AD 應用程式和密碼

[**管理您的 AAD 應用程式和密碼**] 區段可讓您選取或建立新的 Azure AD 應用程式，並產生秘密。 基於安全考慮，在關閉分頁之後，就無法顯示秘密。 應用程式會使用應用程式識別碼和密碼來進行驗證，以取得媒體服務的有效權杖。

請確定您有足夠的許可權可以向您的 Azure AD 租使用者註冊應用程式，並將應用程式指派給 Azure 訂用帳戶中的角色。 如需詳細資訊，請參閱[必要許可權](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

[連線**至媒體服務 API** ] 提供您用來連接服務主體應用程式的值。 您可以取得文字值，或複製 JSON 或 XML 區塊。

## <a name="user-authentication"></a>使用者驗證

此選項可用來驗證使用應用程式與媒體服務資源互動之 Azure Active Directory 的員工或成員。 互動式應用程式應該會先提示使用者輸入使用者的認證。 此驗證方法應該只用于管理應用程式。

### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

複製您的認證，以從 [**連接到媒體服務 API** ] 區段連線到您的使用者應用程式。 您可以取得文字值，或複製 JSON 或 XML 區塊。

## <a name="next-steps"></a>後續步驟

[教學課程：使用媒體服務 V3 上傳、編碼及串流](stream-files-tutorial-with-api.md)影片。
