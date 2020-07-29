---
title: 使用 LexisNexis 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 LexisNexis，這是一種分析和身分識別驗證服務，可用來驗證使用者識別，並根據使用者的裝置提供完整的風險評量。
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6f5b6bfdb523a22fc4dd9593bfec556da7493aa9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371187"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 LexisNexis 的教學課程

在此範例教學課程中，我們會提供如何整合 Azure AD B2C 與[LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)的指引。 LexisNexis 提供各種不同的解決方案，您可以在[這裡](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)找到它們。 在此範例教學課程中，我們將涵蓋來自 LexisNexis 的**ThreatMetrix**解決方案。 ThreatMetrix 是程式碼剖析和身分識別驗證服務。 它可用來驗證使用者識別，並根據使用者的裝置提供完整的風險評量。

此整合會根據使用者資訊的幾個部分來進行分析，而這會在註冊流程期間提供。 ThreatMetrix 會決定是否允許使用者繼續登入。 下列屬性會在 ThreatMetrix 的風險分析中納入考慮：

- 電子郵件
- 電話號碼
- 從使用者的電腦收集到的程式碼剖析資訊

## <a name="prerequisites"></a>先決條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

## <a name="scenario-description"></a>案例描述

ThreatMetrix 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- ThreatMetrix – ThreatMetrix 服務會接受使用者提供的輸入，並將其與從使用者電腦收集的分析資訊結合，以驗證使用者互動的安全性。

- 自訂 Rest API –此 API 會實現 Azure AD B2C 與 ThreatMetrix 服務之間的整合。

下列架構圖顯示執行的方式。

![lexisnexis 的螢幕擷取畫面-架構-圖表](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|步驟 | 說明 |
|:--------------|:-------------|
|1. | 使用者抵達登入頁面。 使用者選取 [註冊] 來建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫仲介層 API，並在使用者屬性上傳遞。
| 3. | 中介層 API 會收集使用者屬性，並將其轉換成 LexisNexis API 可能會使用的格式。 然後，將它傳送至 LexisNexis。  
| 4. | LexisNexis 會取用資訊並加以處理，以根據風險分析來驗證使用者識別。 然後，它會將結果傳回到中介層 API。
| 5. | 中介層 API 會處理資訊，並將相關資訊傳回 Azure AD B2C。
| 6. | Azure AD B2C 從仲介層 API 接收資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功回應，則會驗證使用者並授與存取權。

## <a name="onboard-with-lexisnexis"></a>使用 LexisNexis 上架

1. 若要建立 LexisNexis 帳戶，請聯絡[LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. 建立符合您需求的 LexisNexis 原則。 使用[這裡](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)提供的檔。

>[!NOTE]
> 稍後將會使用原則的名稱。

建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>使用 LexisNexis 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 API 程式碼部署至 Azure 服務。 程式碼可以從 Visual Studio 發佈，請遵循這些[指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。

>[!NOTE]
>您將需要已部署之服務的 URL，才能使用必要的設定來設定 Azure AD。

### <a name="part-2---configure-the-api"></a>第2部分-設定 API

應用程式設定可以[在 Azure 中的 App service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)。  使用此方法時，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須將下列設定提供給 Rest API：

| 應用程式設定 | 來源 | 注意 |
| :-------- | :------------| :-----------|
|ThreatMetrix： Url | ThreatMetrix 帳戶設定 |     |
|ThreatMetrix： OrgId | ThreatMetrix 帳戶設定 |     |
|ThreatMetrix： ApiKey |ThreatMetrix 帳戶設定|  |
|ThreatMetrix：原則 | 在 ThreatMetrix 中建立的原則名稱 | |
| BasicAuth:ApiUsername |定義 API 的使用者名稱| 使用者名稱將用於 Azure AD B2C 設定
| BasicAuth:ApiPassword | 定義 API 的密碼 | 密碼將用於 Azure AD B2C 設定

### <a name="part-3---deploy-the-ui"></a>第3部分-部署 UI

這個解決方案會使用 Azure AD B2C 載入的自訂 UI 範本。 這些 UI 範本會執行直接傳送至 ThreatMetrix 服務的分析。

請參閱這些[指示](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough)，將包含的 UI 檔案部署到 blob 儲存體帳戶。 這些指示包括設定 blob 儲存體帳戶、設定 CORS，以及啟用公用存取。

UI 是以海洋藍色頁面範本為基礎。 UI 中的所有連結都應該更新，以參考已部署的位置。 在 UI 資料夾中，尋找並 https://yourblobstorage/blobcontainer 將取代為已部署的位置。

### <a name="part-4---create-api-policy-keys"></a>第4部分-建立 API 原則金鑰

請參閱這[份檔](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys)，並建立兩個原則金鑰-一個用於 api 使用者名稱，另一個用於您在上方定義的 api 密碼。

範例原則會使用下列索引鍵名稱：

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>第5部分-更新 API URL

在提供的 TrustFrameworkExtensions 原則中，尋找名為的技術設定檔 `Rest-LexisNexus-SessionQuery` ，並 `ServiceUrl` 使用上述部署的 API 位置來更新中繼資料專案。

### <a name="part-6---update-ui-url"></a>第6部分-更新 UI URL

在提供的 TrustFrameworkExtensions 原則中，執行 [尋找和取代]，以搜尋 https://yourblobstorage/blobcontainer/ UI 檔案部署所在的位置。

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>第7部分-設定 Azure AD B2C 原則

請參閱這[份檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack)以下載[本機帳戶入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)，並設定 Azure AD B2C 租使用者的原則。

>[!NOTE]
>更新提供的原則，使其與您的特定租使用者相關。

>[!NOTE]
> 我們建議的最佳作法是在 [屬性集合] 頁面中新增同意通知給客戶。 通知使用者資訊將會傳送至協力廠商服務以進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 [Azure AD B2C 租使用者]，然後在 [原則] 下選取 [**使用者流程**]。

2. 選取您先前建立的**使用者流程**。

3. 選取 [**執行使用者流程**]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式（範例是 JWT）

   b. **回復 url**：選取 [重新**導向 url** ]

   c. 選取 [執行使用者流程]。

4. 完成註冊流程並建立帳戶

5. 登出

6. 完成登入流程  

7. 當您進入 [**繼續**] 之後，ThreatMetrix 謎題會隨即出現。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [開始使用 Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
