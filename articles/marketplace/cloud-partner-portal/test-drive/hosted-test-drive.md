---
title: 託管試用產品 |Azure Marketplace
description: 如何設定及維護 Marketplace 裝載的試用產品
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278258"
---
# <a name="hosted-test-drive"></a>裝載的試用產品

「裝載的試用產品」透過由 Microsoft 提供服務裝載環境免除了設定的複雜性，而且它會維護執行試用產品使用者佈建及取消佈建的服務。 此文章的目標對象是在 AppSource 上提供供應項目或發行新供應項目並想要提供「裝載的試用產品」的發行者，「裝載的試用產品」會與 Dynamics 365 for Customer Engagement、Dynamics 365 for Finance 與 Operations 或 Dynamics 365 Business Central 執行個體連結。

## <a name="how-to-publish-a-test-drive"></a>如何發佈試用產品

瀏覽到現有供應項目或建立新供應項目。

從側邊功能表選取 [試用產品] 選項。

針對 [啟用試用產品]\'\' 選項選擇 [是]\'\'。

在 [詳細資料] 區段中提供下列欄位。

- **描述**：提供您試用產品的概觀。 佈建試用產品時，此文字會顯示給使用者。 此欄位支援 HTML，因此您可以提供格式豐富的內容。
- **使用者手冊**：上傳詳細的使用者手冊 (.pdf 類型的檔案)，協助試用產品使用者了解如何使用您的應用程式。
- **試用產品示範影片**：(選擇性) 上傳示範您應用程式的影片。

使用[這裡](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)的指示， 將在您的租用戶中佈建及取消佈建試用產品使用者的權限授與 AppSource。

在此步驟中，您將會產生 \'Azure AD App 識別碼\'與 \'Azure AD App 金鑰\'值，如下所示。

在 [技術設定]\'\' 區段中提供下列欄位：

- **** 試用產品類型\'：選擇 [Microsoft 裝載 (範例 Dynamics 365 for Customer Engagement)] 選項。 這表示 Microsoft 將會裝載並維護執行試用產品使用者佈建及取消佈建的服務。
- **並行試用產品數目上限**：將此欄位設定為可在任何指定時間點擁有作用中試用產品的並行使用者數目。 當使用者的試用產品為作用中時，每個使用者都會取用 Dynamics 授權，因此您必須確定您至少有這麼多的 Dynamics 授權可供試用產品使用者使用。 建議值為 3-5 個。
- **試用產品持續時間 (小時)**：將此欄位設定為使用者的試用產品將有效的小時數。 在經過設定的小時之後，使用者將從您的租用戶取消佈建。 建議值為 2-24 小時，視您應用程式的複雜度而定。 若使用者用完其時間而且想要再次存取試用產品，一律可以要求進行另一次試用產品。
- **執行個體 URL**：提供試用產品使用者開始嘗試試用產品時，一開始將會連線到的 URL。 這通常是您的應用程式與範例資料安裝所在之 Dynamics 365 執行個體的 URL。 範例值： HTTPs：\//testdrive.crm.dynamics.com
- **Azure AD 租用戶識別碼**：提供您 Dynamics 365 執行個體的 Azure Tenant 租用戶識別碼。 若要擷取此值，請登入 Azure 入口網站並瀏覽到 \'Azure Active Directory\' -\> 從功能表刀鋒視窗選取 [屬性]-\> 複製目錄識別碼。 範例值：72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD App 識別碼**：您在步驟 7 中建立之 Azure AD App 的識別碼。\ 範例值： 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD App 金鑰**步驟 7 中建立的 Azure AD App 祕密。\ E範例值： IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD 租用戶名稱**：提供您 Dynamics 365 執行個體的 Azure Tenant 租用戶名稱。 使用 \<tenantname.\>onmicrosoft.com 格式。 範例值：testdrive.onmicrosoft.com
- **執行個體 Web API URL**：提供您 Dynamics 365 執行個體的 Web API URL。 您可以透過登入您的 Microsoft Dynamics 365 執行個體並瀏覽到 [設定 -\> 自訂 -\> 開發人員資源 -\> 執行個體 Web API (複製此 URL)] 以擷取此值。 範例值： HTTPs：\//testdrive.crm.dynamics.com/api/data/v9。0
- **角色名稱**：提供您已針對試用產品建立之自訂 Dynamics 365 安全性角色的名稱。 這是在使用者的試用產品期間將指派給使用者的角色。 範例值：testdriverole

## <a name="next-steps"></a>後續步驟

準備好**發行**您的供應項目時，在您的應用程式通過認證之後，您將會有您供應項目的**預覽版**。 在 UI 中啟動試用產品，並確認您的試用產品正確執行。 對您的預覽版供應項目感到滿意之後，就可以讓它**上架**！
