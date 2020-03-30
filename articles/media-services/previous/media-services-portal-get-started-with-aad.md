---
title: 利用 Azure 入口網站開始使用 Azure AD 驗證 | Microsoft Docs
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
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330663"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>利用 Azure 入口網站開始使用 Azure AD 驗證

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，請參閱[從 v2 到 v3 的遷移指南](../latest/migrate-from-v2-to-v3.md)

了解如何使用 Azure 入口網站存取 Azure Active Directory (Azure AD) 驗證，以存取 Azure 媒體服務 API。

## <a name="prerequisites"></a>Prerequisites

- 一個 Azure 帳戶。 如果您沒有帳戶，請先從 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 
- 媒體服務帳戶。 如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure 媒體服務帳戶](media-services-portal-create-account.md)。

當您使用 Azure AD 驗證搭配 Azure 媒體服務 時，會有兩個驗證選項：

- **服務主體身份驗證**。 驗證服務。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式：Web 應用程式、函數應用程式、邏輯應用程式、API 或微服務。
- **使用者驗證**。 驗證使用應用程式與媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。 

## <a name="access-the-media-services-api"></a>存取媒體服務 API

此頁面允許您選擇要用於連接到 API 的身份驗證方法。 該頁還提供連接到 API 所需的值。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的媒體服務帳戶。
2. 選取如何連線到媒體服務 API。
3. 在 **"連接到媒體服務 API"** 下，選擇要連接到的媒體服務 API 版本。

## <a name="service-principal-authentication--recommended"></a>服務主體身份驗證（推薦）

使用 Azure 活動目錄 （Azure AD） 應用和機密對服務進行身份驗證。 建議對調用媒體服務 API 的任何中介層服務這樣做。 示例包括 Web 應用、函數、邏輯應用、API 和微服務。 這是推薦的身份驗證方法。

### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 應用和機密

**通過"管理 AAD 應用和機密**部分"，可以選擇或創建新的 Azure AD 應用並生成機密。 出於安全目的，關閉刀片後無法顯示機密。 應用程式使用應用程式 ID 和機密進行身份驗證以獲取媒體服務的有效權杖。

確保有足夠的許可權向 Azure AD 租戶註冊應用程式，並將應用程式分配給 Azure 訂閱中的角色。 有關詳細資訊，請參閱[必需許可權](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

**連接到媒體服務 API**為您提供用於連接服務主體應用程式的值。 您可以獲取文本值或複製 JSON 或 XML 塊。

## <a name="user-authentication"></a>使用者驗證

此選項可用於對使用應用與媒體服務資源交互的 Azure 活動目錄的員工或成員進行身份驗證。 互動式應用程式應該會先提示使用者輸入使用者的認證。 此身份驗證方法應僅用於管理應用程式。

### <a name="connect-to-media-services-api"></a>連接到媒體服務 API

從 **"連接到媒體服務 API"** 部分複製您的憑據以連接使用者應用程式。 您可以獲取文本值或複製 JSON 或 XML 塊。

## <a name="next-steps"></a>後續步驟

開始使用[上傳檔案至您的帳戶](media-services-portal-upload-files.md)。
