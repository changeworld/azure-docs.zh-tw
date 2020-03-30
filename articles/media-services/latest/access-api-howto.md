---
title: 開始使用 Azure AD 身份驗證
description: 瞭解如何訪問 Azure 活動目錄 （Azure AD） 身份驗證以使用 Azure 媒體服務 API。
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
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478249"
---
# <a name="get-credentials-to-access-media-services-api"></a>獲取訪問媒體服務 API 的憑據  

使用 Azure AD 身份驗證訪問 Azure 媒體服務 API 時，有兩個身份驗證選項：

- **服務主體身份驗證**（推薦）

    驗證服務。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式：Web 應用程式、函數應用程式、邏輯應用程式、API 或微服務。
- **使用者驗證**

    驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。 

本文介紹獲取憑據訪問媒體服務 API 的步驟。 從以下選項卡中進行選擇。

## <a name="prerequisites"></a>Prerequisites

- 一個 Azure 帳戶。 如果您沒有帳戶，請先從 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 
- 媒體服務帳戶。 如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure 媒體服務帳戶](create-account-howto.md)。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

### <a name="api-access"></a>API 存取 

API**訪問**頁允許您選擇要用於連接到 API 的身份驗證方法。 該頁還提供連接到 API 所需的值。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的媒體服務帳戶。
2. 選取如何連線到媒體服務 API。
3. 在 **"連接到媒體服務 API"** 下，選擇要連接到的媒體服務 API 版本（V3 是服務的最新版本）。

### <a name="service-principal-authentication--recommended"></a>服務主體身份驗證（推薦）

使用 Azure 活動目錄 （Azure AD） 應用和機密對服務進行身份驗證。 建議對調用媒體服務 API 的任何中介層服務這樣做。 示例包括 Web 應用、函數、邏輯應用、API 和微服務。 這是推薦的身份驗證方法。

#### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 應用和機密

**通過"管理 AAD 應用和機密**部分"，可以選擇或創建新的 Azure AD 應用並生成機密。 出於安全目的，關閉刀片後無法顯示機密。 應用程式使用應用程式 ID 和機密進行身份驗證以獲取媒體服務的有效權杖。

確保有足夠的許可權向 Azure AD 租戶註冊應用程式，並將應用程式分配給 Azure 訂閱中的角色。 有關詳細資訊，請參閱[必需許可權](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

#### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

**連接到媒體服務 API**為您提供用於連接服務主體應用程式的值。 您可以獲取文本值或複製 JSON 或 XML 塊。

### <a name="user-authentication"></a>使用者驗證

此選項可用於對使用應用與媒體服務資源交互的 Azure 活動目錄的員工或成員進行身份驗證。 互動式應用程式應該會先提示使用者輸入使用者的認證。 此身份驗證方法應僅用於管理應用程式。

#### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

從 **"連接到媒體服務 API"** 部分複製您的憑據以連接使用者應用程式。 您可以獲取文本值或複製 JSON 或 XML 塊。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>後續步驟

[教程：使用媒體服務 v3 上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)。
