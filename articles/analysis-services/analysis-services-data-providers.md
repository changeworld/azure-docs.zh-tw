---
title: Azure Analysis Services 用戶端程式庫 |Microsoft Docs
description: 說明用戶端應用程式和工具連接到 Azure Analysis Services 所需的用戶端程式庫
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f099546ced64bd911916164879cd533329761c66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914665"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>用來連接到 Analysis Services 的用戶端程式庫

用戶端程式庫是用戶端應用程式和工具連接到 Analysis Services 伺服器的必備條件。 適用于 Visual Studio 的 Microsoft 用戶端應用程式（如 Power BI Desktop、Excel、SQL Server Management Studio （SSMS）和 Analysis Services 專案延伸模組）會安裝這三個用戶端程式庫，並與一般應用程式更新一起更新。 在某些情況下，您可能需要安裝較新版本的用戶端程式庫。 自訂用戶端應用程式也需要安裝用戶端程式庫。

## <a name="download-the-latest-client-libraries-windows-installer"></a>下載最新的用戶端程式庫 (Windows Installer)  

|下載  |產品版本  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.21.27    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.21.27       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.0.5    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.0.5     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO 和 ADOMD (NuGet 套件)

Analysis Services 管理物件（AMO）和 ADOMD 用戶端程式庫可從[NuGet.org](https://www.nuget.org/)取得安裝的套件。建議您遷移至 NuGet 參考，而不是使用 Windows Installer。 

|Package  | 產品版本  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.0.5     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.0.5      |

Nuget 套件組件 AssemblyVersion 會遵循語意化版本控制系統：MAJOR.MINOR.PATCH。 NuGet 參考會載入預期的版本，即使是 GAC (產生自 MSI 安裝) 中的不同版本也一樣。 PATCH 會針對每個版本遞增。 AMO 和 ADOMD 版本會保持同步。

## <a name="understanding-client-libraries"></a>了解用戶端程式庫

Analysis Services 會利用三個用戶端程式庫，也稱為資料提供者。 ADOMD.NET 和「Analysis Services 管理物件」(AMO) 是受控用戶端程式庫。 Analysis Services OLE DB Provider (MSOLAP DLL) 是原生用戶端程式庫。 通常會同時安裝這所有三個程式庫。 **Azure Analysis Services 需要這三個程式庫的最新版本**。 

Microsoft 用戶端應用程式 (像是 Power BI Desktop 和 Excel) 會安裝這三個用戶端程式庫，並在新版本可用時加以更新。 部分用戶端程式庫可能不是 Azure Analysis Services 所需的最新版本，需依更新的版本或頻率而定。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式需要手動或以程式設計方式安裝程式庫。 手動安裝的用戶端程式庫會以可散發的套件形式包含在 SQL Server 功能套件中。 不過，這些會用戶端程式庫與 SQL Server 版本繫結，而可能不是最新版本。  

用於用戶端連線的用戶端程式庫，與從 Azure Analysis Services 伺服器連接到資料來源所需的資料提供者不同。 若要深入瞭解資料來源連接，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="client-library-types"></a>用戶端程式庫類型

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB 提供者 (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) 是 Analysis Services 資料庫連線的原生用戶端程式庫。 ADOMD.NET 和 AMO 會間接使用，將連線要求委派給資料提供者。 您也可以直接從應用程式程式碼呼叫 OLE DB Provider。  
  
 大部分用來存取 Analysis Services 資料庫的工具和用戶端應用程式會自動安裝 Analysis Services OLE DB Provider。 它必須安裝在用來存取 Analysis Services 資料的電腦上。  
  
 通常會在連接字串中指定 OLE DB Provider。 Analysis Services 連接字串會使用不同的命名法來表示 OLE DB Provider：MSOLAP.\<version>.dll。

### <a name="amo"></a>AMO  

 AMO 是用於伺服器管理及資料定義的受控用戶端程式庫。 工具和用戶端應用程式會加以安裝並使用。 例如，SQL Server Management Studio (SSMS) 會使用 AMO 來連線到 Analysis Services。 使用 AMO 的連線通常是最簡短的，由 `"data source=\<servername>"` 所組成。 建立連線之後，您可以使用 API 來處理資料庫集合與主要物件。 Visual Studio 和 SSMS 都會使用 AMO 來連接到 Analysis Services 實例。  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET 是用於查詢 Analysis Services 資料的受控資料用戶端程式庫。 工具和用戶端應用程式會加以安裝並使用。 
  
 連線到資料庫時，所有三個程式庫的連接字串屬性都會類似。 幾乎任何使用 [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) 針對 ADOMD.NET 定義的連接字串也都適用於 AMO 和 Analysis Services OLE DB Provider (MSOLAP)。 若要進一步了解，請參閱[連接字串屬性與 &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services)。  

  
##  <a name="bkmk_LibUpdate"></a> 如何判斷用戶端程式庫版本   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  移至 `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`。 如果您有多個資料夾，請選擇較高的數字。
  
2.  以滑鼠右鍵按一下 [msolap.dll] > [屬性] > [詳細資料]。 如果檔名為 msolap140.dll，就會比最新版本還舊，而應進行升級。
    
    ![用戶端程式庫詳細資料](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. 移至 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`。 如果您有多個資料夾，請選擇較高的數字。
2. 以滑鼠右鍵按一下 [Microsoft.AnalysisServices] > [屬性] > [詳細資料]。  

### <a name="adomd"></a>ADOMD

1. 移至 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`。 如果您有多個資料夾，請選擇較高的數字。
2. 以滑鼠右鍵按一下 [Microsoft.AnalysisServices.AdomdClient] > [屬性] > [詳細資料]。  


## <a name="next-steps"></a>後續步驟
[使用 Excel 進行連接](analysis-services-connect-excel.md)    
[與 Power BI 連線](analysis-services-connect-pbi.md)
