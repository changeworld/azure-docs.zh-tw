---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017662"
---
請一律使用 [Azure AD 安全性群組](../articles/active-directory/fundamentals/active-directory-manage-groups.md) 作為 ACL 專案中指派的主體。 避免直接指派個別使用者或服務主體。 使用此結構，可讓您直接新增和移除使用者或服務主體，而不需要將 ACL 重新套用至整個目錄結構。 相反地，您可以直接從適當的 Azure AD 安全性群組新增或移除使用者和服務主體。 

有許多不同的方式可以設定群組。 例如，假設您有一個名為 **/LogData** 的目錄，它會保存伺服器所產生的記錄資料。 Azure Data Factory (ADF) 將資料內嵌至該資料夾。 服務工程小組的特定使用者將上傳記錄並管理此資料夾的其他使用者，而各種 Databricks 叢集將會分析該資料夾中的記錄。 

若要啟用這些活動，您可以建立 `LogsWriter` 群組和 `LogsReader` 群組。 然後，您可以指派許可權，如下所示：

- 將 `LogsWriter` 群組新增至具有許可權的 **/LogData** 目錄的 ACL `rwx` 。
- 將 `LogsReader` 群組新增至具有許可權的 **/LogData** 目錄的 ACL `r-x` 。
- 將適用于 ADF 的服務主體物件或受控服務識別 (MSI) 新增至 `LogsWriters` 群組。
- 在服務工程團隊中將使用者新增至 `LogsWriter` 群組。
- 將 Databricks 的服務主體物件或 MSI 新增至 `LogsReader` 群組。

如果服務工程團隊的使用者離開公司，您可以直接從群組中移除 `LogsWriter` 。 如果您未將該使用者新增至群組，而是為該使用者新增專用的 ACL 專案，則必須從 **/LogData** 目錄中移除該 acl 專案。 您也必須從 **/LogData** 目錄的整個目錄階層中的所有子目錄和檔案中移除專案。 

若要建立群組和新增成員，請參閱 [使用 Azure Active Directory 建立基本群組和新增成員](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。