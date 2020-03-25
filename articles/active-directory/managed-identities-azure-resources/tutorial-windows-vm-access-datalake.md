---
title: 教學課程`:` 使用受控識別來存取 Azure Data Lake Store - Windows - Azure AD
description: 本教學課程說明如何使用 Windows VM 系統指派的受控識別，來存取 Azure Data Lake Store。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03f78341b7521267f8aaf72d58ebd4c912949ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977870"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>教學課程：使用 Windows VM 系統指派的受控識別來存取 Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教學課程說明如何將系統指派的受控識別用於 Windows 虛擬機器 (VM)，以存取 Azure Data Lake Store。 受控服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 將您的 VM 存取權授與 Azure Data Lake Store
> * 使用 VM 身分識別取得存取權杖，並使用它存取 Azure Data Lake Store

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>啟用

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>授與存取權

現在您可以將您的 VM 存取權授與 Azure Data Lake Store 中的檔案和資料夾。  在這個步驟，您可以使用現有的 Data Lake Store 或建立新的。  若要使用 Azure 入口網站建立新的 Data Lake Store，請遵循此 [Azure Data Lake Store 快速入門](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。 在 [Azure Data Lake Store 文件](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)中也有使用 Azure CLI 和 Azure PowerShell 的快速入門作法。

在 Data Lake Store 中建立新資料夾，並將在該資料夾中讀取、寫入和執行檔案的權限，授予 VM 系統指派的受控識別：

1. 在 Azure 入口網站中，按一下左方瀏覽列中的 [Data Lake Store]  。
2. 按一下您想要使用於本教學課程中的 Data Lake Store。
3. 按一下命令列中的 [資料總管]  。
4. 會選取 Data Lake Store 的根資料夾。  按一下命令列中的 [存取]  。
5. 按一下 [新增]  。  在 [選取]  欄位中，輸入 VM 的名稱，例如 **DevTestVM**。  按一下以從搜尋結果中選取您的 VM，然後按一下 [選取]  。
6. 按一下 [選取權限]  。  選取 [讀取]  和 [執行]  ，加入到 [此資料夾]  ，然後新增為 [僅存取權限]  。  按一下 [確定]  。  權限應該已成功加入。
7. 關閉 [存取]  刀鋒視窗。
8. 針對此教學課程，建立一個新資料夾。  按一下命令列中的 [新資料夾]  ，提供新資料夾的名稱，例如 **TestFolder**。  按一下 [確定]  。
9. 按一下您建立的資料夾，然後按一下命令列中的 [存取]  。
10. 和步驟 5 相同，按一下 [新增]  ，在 [選取]  欄位中輸入您的 VM 名稱，選取它，然後按一下 [選取]  。
11. 和步驟 6 相同，按一下 [選取權限]  ，選取 [讀取]  、[寫入]  和 [執行]  ，加入到 [此資料夾]  ，並新增為 [存取權限項目及預設權限項目]  。  按一下 [確定]  。  權限應該已成功加入。

VM 系統指派的受控識別現在可於您所建立的資料夾中，對檔案執行所有作業。  如需管理 Data Lake Store 存取權的詳細資訊，請閱讀 [Azure Data Lake Store 中的存取控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)一文。

## <a name="access-data"></a>存取資料

Azure Data Lake Store 原生支援 Azure AD 驗證，因此可以直接接受使用 Azure 資源的受控識別所取得的存取權杖。  為了向 Data Lake Store 檔案系統驗證，您在授權標頭中傳送由 Azure AD 所簽發的存取權杖至 Data Lake Store 檔案系統端點，其格式如下："Bearer <ACCESS_TOKEN_VALUE>"。  若要深入了解 Data Lake Store 的 Azure AD 驗證支援，請閱讀[使用 Azure Active Directory 向 Data Lake Store 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Data Lake Store 檔案系統用戶端 SDK 尚不支援適用於 Azure 資源的受控識別。  待 SDK 新增支援後，將會更新本教學課程。

在本教學課程中，您使用 PowerShell 向 Data Lake Store 檔案系統 REST API 驗證，以提出 REST 要求。 若要使用 VM 系統指派的受控識別進行驗證，您需要從 VM 提出要求。

1. 在入口網站中，瀏覽至 [虛擬機器]  並移至您的 Windows VM，在 [概觀]  中按一下 [連線]  。
2. 輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。 
3. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。 
4. 使用 PowerShell 的 `Invoke-WebRequest`，向 Azure 資源端點的本機受控識別提出要求，以取得 Azure Data Lake Store 的存取權杖。  Data Lake Store 的資源識別碼是 `https://datalake.azure.net/`。  Data Lake 會對資源識別碼執行完全相符的比對，因此結尾的斜線很重要。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   將來自 JSON 物件的回應轉換為 PowerShell 物件。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   擷取回應中的存取權杖。
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. 使用 PowerShell 的 `Invoke-WebRequest' 對您的 Data Lake Store REST 端點提出要求，以列出根資料夾中的資料夾。  這是檢查所有項目是否正確設定的簡單方式。  授權標頭中的字串 "Bearer" 必須是大寫 "B"。  您可以在 Azure 入口網站 Data Lake Store 刀鋒視窗的 [概觀]  區段中找到您的 Data Lake Store 名稱。

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   成功的回應看起來會像這樣：

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. 現在您可以嘗試將檔案上傳至您的 Data Lake Store。  首先，建立要上傳的檔案。

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. 使用 PowerShell 的 `Invoke-WebRequest` 對您的 Data Lake Store REST 端點提出要求，以將檔案上傳到您先前建立的資料夾。  此要求分為兩個步驟。  在第一個步驟中，您提出要求，並重新導向至應上傳檔案的位置。  第二個步驟中，您實際上傳檔案。  如果您使用的資料夾和檔案名稱與本教學課程中的不同，請記得適當地設定這些值。 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   如果您檢查 `$HdfsRedirectResponse` 的值，它看起來應該像下列回應：

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   將要求傳送至重新導向端點以完成上傳：

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   成功的回應看起來會像這樣：

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

您還可以使用其他 Data Lake Store 檔案系統 API，以附加至檔案及下載檔案等。


## <a name="disable"></a>停用

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Windows 虛擬機器的系統指派受控識別，來存取 Azure Data Lake Store。 若要深入了解 Azure Data Lake Store，請參閱：

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
