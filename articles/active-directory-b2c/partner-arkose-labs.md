---
title: Azure Active Directory B2C 的 Arkose Labs
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 Arkose Labs，以協助防範 bot 攻擊、帳戶接管攻擊，以及詐騙帳戶的下機會。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4e5dfe26af7e1d92c20cf15e3519563e600b256
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385700"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Arkose Labs 的教學課程

在本教學課程中，您將瞭解如何整合 Azure AD B2C authentication 與 Arkose Labs。 Arkose Labs 可協助組織對抗 bot 攻擊、帳戶接管攻擊，以及詐騙帳戶的下機會。  

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。

## <a name="scenario-description"></a>案例描述

下圖說明 Arkose Labs 如何與 Azure AD B2C 整合。

![Arkose Labs 架構圖](media/partner-arkose-labs/arkose-architecture-diagram.png)

|   |   |
|---|---|
|1     | 使用者以先前建立的帳戶登入。 當使用者選取 [提交] 時，會出現 Arkose Labs 強制執行挑戰。 在使用者完成挑戰之後，狀態會傳送至 Arkose Labs 以產生權杖。        |
|2     |  Arkose Labs 會將權杖傳回 Azure AD B2C。       |
|3     |  提交登入表單之前，會將權杖傳送至 Arkose Labs 以進行驗證。       |
|4     |  Arkose 會從挑戰傳回成功或失敗結果。       |
|5     |  如果已成功完成挑戰，則會將登入表單提交給 Azure AD B2C，Azure AD B2C 完成驗證。       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>使用 Arkose Labs 上架

1. 一開始請先聯絡[Arkose Labs](https://www.arkoselabs.com/book-a-demo/)並建立帳戶。

2. 建立帳戶之後，流覽至 https://dashboard.arkoselabs.com/login 。

3. 在儀表板中，流覽至 [網站設定] 以尋找您的公開金鑰和私密金鑰。 稍後會需要此資訊才能設定 Azure AD B2C。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>第1部分–建立 blob 儲存體來儲存自訂 HTML

若要建立儲存體帳戶，請遵循下列步驟：  

1. 登入 Azure 入口網站。

2. 請務必使用包含 Azure 訂用帳戶的目錄。 在頂端功能表中選取 [ **目錄 + 訂**用帳戶] 篩選，然後選擇包含您訂用帳戶的目錄。 此目錄不同於包含您 Azure B2C 租用戶的目錄。

3. 選擇 [Azure 入口網站] 左上角的 [**所有服務**]，然後搜尋並選取 [ **儲存體帳戶**]。

4. 選取 [新增] ****。

5. 在 [ **資源群組**] 下，選取 [ **建立新**的]，輸入新資源群組的名稱，然後選取 **[確定]**。

6. 輸入儲存體帳戶的名稱。 您選擇的名稱在 Azure 中必須是唯一的、必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

7. 選取儲存體帳戶的位置，或接受預設位置。

8. 接受所有其他預設值，選取 [  **審核] & 建立**  >  **建立**]。

9. 建立儲存體帳戶之後，請選取 [移至資源] ****。

#### <a name="create-a-container"></a>建立容器

1. 在儲存體帳戶的 [總覽] 頁面上，選取 [  **blob**]。

2. 選取 [  **容器**]，輸入容器的名稱，選擇 [  **blob** （僅限 blob 的匿名讀取存取）]，然後選取 **[確定]**。

#### <a name="enable-cross-origin-resource-sharing-cors"></a>啟用跨原始來源資源分享（CORS）

瀏覽器中的 Azure AD B2C 程式碼會使用最新且標準的方法，從使用者流程中的指定 URL 載入自訂內容。 CORS 允許從其他網域要求網頁上受限制的資源。

1. 在功能表中，選取 [  **CORS**]。

2. 針對 [  **允許的來源**]，輸入  `https://your-tenant-name.b2clogin.com` 。 將您的租使用者名稱取代為您 Azure AD B2C 租使用者的名稱。 例如， `https://fabrikam.b2clogin.com`。 輸入您的租使用者名稱時，請全部使用小寫字母。

3. 針對 **允許的方法**，選取 [ **取得**]、[ **PUT**] 和 [ **OPTIONS**]。

4. 針對 [允許的標頭]****，輸入星號 (*)。

5. 針對 [ **公開的標頭**]，輸入星號（*）。

6. 針對 [最大壽命]****，輸入 200。

   ![Arkose Labs 註冊和登入](media/partner-arkose-labs/signup-signin-arkose.png)

7. 選取 [儲存]。

### <a name="part-2--set-up-a-back-end-server"></a>第2部分–設定後端伺服器

下載 Git Bash，並遵循下列步驟：

1. 請遵循指示來[建立 web 應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php)，直到訊息「恭喜！您已將第一個 PHP 應用程式部署至 App Service 「顯示」。

2. 開啟本機資料夾，**並將 node.js**檔案重新命名為**verify-token。**

3. 開啟剛重新命名的檔案 verify-token，並：

   a. 將內容取代為[GitHub 存放庫](https://github.com/ArkoseLabs/Azure-AD-B2C)中找到的 verify-token 檔案內容。

   b. 以從 Arkose Labs 儀表板取得的私密金鑰取代第3行上的 <private_key>。

4. 在本機終端機視窗中，在 Git 中認可您的變更，然後在 Git Bash 中輸入下列命令，將程式碼變更推送至 Azure：

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>第3部分–最後的設定

#### <a name="store-the-custom-html"></a>儲存自訂 HTML

1. 開啟儲存在[GitHub 存放庫](https://github.com/ArkoseLabs/Azure-AD-B2C)中的 index.html 檔案。

2. 將的所有實例取代 `<tenantname>` 為您的 b2C 租使用者名稱（換句話說， `<tenantname>.b2clogin.com` ）。 應該有四個實例。

3. 將取代為 `<appname>` 您在第2部分（步驟1）中建立的應用程式名稱。

   ![顯示 Arkose Labs Azure CLI 的螢幕擷取畫面](media/partner-arkose-labs/arkose-azure-cli.png)

4. `<public_key>`以您從 Arkose Labs 儀表板取得的公開金鑰取代64行上的。

5. 將 index.html 檔案上傳到上面所建立的 blob 儲存體中。

6. 移至 [**儲存體**  >  **容器**  >  **上傳**]。

#### <a name="set-up-azure-ad-b2c"></a>設定 Azure AD B2C

> [!NOTE]
> 如果您還沒有租用戶，請[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到 Azure 訂用帳戶。

1. 根據[這裡](tutorial-create-user-flows.md)的資訊建立使用者流程。 當您到達 [**測試使用者流程**] 區段時停止。

2. 在您的[使用者流程](user-flow-javascript-overview.md)中啟用 JavaScript。

3. 在相同的 [使用者流程] 頁面中，啟用 [自訂頁面 URL]：移至 [**使用者流程**  >  ]**頁面配置**  >  **使用 [自訂頁面內容**  =  **] [是**  >  **插入自訂頁面 URL**
此自訂頁面 URL 是從 blob 儲存體內的 index.html 檔案位置取得  

   ![顯示 Arkose Labs 儲存體 url 的螢幕擷取畫面](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 [Azure AD B2C 租使用者]，然後在 [**原則**] 下選取 [**使用者流程**]。

2. 選取您先前建立的使用者流程。

3. 選取 [**執行使用者流程**]，然後選取設定：

   a. **應用程式**-選取已註冊的應用程式（範例是 JWT）。

   b. **回復 url** -選取 [重新導向 url]。

   c. 選取 [執行使用者流程]。

4. 請完成註冊流程並建立帳戶。

5. 登出。

6. 請完成登入流程。

7. 當您選取 [**繼續**] 之後，將會出現 Arkose Labs 謎題。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](custom-policy-overview.md)

- [開始使用 Azure AD B2C 中的自訂原則](custom-policy-get-started.md?tabs=applications)
