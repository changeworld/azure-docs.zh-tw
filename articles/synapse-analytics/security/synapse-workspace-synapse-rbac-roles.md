---
title: Synapse RBAC 角色
description: 本文說明內建的 Synapse RBAC 角色
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 55a1255ffa82aec8ea7b9e1ec2bbc6746b9b0636
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523402"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC 角色

本文說明內建的 Synapse RBAC 角色、其授與的許可權，以及可使用這些角色的範圍。  

## <a name="whats-changed-since-the-preview"></a>預覽以來有哪些變更？
針對熟悉預覽期間所提供之 Synapse RBAC 角色的使用者，適用下列變更：
- 工作區系統管理員已重新命名為 **Synapse Administrator**
- Apache Spark 系統管理員重新命名為 **Synapse Apache Spark 系統管理員** ，而且有權查看所有已發佈的程式碼成品，包括 SQL 腳本。  此角色不再提供使用工作區 MSI 的許可權，這需要 Synapse 認證使用者角色。  需要有此許可權，才能執行管線。 
- SQL 系統管理員已重新命名為 **SYNAPSE Sql 系統管理員** ，並具有查看所有已發佈程式碼成品（包括 Spark 筆記本和作業）的許可權。  此角色不再提供使用工作區 MSI 的許可權，這需要 Synapse 認證使用者角色。 需要有此許可權，才能執行管線。
- 引進 **新的更精細 SYNAPSE RBAC 角色**，著重于支援開發和作業角色，而不是特定的分析執行時間。  
- 針對數個角色引進 **新的較低層級範圍**。  這些範圍允許將角色限制為特定的資源或物件。

>[!Note]
>- 新的角色和較低層級的範圍目前為預覽狀態。  
>- 在未來的版本中，將會取代 Apache Spark 系統管理員和 SQL 系統管理員角色，以使用新的 Synapse RBAC 角色。  建議您採用新的預覽角色（這些角色是完全受支援的角色），並提供其使用的意見反應。

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>內建 Synapse RBAC 角色和範圍

下表說明每個內建角色，以及可使用這些角色的範圍。

>[!Note]
> 在任何範圍內具有任何 Synapse RBAC 角色的使用者，都會在工作區範圍自動擁有 Synapse 使用者角色。 

|角色 |權限|範圍|
|---|---|-----|
|Synapse 系統管理員  |無伺服器 SQL 集區、Apache Spark 集區和整合執行時間的完整 Synapse 存取權。  包含所有已發佈程式碼成品的建立、讀取、更新和刪除存取權。  包括計算運算子、連結的資料管理員，以及工作區系統身分識別認證的認證使用者許可權。  包括指派 Synapse RBAC 角色。  需要有 Azure 許可權，才能建立、刪除及管理計算資源。 </br></br>_可以讀取和寫入構件 </br> 可以對 Spark 活動執行所有動作。 </br> 可以 view Spark 集區記錄 </br> 可查看已儲存的筆記本和管線輸出 </br> 可以使用連結服務所儲存的秘密，或認證 </br> 可使用 sql、、和許可權連接到 sql 無伺服器端點。您 `db_datareader` `db_datawriter` `connect` `grant` </br> 可以在目前的範圍內指派及撤銷 Synapse RBAC 角色_|工作區 </br> Spark 集區<br/>整合執行階段 </br>連結的服務</br>認證 |
|Synapse Apache Spark 系統管理員</br>|Apache Spark 集區的完整 Synapse 存取權。  建立、讀取、更新和刪除已發佈的 Spark 作業定義、筆記本及其輸出，以及程式庫、連結的服務和認證的存取權。  包含所有其他已發佈程式碼成品的讀取權限。 不包含使用認證和執行管線的許可權。 不包含授與存取權。 </br></br>_可以在 spark 活動上執行所有動作都 </br> 可以執行 spark 活動上的所有動作_|工作區</br>Spark 集區|
|Synapse SQL 系統管理員|無伺服器 SQL 集區的完整 Synapse 存取權。  建立、讀取、更新和刪除已發行的 SQL 腳本、認證和連結服務的存取權。  包含所有其他已發佈程式碼成品的讀取權限。  不包含使用認證和執行管線的許可權。 不包含授與存取權。 </br></br>*可以在 sql 腳本上執行的所有動作都 <br/> 可以使用 sql、、 `db_datareader` `db_datawriter` `connect` 和 `grant` 許可權連接到 sql 無伺服器端點*|工作區|
|Synapse 參與者|無伺服器 SQL 集區、Apache Spark 集區、整合執行時間的完整 Synapse 存取權。  包括建立、讀取、更新和刪除所有已發佈程式碼成品及其輸出的存取權，包括認證和連結服務。  包含計算運算子許可權。 不包含使用認證和執行管線的許可權。 不包含授與存取權。 </br></br>_可以讀取和寫入構件 </br> 可以查看儲存的筆記本，而管線輸出 </br> 可以執行 spark 活動的所有動作， </br> 以查看 spark 集區記錄_|工作區 </br> Spark 集區<br/> 整合執行階段|
|Synapse 構件發行者|建立、讀取、更新和刪除已發佈程式碼成品及其輸出的存取權。 不包含執行程式碼或管線或授與存取權的許可權。 </br></br>_可以讀取已發佈的成品和發佈成品 </br> ，以查看儲存的筆記本、Spark 作業和管線輸出_|工作區
|Synapse 成品使用者|已發行的程式碼成品及其輸出的讀取權限。 可以建立新的構件，但無法發佈變更或執行程式碼，而不需要其他許可權。|工作區
|Synapse 計算運算子 |提交 Spark 作業和筆記本，並查看記錄。  包括取消任何使用者所提交的 Spark 作業。 需要工作區系統身分識別的其他使用認證許可權，才能執行管線、查看管線執行和輸出。 </br></br>_可以提交和取消作業，包括其他人所提交的作業 </br> 可以查看 Spark 集區記錄_|工作區</br>Spark 集區</br>整合執行階段|
|Synapse 認證使用者|在管線執行等活動的認證和連結服務中，執行時間和設定時間使用秘密。 若要執行管線，需要此角色，範圍設定為工作區系統身分識別。 </br></br>_根據認證的範圍，允許透過受認證保護的連結服務來存取資料 (也需要使用「計算許可權」，) </br> 可讓工作區系統身分識別認證所保護的管線可使用額外的「計算許可權」許可權 ()_|工作區 </br>連結服務</br>認證
|Synapse 連結資料管理員|建立及管理受控私人端點、連結的服務和認證。 可以建立使用受認證保護之連結服務的受控私人端點|工作區|
|Synapse 使用者|列出和查看 SQL 集區、Apache Spark 集區、整合執行時間和已發佈連結服務和認證的詳細資料。 不包含其他已發佈的程式碼成品。  可以建立新的構件，但無法在沒有其他許可權的情況下執行或發行。</br></br>_可以列出和讀取 Spark 集區、整合執行時間。_|工作區、Spark 集區</br>連結的服務 </br>認證|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC 角色及其允許的動作

>[!Note]
>- 下表中列出的所有動作都有前面加上 "Synapse/..."</br>
>- 所有成品讀取、寫入和刪除動作都與即時服務中的已發佈成品有關。  這些許可權不會影響對已連線的 Git 存放庫中的成品的存取。  

下表列出內建角色以及各項支援的動作/許可權。

角色|動作
--|--
Synapse 系統管理員|工作區/讀取</br>workspace/roleAssignments/write、delete</br>workspace/managedPrivateEndpoint/write、delete</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>工作區/構件/讀取</br>工作區/筆記本/寫入、刪除</br>workspace/sparkJobDefinitions/write、delete</br>workspace/sqlScripts/write、delete</br>workspace/資料流程/write、delete</br>工作區/管線/寫入、刪除</br>工作區/觸發程式/寫入、刪除</br>工作區/資料集/寫入、刪除</br>工作區/程式庫/寫入、刪除</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除</br>工作區/筆記本/viewOutputs/action</br>工作區/管線/viewOutputs/動作</br>workspace/Linkedservices.json 和 datasets.json/useSecret/action</br>工作區/認證/useSecret/動作|
|Synapse Apache Spark 系統管理員|工作區/讀取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>工作區/筆記本/viewOutputs/action</br>工作區/構件/讀取</br>工作區/筆記本/寫入、刪除</br>workspace/sparkJobDefinitions/write、delete</br>工作區/程式庫/寫入、刪除</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除|
|Synapse SQL 系統管理員|工作區/讀取</br>工作區/構件/讀取</br>workspace/sqlScripts/write、delete</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除|
|Synapse 參與者|工作區/讀取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>workspace/integrationRuntimes/viewLogs/action</br>工作區/構件/讀取</br>工作區/筆記本/寫入、刪除</br>workspace/sparkJobDefinitions/write、delete</br>workspace/sqlScripts/write、delete</br>workspace/資料流程/write、delete</br>工作區/管線/寫入、刪除</br>工作區/觸發程式/寫入、刪除</br>工作區/資料集/寫入、刪除</br>工作區/程式庫/寫入、刪除</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除</br>工作區/筆記本/viewOutputs/action</br>工作區/管線/viewOutputs/動作|
|Synapse 構件發行者|工作區/讀取</br>工作區/構件/讀取</br>工作區/筆記本/寫入、刪除</br>workspace/sparkJobDefinitions/write、delete</br>workspace/sqlScripts/write、delete</br>workspace/資料流程/write、delete</br>工作區/管線/寫入、刪除</br>工作區/觸發程式/寫入、刪除</br>工作區/資料集/寫入、刪除</br>工作區/程式庫/寫入、刪除</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除</br>工作區/筆記本/viewOutputs/action</br>工作區/管線/viewOutputs/動作|
|Synapse 成品使用者|工作區/讀取</br>工作區/構件/讀取</br>工作區/筆記本/viewOutputs/action</br>工作區/管線/viewOutputs/動作|
|Synapse 計算運算子 |工作區/讀取</br>workspace/bigDataPools/useCompute/action</br>workspace/bigDataPools/viewLogs/action</br>workspace/integrationRuntimes/useCompute/action</br>workspace/integrationRuntimes/viewLogs/action|
|Synapse 認證使用者|工作區/讀取</br>workspace/Linkedservices.json 和 datasets.json/useSecret/action</br>工作區/認證/useSecret/動作|
|Synapse 連結資料管理員|工作區/讀取</br>workspace/managedPrivateEndpoint/write、delete</br>workspace/Linkedservices.json 和 datasets.json/write、delete</br>工作區/認證/寫入、刪除|
|Synapse 使用者|工作區/讀取|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC 動作和允許他們的角色

下表列出 Synapse 動作和允許這些動作的內建角色：

動作|角色
--|--
工作區/讀取|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse SQL 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 成品使用者</br>Synapse 計算運算子 </br>Synapse 認證使用者</br>Synapse 連結資料管理員</br>Synapse 使用者 
workspace/roleAssignments/write、delete|Synapse 系統管理員
workspace/managedPrivateEndpoint/write、delete|Synapse 系統管理員</br>Synapse 連結資料管理員
workspace/bigDataPools/useCompute/action|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 計算運算子 
workspace/bigDataPools/viewLogs/action|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 計算運算子 
workspace/integrationRuntimes/useCompute/action|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 計算運算子 
工作區/構件/讀取|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse SQL 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 成品使用者
工作區/筆記本/寫入、刪除|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
workspace/sparkJobDefinitions/write、delete|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
workspace/sqlScripts/write、delete|Synapse 系統管理員</br>Synapse SQL 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
workspace/資料流程/write、delete|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
工作區/管線/寫入、刪除|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
工作區/觸發程式/寫入、刪除|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
工作區/資料集/寫入、刪除|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
工作區/程式庫/寫入、刪除|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者
workspace/Linkedservices.json 和 datasets.json/write、delete|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 連結資料管理員
工作區/認證/寫入、刪除|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 連結資料管理員
工作區/筆記本/viewOutputs/action|Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 成品使用者
工作區/管線/viewOutputs/動作|Synapse 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 成品使用者
workspace/Linkedservices.json 和 datasets.json/useSecret/action|Synapse 系統管理員</br>Synapse 認證使用者
工作區/認證/useSecret/動作|Synapse 系統管理員</br>Synapse 認證使用者

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC 範圍及其支援的角色

下表列出 Synapse RBAC 範圍，以及可在每個範圍指派的角色。 

>[!note]
>若要建立或刪除物件，您必須擁有較高層級範圍的許可權。

範圍|角色
--|--
工作區 |Synapse 系統管理員</br>Synapse Apache Spark 系統管理員</br>Synapse SQL 系統管理員</br>Synapse 參與者</br>Synapse 構件發行者</br>Synapse 成品使用者</br>Synapse 計算運算子 </br>Synapse 認證使用者</br>Synapse 連結資料管理員</br>Synapse 使用者
Apache Spark 集區 | Synapse 系統管理員 </br>Synapse 參與者 </br> Synapse 計算運算子
整合執行階段 | Synapse 系統管理員 </br>Synapse 參與者 </br> Synapse 計算運算子
連結的服務 |Synapse 系統管理員 </br>Synapse 認證使用者
認證 |Synapse 系統管理員 </br>Synapse 認證使用者
 
>[!note]
>所有成品角色和動作都是在工作區層級設定範圍。 

## <a name="next-steps"></a>後續步驟

瞭解 [如何檢查工作區的 SYNAPSE RBAC 角色指派](./how-to-review-synapse-rbac-role-assignments.md) 。

瞭解 [如何指派 SYNAPSE RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)