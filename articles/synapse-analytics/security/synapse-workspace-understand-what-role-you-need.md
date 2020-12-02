---
title: 瞭解在 Synapse 中執行一般工作所需的角色
description: '本文說明完成特定工作所需的內建 Synapse RBAC 角色 () '
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: aadc8e817eb2b5de856ac73cfd010b48d0531bfc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523401"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>瞭解在 Synapse 中執行一般工作所需的角色

本文將協助您瞭解哪些 Synapse RBAC (角色型存取控制) 或您在 Synapse Studio 中完成工作所需的 Azure RBAC 角色。  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio 存取控制和工作流程摘要 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>存取 Synapse Studio 並查看其內容

- 您可以開啟 Synapse Studio 並查看工作區的詳細資料，並列出其任何 Azure 資源 (SQL 集區、Spark 集區或整合執行時間) 如果您已獲指派任何 Synapse RBAC 角色，或擁有工作區的 Azure 擁有者、參與者或讀取者角色。

### <a name="resource-management"></a>資源管理

- 如果您是工作區的 Azure 擁有者或參與者，您可以建立 SQL 集區、Apache Spark 集區和整合執行時間。
- 如果您是工作區或該資源的 Azure 擁有者或參與者，您可以暫停或調整專用的 SQL 集區、設定 Spark 集區或整合執行時間。

### <a name="viewing-and-editing-code-artifacts"></a>查看及編輯程式碼成品

- 有了 Synapse Studio 的存取權，您就可以建立新的程式碼成品，例如 SQL 腳本、筆記本、spark 作業、連結的服務、管線、資料流程、觸發程式和認證。  您可以使用其他許可權來發佈或儲存這些構件 (。 )   
- 如果您是 Synapse 成品使用者、Synapse 成品發行者、Synapse 參與者或 Synapse 系統管理員，您可以列出、開啟和編輯已發佈的程式碼成品。

### <a name="executing-your-code"></a>執行您的程式碼

- 如果您有在 SQL 集區中定義的必要 SQL 許可權，您可以在 SQL 集區上執行 SQL 腳本。  
- 如果您已在工作區或特定 Apache Spark 集區上 Synapse 計算操作員許可權，就可以執行筆記本和 Spark 作業。  
- 使用工作區或特定整合執行時間的計算運算子許可權，以及您可以執行管線的適當認證許可權。

### <a name="monitoring-and-managing-execution"></a>監視和管理執行
- 如果您是 Synapse 使用者，您可以在 Apache Spark 集區中檢查執行中的筆記本和作業的狀態。
- 如果您是工作區或特定 Spark 集區或管線的 Synapse 計算運算子，您可以查看記錄並取消執行中的作業和管線。  

### <a name="publishing-and-saving-your-code"></a>發行和儲存您的程式碼

- 如果您是 Synapse 構件發行者、Synapse 參與者或 Synapse 系統管理員，您可以將新的或更新的程式碼成品發佈至服務。 
- 如果工作區已啟用 Git 且您具有 Git 許可權，您可以將程式碼成品認可至 Git 存放庫的工作分支。 在啟用 Git 的情況下，只允許從共同作業分支進行發佈。
- 如果您關閉 Synapse Studio 而不發行或認可程式碼成品的變更，這些變更將會遺失。


## <a name="tasks-and-required-roles"></a>工作和必要角色

下表列出適用于每個工作的一般工作，以及所需的 Synapse RBAC 或 Azure RBAC 角色。  

>[!Note]
>- 系統不會針對每個工作列出 Synapse 系統管理員，除非它是唯一提供必要許可權的角色。  Synapse 系統管理員可以執行其他 Synapse RBAC 角色所啟用的所有工作。</br>
>- 任何範圍的所有 Synapse RBAC 角色都可讓您在工作區中 Synapse 使用者權限
>- 資料表中顯示的所有 Synapse RBAC 許可權/動作都是前置詞為 Microsoft/Synapse/workspace/.。。 </br>


工作 (我想要 ... )  |我需要的角色 (... ) |Synapse RBAC 許可權/動作
--|--|--
|在工作區上開啟 Synapse Studio|Synapse 使用者或|讀取
| |工作區上的 Azure 擁有者、參與者或讀取者|無
|列出 SQL 集區、Apache Spark 集區、整合執行時間和存取其設定詳細資料|Synapse 使用者或|讀取|
||工作區上的 Azure 擁有者、參與者或讀取者|無
|列出連結的服務、認證、受控的私人端點|Synapse 使用者|讀取
**SQL 集區**||
建立專用的 SQL 集區或無伺服器的 SQL 集區|工作區上的 Azure 擁有者或參與者|無
管理 (暫停、調整或刪除) 專用的 SQL 集區|SQL 集區或工作區上的 Azure 擁有者或參與者|無
建立 SQL 腳本</br>|Synapse 使用者或 </br>工作區上的 Azure 擁有者或參與者 </br>*需要額外的 sql 許可權，才能執行 SQL 腳本*。|
列出並開啟任何已發佈的 SQL 腳本| Synapse 成品使用者、成品發行者、Synapse 參與者|構件/讀取
在無伺服器的 SQL 集區上執行 SQL 腳本|集區上的 SQL 許可權 (自動授與 Synapse 系統管理員) |無
在專用的 SQL 集區上執行 SQL 腳本|需要集區上的 SQL 許可權|無
發佈新的、已更新或已刪除的 SQL 腳本|Synapse 成品發行者，Synapse 參與者|sqlScripts/write、delete
將 SQL 腳本的變更認可至 Git 存放庫|需要存放庫的 Git 許可權|
在工作區 (透過 Azure 入口網站中的工作區屬性指派 Active Directory 系統管理員) |工作區上的 Azure 擁有者或參與者 |
**Apache Spark 集區**||
建立 Apache Spark 集區|工作區上的 Azure 擁有者或參與者|
監視 Apache Spark 的應用程式| Synapse 使用者|讀取
查看筆記本和作業執行的記錄 |Synapse 計算運算子|
取消任何在 Apache Spark 集區上執行的筆記本或 Spark 作業|Apache Spark 集區上的 Synapse 計算運算子。|bigDataPools/useCompute
建立筆記本或作業定義|在工作區上 Synapse 使用者或 Azure 擁有者、參與者或讀者</br> *需要有額外的許可權，才能執行、發佈或儲存*|讀取
列出並開啟已發佈的筆記本或作業定義，包括審核儲存的輸出|Synapse 成品使用者、Synapse 成品發行者、工作區上的 Synapse 參與者|構件/讀取
執行筆記本並檢查其輸出|Synapse Apache Spark 系統管理員，在選取的 Apache Spark 集區上的 Synapse 計算運算子|bigDataPools/useCompute 
發佈或刪除筆記本或工作定義 (包括服務的輸出) |工作區上的構件發行者，Synapse Apache Spark 系統管理員|筆記本/write、delete
將筆記本或作業定義的變更認可至 Git 工作分支|Git 許可權|無
**管線、整合執行時間、資料流程、資料集和觸發程式**||
建立、更新或刪除整合執行時間|工作區上的 Azure 擁有者或參與者|
監視整合執行時間狀態|Synapse 使用者|讀取、管線/viewOutputs
檢查管線執行|Synapse 成品發行者/Synapse 參與者|讀取、管線/viewOutputs 
建立管線 |Synapse 使用者 </br>[依 *_考慮 + Synapse Credential User On WorkspaceSystemIdentity_* _]</br>需要 _Additional 許可權才能發佈或儲存 *|讀取、認證/UseSecret/動作
建立資料流程、資料集或觸發程式 |Synapse 使用者</br>*需要其他許可權才能發佈或儲存*|讀取
列出並開啟已發佈的管線 |Synapse 成品使用者 | 構件/讀取
預覽資料集資料|WorkspaceSystemIdentity 上的 Synapse 使用者 + Synapse 認證使用者| 
使用預設整合執行時間來對管線進行調試|WorkspaceSystemIdentity 認證上的 Synapse 使用者 + Synapse 認證使用者|讀 </br>認證/useSecret
建立觸發程式，包括立即觸發程式|WorkspaceSystemIdentity 上的 Synapse 使用者 + Synapse 認證使用者|讀取、認證/useSecret/動作
使用資料複製工具複製資料|在工作區系統身分識別上 Synapse 使用者 + Synapse 認證使用者|讀取、認證/useSecret/動作
使用排程) 內嵌資料 (|在工作區系統身分識別上 Synapse 作者 + Synapse 認證使用者|讀取、認證/useSecret/動作
將新的、已更新或已刪除的管線、資料流程或觸發程式發佈到服務|在工作區上 Synapse 成品發行者|管線/寫入、刪除</br>資料流程 write、delete</br>觸發程式/寫入、刪除
將新的、已更新或已刪除的資料流程、資料集或觸發程式發佈到服務|工作區上的構件發行者|觸發程式/寫入、刪除
儲存 (認可對 Git 存放庫的管線、資料流程、資料集、觸發程式) 變更 |Git 許可權|無 
**連結的服務**||
建立連結的服務 (包括指派認證) |Synapse 使用者</br>*需要有額外的許可權，才能執行、發佈或儲存*|讀取
列出並開啟已發佈的連結服務|Synapse 成品使用者|Linkedservices.json 和 datasets.json/write、delete  
在受認證保護的連結服務上測試連接|Synapse 使用者和 Synapse 認證使用者|認證/useSecret/action|
發佈連結服務|Synapse 構件發行者|Linkedservices.json 和 datasets.json/write、delete
將 (認可) 連結的服務定義儲存至 Git 存放庫|Git 許可權|無
**存取管理**||
複習 Synapse 任何範圍的 RBAC 角色指派|Synapse 使用者|讀取
指派和移除使用者、群組和服務主體的 Synapse RBAC 角色指派| Synapse 工作區或特定工作區專案範圍的系統管理員|roleAssignments/write、delete
建立或移除程式碼成品的 Synapse RBAC 存取權|Synapse 工作區範圍的系統管理員|roleAssignments/write、delete   

>[!Note]
>來自另一個租使用者的來賓使用者無法檢查、新增或變更角色指派，不論角色指派的角色為何。 

## <a name="next-steps"></a>後續步驟

瞭解 [如何審核 SYNAPSE RBAC 角色指派](./how-to-review-synapse-rbac-role-assignments.md)

瞭解 [如何管理 SYNAPSE RBAC 角色指派](./how-to-manage-synapse-rbac-role-assignments.md)。 