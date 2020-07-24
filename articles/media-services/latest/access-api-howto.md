---
title: 開始使用 Azure AD 驗證
description: 了解如何存取 Azure Active Directory (Azure AD) 驗證，以使用 Azure 媒體服務 API。
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
ms.date: 05/21/2020
ms.author: juliako
ms.openlocfilehash: 33c762e2f2a9bed90991f7b85e0fb83fbb43765c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090156"
---
# <a name="get-credentials-to-access-media-services-api"></a>取得認證以存取媒體服務 API  

當您使用 Azure AD 驗證存取 Azure 媒體服務 API 時，會有兩個驗證選項：

- **服務主體驗證** (建議)

    驗證服務。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式：Web 應用程式、函數應用程式、邏輯應用程式、API 或微服務。
- **使用者驗證**

    驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。 

本文說明取得認證以存取媒體服務 API 的步驟。 從下列索引標籤中選擇。

## <a name="prerequisites"></a>Prerequisites

- 一個 Azure 帳戶。 如果您沒有帳戶，請先從 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 
- 媒體服務帳戶。 如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure 媒體服務帳戶](create-account-howto.md)。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

### <a name="api-access"></a>API 存取 

[API 存取] 頁面可讓您選取要用來連線到 API 的驗證方法。 此頁面也會提供連線到 API 所需的值。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的媒體服務帳戶。
2. 選取如何連線到媒體服務 API。
3. 在 [連線到媒體服務 API] 下，選取要連線的媒體服務 API 版本 (V3 是最新的服務版本)。

### <a name="service-principal-authentication--recommended"></a>服務主體驗證 (建議)

使用 Azure Active Directory (Azure AD) 應用程式和祕密來驗證服務。 建議呼叫媒體服務 API 的所有中介層服務均使用此做法。 例如 Web 應用程式、函式、Logic Apps、API 或微服務。 這是建議的驗證方法。

#### <a name="manage-your-azure-ad-app-and-secret"></a>管理您的 Azure AD 應用程式及祕密

**管理您的 AAD 應用程式和祕密**區段可讓您選取或建立新的 Azure AD 應用程式，並產生秘密。 基於安全性考量，關閉刀鋒視窗後即無法顯示祕密。 應用程式會使用應用程式識別碼和祕密來進行驗證，以取得媒體服務的有效權杖。

請確定您有足夠的權限，可以使用 Azure AD 租用戶註冊應用程式，並將應用程式指派至 Azure 訂用帳戶中的角色。 如需詳細資訊，請參閱[必要權限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

#### <a name="connect-to-media-services-api"></a>連線到媒體服務 API

**連線到媒體服務 API** 會提供您用來連線服務主體應用程式的值。 您可以取得文字值，或複製 JSON 或 XML 區塊。

### <a name="user-authentication"></a>使用者驗證

此選項可用來驗證使用應用程式與媒體服務資源互動之 Azure Active Directory 的員工或成員。 互動式應用程式應該會先提示使用者輸入使用者的認證。 這種驗證方法只應用於管理應用程式。

#### <a name="connect-to-media-services-api"></a>連線到媒體服務 API

複製您的認證，以將使用者應用程式從**連線到媒體服務 API**區段連線。 您可以取得文字值，或複製 JSON 或 XML 區塊。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>後續步驟

[教學課程：使用媒體服務 v3 上傳、編碼和串流處理影片](stream-files-tutorial-with-api.md)。
