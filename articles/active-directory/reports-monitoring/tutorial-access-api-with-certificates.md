---
title: AD 報告 API 搭配憑證的教學課程 | Microsoft Docs
description: 本教學課程說明如何使用 Azure AD 報告 API 配合憑證認證來取得目錄中的資料，而不需使用者介入。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c3443cb73e85fc69349e7293597a5f4a723959d3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130046"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>教學課程：使用 Azure Active Directory 報告 API 搭配憑證來取得資料

[Azure Active Directory (Azure AD) 報告 API](concept-reporting-api.md) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。 如果您想在沒有使用者介入的情況下存取 Azure AD 報告 API，請務必設定您的存取權以使用憑證。

在本教學課程中，您會了解如何使用測試憑證來存取 MS 圖形 API 以進行報告。 我們不建議在生產環境中使用測試憑證。 

## <a name="prerequisites"></a>必要條件

1. 若要存取登入資料，請確定您有進階 (P1/P2) 授權的 Azure Active Directory 租用戶。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 請注意，如果您在升級前沒有任何活動資料，則在升級至進階授權之後，報告需要幾天的時間才會顯示出資料。 

2. 建立或切換到租用戶角色為 **全域系統管理員**、**安全性系統管理員**、**安全性讀取者** 或 **報告讀取者** 的使用者帳戶。 

3. 完成[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)。 

4. 下載並安裝 [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)。

5. 安裝 [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/)。 此模組會提供數個公用程式 Cmdlet，包括︰
    - 驗證所需的 ADAL 程式庫
    - 從使用者、應用程式金鑰和憑證存取權杖 (使用 ADAL)
    - 處理分頁結果的圖形 API

6. 如果您第一次使用模組執行 **Install-MSCloudIdUtilsModule**，則改用 **Import-Module** PowerShell 命令將其匯入。 您的工作階段看起來應該類似此畫面：![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. 使用 **New-SelfSignedCertificate** PowerShell commandlet 建立測試憑證。

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. 使用 **Export-Certificate** commandlet 將其匯出至憑證檔案。

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用 Azure Active Directory 報告 API 搭配憑證來取得資料

1. 導覽至 [Azure 入口網站](https://portal.azure.com)，選取 [Azure Active Directory]，接著選取 [應用程式註冊]，然後從清單中選擇您的應用程式。 

2. 在 [應用程式註冊] 刀鋒視窗的 **管理** 區段下，選取 [憑證與祕密]，然後選取 [上傳憑證]。

3. 從上一個步驟中選取憑證檔案，然後選取 [新增]。 

4. 請記下應用程式識別碼，以及您剛剛向應用程式註冊的憑證指紋。 若要尋找指紋，請從入口網站的應用程式頁面中移至 **管理** 下的 **憑證和祕密**。 指紋會出現在 **憑證** 清單下方。

5. 在內嵌資訊清單編輯器中開啟應用程式資訊清單，然後驗證 *keyCredentials* 屬性是否已使用下列新的憑證資訊更新 - 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. 現在，您可以使用此憑證來取得 MS 圖形 API 的存取權杖。 使用 MSCloudIdUtils PowerShell 模組中的 **Get-MSCloudIdMSGraphAccessTokenFromCert** Cmdlet，然後傳入您從上一個步驟中所取得的應用程式識別碼和指紋。 

   ![此螢幕擷取畫面顯示 PowerShell 視窗，其中包含可建立存取權杖的命令。](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. 使用 PowerShell 指令碼中的存取權杖來查詢圖形 API。 使用 MSCloudIDUtils 中的 **Invoke-MSCloudIdMSGraphQuery** 指令程式來列舉 signins 和 directoryAudits 端點。 此指令程式可處理多個分頁結果，並將這些結果傳送至 PowerShell 管道。

8. 查詢 directoryAudits 端點以擷取稽核記錄。 

   ![此螢幕擷取畫面顯示 PowerShell 視窗，其中包含命令，可使用此程序中先前的存取權杖來查詢 directoryAudits 端點。](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. 查詢 signins 端點以擷取登入記錄。

    ![此螢幕擷取畫面顯示 PowerShell 視窗，其中包含命令，可使用此程序中先前的存取權杖來查詢 signins 端點。](./media/tutorial-access-api-with-certificates/query-signins.png)

10. 您現在可以選擇將此資料匯出為 CSV，並儲存至 SIEM 系統。 您也可以在排定的工作中包裝您的指令碼，以便定期從租用戶取得 Azure AD 資料，而不必將應用程式金鑰儲存在原始程式碼中。 

## <a name="next-steps"></a>後續步驟

* [取得報告 API 的第一印象](concept-reporting-api.md)
* [稽核 API 參考](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [登入活動報告 API 參考](/graph/api/resources/signin?view=graph-rest-beta)