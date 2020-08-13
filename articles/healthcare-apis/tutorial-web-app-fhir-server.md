---
title: Web 應用程式教學課程 - 設定 Azure API for FHIR
description: 此教學課程會逐步解說部署簡單 Web 應用程式的範例。 這第一個教學課程會說明 Azure API for FHIR 的必要條件和部署
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 4b39c32da04efa3782cb2166c8e1137029b21258
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852951"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>將 JavaScript 應用程式從 FHIR 服務部署至讀取資料
在本教學課程中，您將部署小型的 JavaScript 應用程式，以從 FHIR 服務讀取資料。 本教學課程中的步驟如下：
1. 部署 FHIR 伺服器
1. 註冊公開用戶端應用程式
1. 測試應用程式的存取權
1. 建立可讀取此 FHIR 資料的 Web 應用程式

## <a name="prerequisites"></a>必要條件
開始這組教學課程之前，您將需要下列項目：
1. Azure 訂用帳戶
1. Azure Active Directory 租用戶
1. [Postman](https://www.getpostman.com/) 已安裝

> [!NOTE]
> 在本教學課程中，FHIR 服務、Azure AD 應用程式和 Azure AD 使用者全都位於相同的 Azure AD 租用戶中。 如果您的情況不是如此，雖仍可遵循本教學課程，但可能需要查閱一些參考文件，以執行其他步驟。

## <a name="deploy-azure-api-for-fhir"></a>部署 Azure API for FHIR
本教學課程的第一個步驟是正確設定 Azure API for FHIR。

1. 部署 [Azure API for FHIR](fhir-paas-portal-quickstart.md)
1. 部署好 Azure API for FHIR 之後，請移至 Azure API for FHIR 並選取 [CORS]，以進行 [CORS](configure-cross-origin-resource-sharing.md) 設定。 
    1. 將 [來源] 設定為 *
    1. 將 [標頭] 設定為 *
    1. 在 [方法] 底下，選擇 [全選]
    1. 將 [存留期上限] 設定為 [600]

## <a name="next-steps"></a>後續步驟
現在您已部署好 Azure API for FHIR，因此已準備好註冊公用用戶端應用程式。

>[!div class="nextstepaction"]
>[註冊公用用戶端應用程式](tutorial-web-app-public-app-reg.md)
