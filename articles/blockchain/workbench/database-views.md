---
title: Azure Blockchain Workbench 資料庫檢視
description: 預覽 SQL DB 資料庫檢視的可用 Azure Blockchain Workbench 總覽。
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e715bd2297c1e051ae92b15d73c945c9e80c3d94
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003316"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure Blockchain Workbench 資料庫檢視

Azure Blockchain Workbench Preview 會將分散式總帳中的資料傳遞至*連鎖*的 SQL DB 資料庫。 離鏈資料庫可讓您使用 SQL 和現有的工具（例如[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)）與區塊鏈資料互動。

Azure Blockchain Workbench 提供一組資料庫檢視，可讓您存取在執行查詢時將有所幫助的資料。 這些檢視經過大幅的反正規化，以便於快速著手建置報告、分析，以及將區塊鏈資料用於現有的工具，而不需要重新培訓資料庫工作人員。

本節將概略說明資料庫檢視及其包含的資料。

> [!NOTE]
> 在這些檢視外部直接使用位於資料庫中的資料庫資料表雖然是可行的，但不受支援。
>

## <a name="vwapplication"></a>vwApplication

此檢視能針對已上傳至 Azure Blockchain Workbench 的**應用程式**來提供詳細資料。

| 名稱                             | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                  | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDescription           | nvarchar(255) | 是         | 應用程式的說明 |
| ApplicationDisplayName           | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled               | bit           | 否          | 識別應用程式目前是否啟用<br /> **注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。 |
| UploadedDtTm                     | datetime2(7)  | 否          | 合約上傳的日期和時間 |
| UploadedByUserId                 | int           | 否          | 應用程式上傳者的使用者識別碼 |
| UploadedByUserExternalId         | nvarchar(255) | 否          | 應用程式上傳者的使用者外部識別碼。 根據預設，此識別碼是聯盟的 Azure Active Directory 使用者。                                                                                                |
| UploadedByUserProvisioningStatus | int           | 否          | 識別使用者佈建程序的目前狀態。 可能的值包括： <br />0 – API 已建立使用者<br />1 – 已在資料庫中建立與使用者相關聯的金鑰<br />2 – 已完整佈建使用者                         |
| UploadedByUserFirstName          | nvarchar(50)  | 是         | 合約上傳者的使用者名字 |
| UploadedByUserLastName           | nvarchar(50)  | 是         | 合約上傳者的使用者姓氏 |
| UploadedByUserEmailAddress       | nvarchar(255) | 是         | 合約上傳者的使用者電子郵件地址 |

## <a name="vwapplicationrole"></a>vwApplicationRole

此檢視能為 Azure Blockchain Workbench 應用程式中已定義的角色提供詳細資料。

例如，在 [資產傳輸]** 應用程式中，可定義*買方*和*賣方*等角色。

| 名稱                   | 類型             | 可為 Null | 描述                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | 否          | 應用程式的唯一識別碼           |
| ApplicationName        | nvarchar(50)     | 否          | 應用程式的名稱                       |
| ApplicationDescription | nvarchar(255)    | 是         | 應用程式的說明                  |
| ApplicationDisplayName | nvarchar(255)    | 否          | 要在使用者介面中顯示的名稱      |
| RoleId                 | int              | 否          | 應用程式中的角色的唯一識別碼 |
| RoleName               | nvarchar50)      | 否          | 角色的名稱                              |
| RoleDescription        | description(255) | 是         | 角色的說明                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

此檢視提供已在 Azure Blockchain Workbench 應用程式中定義的角色及其相關使用者的詳細資料。

例如，在 [資產傳輸]** 應用程式中，*John Smith* 可以與*買方*角色產生關聯。

| 名稱                       | 類型          | 可為 Null | 描述                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | 否          | 應用程式的唯一識別碼                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | 否          | 應用程式的名稱                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | 是         | 應用程式的說明                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱                                                                                                                                                                                          |
| ApplicationRoleId          | int           | 否          | 應用程式中的角色的唯一識別碼                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | 否          | 角色的名稱                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | 是         | 角色的說明                                                                                                                                                                                                             |
| UserId                     | int           | 否          | 與角色相關聯的使用者識別碼 |
| UserExternalId             | nvarchar(255) | 否          | 與角色相關聯之使用者的外部識別碼。 根據預設，此識別碼是聯盟的 Azure Active Directory 使用者。                                                                     |
| UserProvisioningStatus     | int           | 否          | 識別使用者佈建程序的目前狀態。 可能的值包括： <br />0 – API 已建立使用者<br />1 – 已在資料庫中建立與使用者相關聯的金鑰<br />2 – 已完整佈建使用者 |
| UserFirstName              | nvarchar(50)  | 是         | 與角色相關聯之使用者名字 |
| UserLastName               | nvarchar(255) | 是         | 與角色相關聯之使用者的姓氏 |
| UserEmailAddress           | nvarchar(255) | 是         | 與角色相關聯之使用者的電子郵件地址 |

## <a name="vwconnectionuser"></a>vwConnectionUser

此檢視提供已在 Azure Blockchain Workbench 中定義的連線及其相關使用者的詳細資料。 對於每個連線，此檢視會包含下列資料：

-   相關聯的總帳詳細資料
-   相關聯的使用者資訊

| 名稱                     | 類型          | 可為 Null | 描述                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | 否          | Azure Blockchain Workbench 中的連線的唯一識別碼 |
| ConnectionEndpointUrl    | nvarchar(50)  | 否          | 連線的端點 URL |
| ConnectionFundingAccount | nvarchar(255) | 是         | 與連線相關聯的資金帳戶 (如果適用) |
| LedgerId                 | int           | 否          | 總帳的唯一識別碼 |
| LedgerName               | nvarchar(50)  | 否          | 總帳的名稱 |
| LedgerDisplayName        | nvarchar(255) | 否          | 要在 UI 中顯示的總帳名稱 |
| UserId                   | int           | 否          | 與連線相關聯的使用者識別碼 |
| UserExternalId           | nvarchar(255) | 否          | 與連線相關聯之使用者的外部識別碼。 根據預設，此識別碼是聯盟的 Azure Active Directory 使用者。 |
| UserProvisioningStatus   | int           | 否          |識別使用者佈建程序的目前狀態。 可能的值包括： <br />0 – API 已建立使用者<br />1 – 已在資料庫中建立與使用者相關聯的金鑰<br />2 – 已完整佈建使用者 |
| UserFirstName            | nvarchar(50)  | 是         | 與連線相關聯之使用者的名字 |
| UserLastName             | nvarchar(255) | 是         | 與連線相關聯之使用者的姓氏 |
| UserEmailAddress         | nvarchar(255) | 是         | 與連線相關聯之使用者的電子郵件地址 |

## <a name="vwcontract"></a>vwContract

此檢視會提供與已部署的合約有關的詳細資料。 對於每個合約，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   函式的相關總帳實作
-   動作起始者的使用者詳細資料
-   與區塊鏈的區塊和交易有關的詳細資料

| 名稱                                     | 類型           | 可為 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | 否          | Azure Blockchain Workbench 中的連線的唯一識別碼。                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | 否          | 連線的端點 URL |
| ConnectionFundingAccount                 | nvarchar(255)  | 是         | 與連線相關聯的資金帳戶 (如果適用) |
| LedgerId                                 | int            | 否          | 總帳的唯一識別碼 |
| LedgerName                               | nvarchar(50)   | 否          | 總帳的名稱 |
| LedgerDisplayName                        | nvarchar(255)  | 否          | 要在 UI 中顯示的總帳名稱 |
| ApplicationId                            | int            | 否          | 應用程式的唯一識別碼 |
| ApplicationName                          | nvarchar (50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName                   | nvarchar (255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                       | bit            | 否          | 識別應用程式目前是否啟用。<br /> **注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。  |
| WorkflowId                               | int            | 否          | 與合約相關聯的工作流程唯一識別碼 |
| WorkflowName                             | nvarchar(50)   | 否          | 與合約相關聯的工作流程名稱 |
| WorkflowDisplayName                      | nvarchar(255)  | 否          | 與合約相關聯且要在使用者介面中顯示的工作流程名稱 |
| WorkflowDescription                      | nvarchar(255)  | 是         | 與合約相關聯的工作流程說明 |
| ContractCodeId                           | int            | 否          | 與合約相關聯之合約程式碼的唯一識別碼 |
| ContractFileName                         | int            | 否          | 包含此工作流程之智慧合約程式碼的檔案名稱。 |
| ContractUploadedDtTm                     | int            | 否          | 合約程式碼上傳的日期和時間 |
| ContractId                               | int            | 否          | 合約的唯一識別碼 |
| ContractProvisioningStatus               | int            | 否          | 識別合約佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立合約<br />1 – 合約已傳送至總帳<br />2 – 合約已成功部署至總帳<br />3 或 4 – 合約無法部署至總帳<br />5 – 合約早已成功部署至總帳 <br /><br />自 1.5 版起，支援值 0 到 5。 對於目前版本中的回溯相容性，請參考僅支援值 0 到 2 的 **vwContractV0** 檢視。 |
| ContractLedgerIdentifier                 | nvarchar (255) |             | 合約部署者的使用者電子郵件地址 |
| ContractDeployedByUserId                 | int            | 否          | 合約部署者的使用者外部識別碼。 根據預設，此識別碼是代表使用者 Azure Active Directory 識別碼的 guid。                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | 否          | 合約部署者的使用者外部識別碼。 根據預設，此識別碼是代表使用者 Azure Active Directory 識別碼的 guid。                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | 否          | 識別使用者佈建程序的目前狀態。 可能的值包括： <br />0 – API 已建立使用者<br />1 – 已在資料庫中建立與使用者相關聯的金鑰 <br />2 – 已完整佈建使用者                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | 是         | 合約部署者的使用者名字 |
| ContractDeployedByUserLastName           | nvarchar(255)  | 是         | 合約部署者的使用者姓氏 |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | 是         | 合約部署者的使用者電子郵件地址 |

## <a name="vwcontractaction"></a>vwContractAction

此檢視能針對因合約而採取的動作來顯示多數的相關資訊，旨在促進常見報告案例的效率。 對於每個執行的動作，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   相關聯的智慧合約函式和參數定義
-   函式的相關總帳實作
-   為參數提供的特定執行個體值
-   動作起始者的使用者詳細資料
-   與區塊鏈的區塊和交易有關的詳細資料

| 名稱                                     | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                          | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName                   | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                       | bit           | 否          | 此欄位可識別應用程式目前是否啟用。 注意 – 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。                                                  |
| WorkflowId                               | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                             | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName                      | nvarchar(255) | 否          | 要在使用者介面中顯示的工作流程名稱 |
| WorkflowDescription                      | nvarchar(255) | 是         | 工作流程的說明 |
| ContractId                               | int           | 否          | 合約的唯一識別碼 |
| ContractProvisioningStatus               | int           | 否          | 識別合約佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立合約<br />1 – 合約已傳送至總帳<br />2 – 合約已成功部署至總帳<br />3 或 4 – 合約無法部署至總帳<br />5 – 合約早已成功部署至總帳 <br /><br />自 1.5 版起，支援值 0 到 5。 對於目前版本中的回溯相容性，請參考僅支援值 0 到 2 的 **vwContractV0** 檢視。 |
| ContractCodeId                           | int           | 否          | 合約之程式碼實作的唯一識別碼 |
| ContractLedgerIdentifier                 | nvarchar(255) | 是         | 已為特定分散式總帳部署的智慧合約版本的相關唯一識別碼。 例如 Ethereum。 |
| ContractDeployedByUserId                 | int           | 否          | 合約部署者的使用者唯一識別碼 |
| ContractDeployedByUserFirstName          | nvarchar(50)  | 是         | 合約部署者的使用者名字 |
| ContractDeployedByUserLastName           | nvarchar(255) | 是         | 合約部署者的使用者姓氏 |
| ContractDeployedByUserExternalId         | nvarchar(255) | 否          | 合約部署者的使用者外部識別碼。 根據預設，此識別碼是在聯盟 Azure Active Directory 中代表其身分識別的 guid。                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | 是         | 合約部署者的使用者電子郵件地址 |
| WorkflowFunctionId                       | int           | 否          | 工作流程函式的唯一識別碼 |
| WorkflowFunctionName                     | nvarchar(50)  | 否          | 函式的名稱 |
| WorkflowFunctionDisplayName              | nvarchar(255) | 否          | 要在使用者介面中顯示的函式名稱 |
| WorkflowFunctionDescription              | nvarchar(255) | 否          | 函式的說明 |
| ContractActionId                         | int           | 否          | 合約動作的唯一識別碼 |
| ContractActionProvisioningStatus         | int           | 否          | 識別合約動作佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立合約動作<br />1 – 合約動作已傳送至總帳<br />2 – 合約動作已成功部署至總帳<br />3 或 4 – 合約無法部署至總帳<br />5 – 合約早已成功部署至總帳 <br /><br />自 1.5 版起，支援值 0 到 5。 對於目前版本中的回溯相容性，請參考僅支援值 0 到 2 的 **vwContractV0** 檢視。 |
| ContractActionTimestamp                  | datetime(2,7) | 否          | 合約動作的時間戳記 |
| ContractActionExecutedByUserId           | int           | 否          | 執行合約動作之使用者的唯一識別碼 |
| ContractActionExecutedByUserFirstName    | int           | 是         | 合約動作執行者的使用者名字 |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | 是         | 合約動作執行者的使用者姓氏 |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | 是         | 合約動作執行者的使用者外部識別碼 根據預設，此識別碼是在聯盟 Azure Active Directory 中代表其身分識別的 guid。 |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | 是         | 合約動作執行者的使用者電子郵件地址 |
| WorkflowFunctionParameterId              | int           | 否          | 函式參數的唯一識別碼 |
| WorkflowFunctionParameterName            | nvarchar(50)  | 否          | 函式參數的名稱 |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | 否          | 要在使用者介面中顯示的函式參數名稱 |
| WorkflowFunctionParameterDataTypeId      | int           | 否          | 與工作流程函式參數相關聯之資料類型的唯一識別碼 |
| WorkflowParameterDataTypeName            | nvarchar(50)  | 否          | 與工作流程函式參數相關聯之資料類型的名稱 |
| ContractActionParameterValue             | nvarchar(255) | 否          | 儲存在智慧合約中的參數值 |
| BlockHash                                | nvarchar(255) | 是         | 區塊的雜湊 |
| BlockNumber                              | int           | 是         | 總帳上的區塊數目 |
| BlockTimestamp                           | datetime(2,7) | 是         | 區塊的時間戳記 |
| TransactionId                            | int           | 否          | 交易的唯一識別碼 |
| TransactionFrom                          | nvarchar(255) | 是         | 產生交易的合作對象 |
| TransactionTo                            | nvarchar(255) | 是         | 作為另一交易方的合作對象 |
| TransactionHash                          | nvarchar(255) | 是         | 交易的雜湊 |
| TransactionIsWorkbenchTransaction        | bit           | 是         | 識別交易是否為 Azure Blockchain Workbench 交易的位 |
| TransactionProvisioningStatus            | int           | 是         | 識別交易佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立交易<br />1 – 交易已傳送至總帳<br />2 – 交易已成功部署至總帳                 |
| TransactionValue                         | decimal(32,2) | 是         | 交易的值 |

## <a name="vwcontractproperty"></a>vwContractProperty

此檢視能針對與合約有所關聯的屬性來顯示多數的相關資訊，旨在促進常見報告案例的效率。 對於每個採用的屬性，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   工作流程部署者的使用者詳細資料
-   相關聯的智慧合約屬性定義
-   屬性的特定執行個體值
-   合約之狀態屬性的詳細資料

| 名稱                               | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                    | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName             | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                 | bit           | 否          | 識別應用程式目前是否啟用。<br />**注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。                      |
| WorkflowId                         | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                       | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName                | nvarchar(255) | 否          | 要在使用者介面中顯示的工作流程名稱 |
| WorkflowDescription                | nvarchar(255) | 是         | 工作流程的說明 |
| ContractId                         | int           | 否          | 合約的唯一識別碼 |
| ContractProvisioningStatus         | int           | 否          | 識別合約佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立合約<br />1 – 合約已傳送至總帳<br />2 – 合約已成功部署至總帳<br />3 或 4 – 合約無法部署至總帳<br />5 – 合約早已成功部署至總帳 <br /><br />自 1.5 版起，支援值 0 到 5。 對於目前版本中的回溯相容性，請參考僅支援值 0 到 2 的 **vwContractPropertyV0** 檢視。 |
| ContractCodeId                     | int           | 否          | 合約之程式碼實作的唯一識別碼 |
| ContractLedgerIdentifier           | nvarchar(255) | 是         | 已為特定分散式總帳部署的智慧合約版本的相關唯一識別碼。 例如 Ethereum。 |
| ContractDeployedByUserId           | int           | 否          | 合約部署者的使用者唯一識別碼 |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 是         | 合約部署者的使用者名字 |
| ContractDeployedByUserLastName     | nvarchar(255) | 是         | 合約部署者的使用者姓氏 |
| ContractDeployedByUserExternalId   | nvarchar(255) | 否          | 合約部署者的使用者外部識別碼。 根據預設，此識別碼是在聯盟中代表其身分識別的 guid Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 是         | 合約部署者的使用者電子郵件地址 |
| WorkflowPropertyId                 | int           |             | 工作流程屬性的唯一識別碼 |
| WorkflowPropertyDataTypeId         | int           | 否          | 屬性之資料類型的識別碼 |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | 否          | 屬性之資料類型的名稱 |
| WorkflowPropertyName               | nvarchar(50)  | 否          | 工作流程屬性的名稱 |
| WorkflowPropertyDisplayName        | nvarchar(255) | 否          | 工作流程屬性的顯示名稱 |
| WorkflowPropertyDescription        | nvarchar(255) | 是         | 屬性的說明 |
| ContractPropertyValue              | nvarchar(255) | 否          | 合約上的屬性值 |
| StateName                          | nvarchar(50)  | 是         | 如果此屬性包含合約的狀態，則為狀態的顯示名稱。 如果與狀態沒有關聯，則值將為 Null。 |
| StateDisplayName                   | nvarchar(255) | 否          | 如果此屬性包含狀態，則為狀態的顯示名稱。 如果與狀態沒有關聯，則值將為 Null。 |
| StateValue                         | nvarchar(255) | 是         | 如果此屬性包含狀態，則為狀態值。 如果與狀態沒有關聯，則值將為 Null。 |

## <a name="vwcontractstate"></a>vwContractState

此檢視能顯示與特定合約狀態相關的多數資訊，旨在促進常見報告案例的效率。 此檢視中的各項記錄會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   工作流程部署者的使用者詳細資料
-   相關聯的智慧合約屬性定義
-   合約之狀態屬性的詳細資料

| 名稱                               | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                    | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName             | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                 | bit           | 否          | 識別應用程式目前是否啟用。<br />**注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。 |
| WorkflowId                         | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                       | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName                | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| WorkflowDescription                | nvarchar(255) | 是         | 工作流程的說明 |
| ContractLedgerImplementationId     | nvarchar(255) | 是         | 已為特定分散式總帳部署的智慧合約版本的相關唯一識別碼。 例如 Ethereum。 |
| ContractId                         | int           | 否          | 合約的唯一識別碼 |
| ContractProvisioningStatus         | int           | 否          |識別合約佈建程序的目前狀態。 可能的值包括： <br />0 – API 已在資料庫中建立合約<br />1 – 合約已傳送至總帳<br />2 – 合約已成功部署至總帳<br />3 或 4 – 合約無法部署至總帳<br />5 – 合約早已成功部署至總帳 <br /><br />自 1.5 版起，支援值 0 到 5。 對於目前版本中的回溯相容性，請參考僅支援值 0 到 2 的 **vwContractStateV0** 檢視。 |
| ConnectionId                       | int           | 否          | 工作流程部署到的區塊鏈執行個體的唯一識別碼 |
| ContractCodeId                     | int           | 否          | 合約之程式碼實作的唯一識別碼 |
| ContractDeployedByUserId           | int           | 否          | 合約部署者的使用者唯一識別碼 |
| ContractDeployedByUserExternalId   | nvarchar(255) | 否          | 合約部署者的使用者外部識別碼。 根據預設，此識別碼是在聯盟 Azure Active Directory 中代表其身分識別的 guid。 |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 是         | 合約部署者的使用者名字 |
| ContractDeployedByUserLastName     | nvarchar(255) | 是         | 合約部署者的使用者姓氏 |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 是         | 合約部署者的使用者電子郵件地址 |
| WorkflowPropertyId                 | int           | 否          | 工作流程屬性的唯一識別碼 |
| WorkflowPropertyDataTypeId         | int           | 否          | 工作流程屬性的資料類型識別碼 |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | 否          | 工作流程屬性的資料類型名稱 |
| WorkflowPropertyName               | nvarchar(50)  | 否          | 工作流程屬性的名稱 |
| WorkflowPropertyDisplayName        | nvarchar(255) | 否          | 要在 UI 中顯示之屬性的顯示名稱 |
| WorkflowPropertyDescription        | nvarchar(255) | 是         | 屬性的說明 |
| ContractPropertyValue              | nvarchar(255) | 否          | 儲存在合約中的屬性值 |
| StateName                          | nvarchar(50)  | 是         | 如果此屬性包含狀態，則為狀態的顯示名稱。 如果與狀態沒有關聯，則值將為 Null。 |
| StateDisplayName                   | nvarchar(255) | 否          | 如果此屬性包含狀態，則為狀態的顯示名稱。 如果與狀態沒有關聯，則值將為 Null。 |
| StateValue                         | nvarchar(255) | 是         | 如果此屬性包含狀態，則為狀態值。 如果與狀態沒有關聯，則值將為 Null。 |

## <a name="vwuser"></a>vwUser

此檢視能針對經佈建而可使用 Azure Blockchain Workbench 的協會成員提供其詳細資料。 根據預設，會透過使用者的初始佈建來填入資料。

| 名稱               | 類型          | 可為 Null | 描述                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | 否          | 使用者的唯一識別碼 |
| ExternalID         | nvarchar(255) | 否          | 使用者的外部識別碼。 根據預設，此識別碼是代表使用者 Azure Active Directory 識別碼的 guid。 |
| ProvisioningStatus | int           | 否          |識別使用者佈建程序的目前狀態。 可能的值包括： <br />0 – API 已建立使用者<br />1 – 已在資料庫中建立與使用者相關聯的金鑰<br />2 – 已完整佈建使用者 |
| FirstName          | nvarchar(50)  | 是         | 使用者的名字 |
| LastName           | nvarchar(50)  | 是         | 使用者的姓氏 |
| EmailAddress       | nvarchar(255) | 是         | 使用者的電子郵件地址 |

## <a name="vwworkflow"></a>vwWorkflow

此檢視會詳細顯示核心工作流程中繼資料以及工作流程的函式和參數。 針對報表而設計的，它也包含與工作流程相關聯之應用程式的中繼資料。 此檢視包含來自多個基礎資料表的資料，以利報告工作流程的資料。 對於每個工作流程，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   相關聯的工作流程開始狀態資訊

| 名稱                              | 類型          | 可為 Null | 描述                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                   | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName            | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                | bit           | 否          | 識別應用程式是否啟用 |
| WorkflowId                        | int           | 是         | 工作流程的唯一識別碼 |
| WorkflowName                      | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName               | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| WorkflowDescription               | nvarchar(255) | 是         | 工作流程的說明。 |
| WorkflowConstructorFunctionId     | int           | 否          | 此為工作流程函式的識別碼，可用來充任工作流程的建構函式 |
| WorkflowStartStateId              | int           | 否          | 狀態的唯一識別碼 |
| WorkflowStartStateName            | nvarchar(50)  | 否          | 狀態的名稱 |
| WorkflowStartStateDisplayName     | nvarchar(255) | 否          | 要在使用者介面中為狀態顯示的名稱 |
| WorkflowStartStateDescription     | nvarchar(255) | 是         | 工作流程狀態的說明 |
| WorkflowStartStateStyle           | nvarchar(50)  | 是         | 此值可識別工作流程在處於狀態時的完成百分比 |
| WorkflowStartStateValue           | int           | 否          | 狀態的值 |
| WorkflowStartStatePercentComplete | int           | 否          | 一個文字說明，可提示用戶端如何在 UI 中轉譯此狀態。 支援的狀態包括成功** 和失敗** |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

此檢視會詳細顯示核心工作流程中繼資料以及工作流程的函式和參數。 針對報表而設計的，它也包含與工作流程相關聯之應用程式的中繼資料。 此檢視包含來自多個基礎資料表的資料，以利報告工作流程的資料。 對於每個工作流程函式，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   工作流程函式詳細資料

| 名稱                                 | 類型          | 可為 Null | 描述                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName                      | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName               | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled                   | bit           | 否          | 識別應用程式是否啟用 |
| WorkflowId                           | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                         | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName                  | nvarchar(255) | 否          | 要在使用者介面中顯示的工作流程名稱 |
| WorkflowDescription                  | nvarchar(255) | 是         | 工作流程的說明 |
| WorkflowFunctionId                   | int           | 否          | 函式的唯一識別碼 |
| WorkflowFunctionName                 | nvarchar(50)  | 是         | 函式的名稱 |
| WorkflowFunctionDisplayName          | nvarchar(255) | 否          | 要在使用者介面中顯示的函式名稱 |
| WorkflowFunctionDescription          | nvarchar(255) | 是         | 工作流程函式的說明 |
| WorkflowFunctionIsConstructor        | bit           | 否          | 識別工作流程函式是否為工作流程的構造函式 |
| WorkflowFunctionParameterId          | int           | 否          | 函式參數的唯一識別碼 |
| WorkflowFunctionParameterName        | nvarchar(50)  | 否          | 函式參數的名稱 |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | 否          | 要在使用者介面中顯示的函式參數名稱 |
| WorkflowFunctionParameterDataTypeId  | int           | 否          | 與工作流程函式參數相關聯之資料類型的唯一識別碼 |
| WorkflowParameterDataTypeName        | nvarchar(50)  | 否          | 與工作流程函式參數相關聯之資料類型的名稱 |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

此檢視會顯示為工作流程定義的屬性。 對於每個屬性，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   工作流程屬性詳細資料

| 名稱                         | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName              | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName       | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| ApplicationEnabled           | bit           | 否          | 識別應用程式目前是否啟用。<br />**注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。 |
| WorkflowId                   | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                 | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName          | nvarchar(255) | 否          | 要在使用者介面中為工作流程顯示的名稱 |
| WorkflowDescription          | nvarchar(255) | 是         | 工作流程的說明 |
| WorkflowPropertyID           | int           | 否          | 工作流程屬性的唯一識別碼 |
| WorkflowPropertyName         | nvarchar(50)  | 否          | 屬性的名稱 |
| WorkflowPropertyDescription  | nvarchar(255) | 是         | 屬性的說明 |
| WorkflowPropertyDisplayName  | nvarchar(255) | 否          | 要在使用者介面中顯示的名稱 |
| WorkflowPropertyWorkflowId   | int           | 否          | 與此屬性相關聯的工作流程識別碼 |
| WorkflowPropertyDataTypeId   | int           | 否          | 為屬性定義的資料類型識別碼 |
| WorkflowPropertyDataTypeName | nvarchar(50)  | 否          | 為屬性定義的資料類型名稱 |
| WorkflowPropertyIsState      | bit           | 否          | 此欄位可識別此工作流程屬性是否包含工作流程的狀態 |

## <a name="vwworkflowstate"></a>vwWorkflowState

此檢視會顯示與工作流程相關聯的屬性。 對於每個合約，此檢視會包含下列資料：

-   相關聯的應用程式定義
-   相關聯的工作流程定義
-   工作流程狀態資訊

| 名稱                         | 類型          | 可為 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | 否          | 應用程式的唯一識別碼 |
| ApplicationName              | nvarchar(50)  | 否          | 應用程式的名稱 |
| ApplicationDisplayName       | nvarchar(255) | 否          | 應用程式的說明 |
| ApplicationEnabled           | bit           | 否          | 識別應用程式目前是否啟用。<br />**注意：** 即使應用程式在資料庫中可能反映為已停用，相關聯的合約仍會保留在區塊鏈上，且這些合約的相關資料也會保留在資料庫中。 |
| WorkflowId                   | int           | 否          | 工作流程的唯一識別碼 |
| WorkflowName                 | nvarchar(50)  | 否          | 工作流程的名稱 |
| WorkflowDisplayName          | nvarchar(255) | 否          | 要在使用者介面中為工作流程顯示的名稱 |
| WorkflowDescription          | nvarchar(255) | 是         | 工作流程的說明 |
| WorkflowStateID              | int           | 否          | 狀態的唯一識別碼 |
| WorkflowStateName            | nvarchar(50)  | 否          | 狀態的名稱 |
| WorkflowStateDisplayName     | nvarchar(255) | 否          | 要在使用者介面中為狀態顯示的名稱 |
| WorkflowStateDescription     | nvarchar(255) | 是         | 工作流程狀態的說明 |
| WorkflowStatePercentComplete | int           | 否          | 此值可識別工作流程在處於狀態時的完成百分比 |
| WorkflowStateValue           | nvarchar(50)  | 否          | 狀態的值 |
| WorkflowStateStyle           | nvarchar(50)  | 否          | 一個文字說明，可提示用戶端如何在 UI 中轉譯此狀態。 支援的狀態包括成功** 和失敗** |
