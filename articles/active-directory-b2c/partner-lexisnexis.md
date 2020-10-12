---
title: 使用 LexisNexis 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 LexisNexis，這是一種分析和身分識別驗證服務，可用來驗證使用者的身分識別，並根據使用者的裝置提供全面的風險評估。
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c753e9a18f9869e1bf11aa437fb60484f2553e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259249"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 LexisNexis 的教學課程

在此範例教學課程中，我們會提供有關如何將 Azure AD B2C 與 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)整合的指引。 LexisNexis 提供各種不同的解決方案，您可以在 [這裡](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)找到它們。 在此範例教學課程中，我們將討論來自 LexisNexis 的 **ThreatMetrix** 解決方案。 ThreatMetrix 是一種分析和身分識別驗證服務。 它是用來驗證使用者識別，並根據使用者的裝置提供完整的風險評估。

這項整合會根據使用者在註冊流程期間提供的幾個使用者資訊來進行分析。 ThreatMetrix 會決定是否允許使用者繼續登入。 下列屬性會在 ThreatMetrix 的風險分析中考慮：

- 電子郵件
- 電話號碼
- 從使用者的電腦收集到的程式碼剖析資訊

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

## <a name="scenario-description"></a>案例描述

ThreatMetrix 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- ThreatMetrix – ThreatMetrix 服務會接受使用者提供的輸入，並將其與從使用者電腦收集到的分析資訊結合，以驗證使用者互動的安全性。

- 自訂 Rest API-此 API 會在 Azure AD B2C 與 ThreatMetrix 服務之間進行整合。

下列架構圖顯示執行。

![lexisnexis 的螢幕擷取畫面-架構-圖表](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|步驟 | 描述 |
|:--------------|:-------------|
|1. | 使用者抵達登入頁面。 使用者選取註冊來建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。
| 3. | 仲介層 API 會收集使用者屬性，並將其轉換為 LexisNexis API 可能取用的格式。 然後，將它傳送至 LexisNexis。  
| 4. | LexisNexis 會取用此資訊並加以處理，以根據風險分析來驗證使用者識別。 然後，它會將結果傳回至中介層 API。
| 5. | 仲介層 API 會處理資訊，並將相關資訊傳送回 Azure AD B2C。
| 6. | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功的回應，就會驗證使用者並授與存取權。

## <a name="onboard-with-lexisnexis"></a>使用 LexisNexis 上線

1. 若要建立 LexisNexis 帳戶，請聯絡 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. 建立符合您需求的 LexisNexis 原則。 使用 [這裡](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)提供的檔。

>[!NOTE]
> 稍後將會使用原則的名稱。

建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>使用 LexisNexis 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) 部署至 Azure 服務。 您可以依照下列 [指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。

>[!NOTE]
>您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

### <a name="part-2---configure-the-api"></a>第2部分-設定 API

您可以在 [Azure 中的 App service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)應用程式設定。  使用這個方法，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須提供下列設定給 Rest API：

| 應用程式設定 | 來源 | 注意 |
| :-------- | :------------| :-----------|
|ThreatMetrix： Url | ThreatMetrix 帳戶設定 |     |
|ThreatMetrix： OrgId | ThreatMetrix 帳戶設定 |     |
|ThreatMetrix： ApiKey |ThreatMetrix 帳戶設定|  |
|ThreatMetrix：原則 | 在 ThreatMetrix 中建立的原則名稱 | |
| BasicAuth:ApiUsername |定義 API 的使用者名稱| Azure AD B2C 設定中會使用使用者名稱
| BasicAuth:ApiPassword | 定義 API 的密碼 | 密碼將用於 Azure AD B2C 設定

### <a name="part-3---deploy-the-ui"></a>第3部分-部署 UI

此解決方案會使用 Azure AD B2C 載入的自訂 UI 範本。 這些 UI 範本會進行直接傳送至 ThreatMetrix 服務的分析。

請參閱這些 [指示](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough) ，將內含的 [UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) 檔案部署至 blob 儲存體帳戶。 這些指示包括設定 blob 儲存體帳戶、設定 CORS，以及啟用公用存取。

UI 是以 [海洋藍範本](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)為基礎。 UI 內的所有連結都應更新為參考部署的位置。 在 [UI] 資料夾中，尋找並取代 https://yourblobstorage/blobcontainer 為部署的位置。

### <a name="part-4---create-api-policy-keys"></a>第4部分-建立 API 原則金鑰

請參閱這 [份檔](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) ，並建立兩個原則金鑰（一個用於 api 使用者名稱），一個用於您在上面定義的 api 密碼。

範例原則會使用這些金鑰名稱：

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>第5部分-更新 API URL

在提供的 [TrustFrameworkExtensions 原則](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)中，尋找名為的技術設定檔 `Rest-LexisNexus-SessionQuery` ，並 `ServiceUrl` 使用上述部署的 API 位置來更新中繼資料專案。

### <a name="part-6---update-ui-url"></a>第6部分-更新 UI URL

在提供的 [TrustFrameworkExtensions 原則](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)中，使用 [尋找和取代] 來搜尋 UI 檔案的 https://yourblobstorage/blobcontainer/ 部署位置。

>[!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面中，將同意通知新增至客戶。 通知使用者資訊將傳送至協力廠商服務進行身分識別驗證。

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>第7部分-設定 Azure AD B2C 原則

請參閱本 [檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 以下載 [本機帳戶入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) ，並設定 Azure AD B2C 租使用者的 [原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) 。

>[!NOTE]
>更新提供的原則，以與您特定的租使用者相關聯。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [ **使用者流程**]。

2. 選取您先前建立的 **使用者流程**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例為 JWT) 

   b. **回復 url**：選取重新 **導向 url**

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 登出

6. 登入流程  

7. 當您進入 [ **繼續**] 之後，ThreatMetrix 拼圖將會出現。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
