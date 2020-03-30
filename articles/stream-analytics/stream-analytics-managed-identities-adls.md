---
title: 將 Azure 流分析驗證為 Azure 資料存儲第 1 代
description: 本文說明如何使用受控識別向 Azure Data Lake Storage Gen1 輸出驗證 Azure 串流分析作業。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254374"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>使用託管標識對 Azure 資料存儲第 1 代進行身份驗證流分析

Azure 串流分析支援向 Azure Data Lake Storage (ADLS) Gen1 輸出進行受控識別驗證。 身分識別是在 Azure Active Directory 中註冊的受控應用程式，代表了指定的串流分析作業，並可用來向指定的資源驗證。 受控識別消除了以使用者為基礎的驗證方法限制，比如因為密碼改變或使用者權杖每 90 天到期一次而需要重新驗證。 此外，受控識別有助於進行輸出至 Azure Data Lake Storage Gen1 的串流分析作業部署自動化。

本文說明的三種方式可讓您透過 Azure 入口網站、Azure Resource Manager 範本部署和適用於 Visual Studio 的 Azure 串流分析工具，為輸出至 Azure Data Lake Storage Gen1 的 Azure 串流分析作業啟用受控識別。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure 入口網站

1. 一開始先建立新的串流分析作業，或在 Azure 入口網站中開啟現有作業。 從位於螢幕左側的功能表列中，選擇位於 **"配置**"下的**託管標識**。

   ![配置流分析託管標識](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 從右側顯示的視窗選擇 **"使用系統分配的託管標識**"。 按一下 **"保存到**服務主體"以查看 Azure 活動目錄中流分析作業的標識。 Azure 會負責管理新建立的身分識別生命週期。 當串流分析作業刪除時，Azure 會自動刪除已與其建立關聯的身分識別 (亦即服務主體)。

   當組態儲存時，服務主體的物件識別碼 (OID) 會列為主體識別碼，如下所示：

   ![串流分析服務主體識別碼](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   服務主體與串流分析作業的名稱相同。 例如，如果您作業的名稱是 **MyASAJob**，建立的服務主體名稱也會是 **MyASAJob**。

3. 在 ADLS Gen1 輸出接收器的輸出屬性視窗中，按一下身份驗證模式下拉，然後選擇 [託管標識]。

4. 填寫其餘屬性。 若要深入了解如何建立 ADLS 輸出，請參閱[使用串流分析建立 Data Lake Store 輸出](../data-lake-store/data-lake-store-stream-analytics.md)。 當您完成後，請按一下 [儲存]****。

   ![設定 Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. 瀏覽到 ADLS Gen1 的 [概觀] 頁面，然後按一下 [資料總管]****。

   ![設定 Data Lake Storage 概觀](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. 在 [資料總管] 窗格中選取 [存取]****，然後在 [存取] 窗格中按一下 [新增]****。

   ![設定 Data Lake Store 存取](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. 在 [選取使用者或群組]**** 窗格的文字方塊中，鍵入服務主體的名稱。 請記住，服務主體的名稱也是對應串流分析作業的名稱。 當您開始鍵入主體名稱時，名稱就會顯示在文字方塊的下方。 選擇您想要的服務主體名稱，然後按一下 [選取]****。

   ![選取服務主體名稱](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. 在 [權限]**** 窗格中，選取 [寫入]**** 和 [執行]**** 權限，並將其指派至 [此資料夾及所有子系]****。 然後按一下 [確定]****。

   ![選取寫入和執行權限](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 服務主體會列在 [存取]**** 窗格 [指派的權限]**** 底下，如下所示。 您現在可以返回並啟動您的串流分析作業。

   ![入口網站中的串流分析存取清單](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   若要深入了解 Data Lake Storage Gen1 檔案系統權限，請參閱 [Azure Data Lake Storage Gen1 的存取控制](../data-lake-store/data-lake-store-access-control.md)。

## <a name="stream-analytics-tools-for-visual-studio"></a>適用於 Visual Studio 的串流分析工具

1. 在 JobConfig.json 中，將 [使用系統指派的身分識別]**** 設定為 [True]****。

   ![串流分析作業組態受控識別](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. 在 ADLS Gen1 輸出接收器的輸出屬性視窗中，按一下身份驗證模式下拉，然後選擇 [託管標識]。

   ![ADLS 輸出受控識別](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. 填寫其餘的屬性，然後按一下 [儲存]****。

4. 按一下查詢編輯器中的 [提交至 Azure]****。

   當您提交作業時，工具可做兩件事：

   * 在 Azure Active Directory 中自動建立串流分析作業的身分識別服務主體。 Azure 會負責管理新建立的身分識別生命週期。 當串流分析作業刪除時，Azure 會自動刪除已與其建立關聯的身分識別 (亦即服務主體)。

   * 為作業中使用的 ADLS Gen1 前置詞路徑自動設定 [寫入]**** 和 [執行]**** 權限，並將其指派至此資料夾及所有子系。

5. 您可以使用[串流分析 CI.CD Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 版或更新版本，在組建電腦 (Visual Studio 外部) 上產生具有下列屬性的 Resource Manager 範本。 請依照下一節中的 Resource Manager 範本部署步驟來取得服務主體，並透過 PowerShell 授與服務主體的存取權。

## <a name="resource-manager-template-deployment"></a>Resource Manager 範本部署

1. 您現在可以建立具有受控識別的 *Microsoft.StreamAnalytics/streamingjobs* 資源，方法是在 Resource Manager 範本的資源區段中加入下列屬性：

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   這個屬性會要求 Azure Resource Manager 建立及管理您的 Azure 串流分析作業身分識別。

   **範例作業**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **範例作業回應**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   請記下作業回應中的主體識別碼，以授予必要 ADLS 資源的存取權。

   **租用戶識別碼**是 Azure Active Directory 租用戶的識別碼，而該租用戶是建立服務主體的位置。 服務主體會建立在訂用帳戶信任的 Azure 租用戶中。

   **類型**表示受控識別的類型，如受控識別類型中所述。 僅支援系統指派的類型。

2. 使用 PowerShell 提供服務主體的存取。 若要經由 PowerShell 提供服務主體的存取權，請執行下列命令：

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** 是服務主體的物件識別碼，並會在服務主體建立時，列於入口網站畫面上。 如果您使用了 Resource Manager 範本部署來建立作業，物件識別碼會列在作業回應的識別屬性中。

   **範例**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   要瞭解有關上述 PowerShell 命令的詳細資訊，請參閱[集-AzDataLakeStoreItemAclentry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)文檔。

## <a name="limitations"></a>限制
此功能不支援以下內容：

1. **多租戶訪問**：為給定的流分析作業創建的服務主體將駐留在創建作業的 Azure 活動目錄租戶上，並且不能針對駐留在其他 Azure 活動目錄租戶上的資源使用。 因此，您只能在與 Azure 流分析作業位於同一 Azure 活動目錄租戶中的 ADLS 第 1 代資源上使用 MSI。 

2. **[不支援使用者分配的身份](../active-directory/managed-identities-azure-resources/overview.md)**： 這意味著使用者無法輸入自己的服務主體，以便其流分析作業使用。 服務主體由 Azure 流分析生成。

## <a name="next-steps"></a>後續步驟

* [使用串流分析建立 Data Lake Store 輸出](../data-lake-store/data-lake-store-stream-analytics.md)
* [使用 Visual Studio 在本機測試串流分析查詢](stream-analytics-vs-tools-local-run.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md) 
