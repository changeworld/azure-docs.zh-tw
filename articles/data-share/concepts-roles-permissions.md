---
title: Azure Data Share 的角色和需求
description: 深入瞭解使用 Azure Data Share 共用及接收資料時所需的許可權。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: efb86dbcbe7619ff6727c5e7374835dc3fc7d731
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220494"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和需求 

本文說明使用 Azure Data Share service 共用及接收資料時所需的角色和許可權。 

## <a name="roles-and-requirements"></a>角色和需求

透過 Azure Data Share service，您可以在不交換資料提供者與取用者之間的認證的情況下共用資料。 Azure Data Share 服務使用受控識別 (先前稱為 Msi) 來向 Azure 資料存放區進行驗證。 

Azure Data Share 資源的受控識別必須獲得 Azure 資料存放區的存取權。 Azure Data Share 服務接著會使用此受控識別來讀取和寫入快照式共用的資料，以及建立就地共用的符號連結。 

若要從 Azure 資料存放區共用或接收資料，使用者至少需要下列許可權。 以 SQL 為基礎的共用需要其他許可權。

* 寫入 Azure 資料存放區的許可權。 一般來說，此許可權存在於 **參與者** 角色中。
* 在 Azure 資料存放區中建立角色指派的許可權。 一般而言，建立角色指派的許可權存在於 **擁有** 者角色、使用者存取系統管理員角色，或具有 Microsoft 的自訂角色中。指派的授權/角色指派/寫入權限。 如果資料共用資源的受控識別已獲得 Azure 資料存放區的存取權，則不需要此許可權。 請參閱下表以取得必要的角色。

以下是指派給 Data Share 資源受控識別的角色摘要：

|**資料存放區類型**|**Data Provider 來源資料存放區**|**資料取用者目標資料存放區**|
|---|---|---|
|Azure Blob 儲存體| 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure Data Lake Gen1 | 擁有者 | 不支援
|Azure Data Lake Gen2 | 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure 資料總管叢集 | 參與者 | 參與者
|

針對以 SQL 為基礎的共用，必須從 Azure SQL Database 中的外部提供者建立 SQL 使用者，且名稱與 Azure Data Share 資源相同。 需要 Azure Active Directory 系統管理員許可權，才能建立此使用者。 以下是 SQL 使用者所需的許可權摘要。

|**SQL Database 類型**|**Data Provider SQL 使用者權限**|**資料取用者 SQL 使用者權限**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader、db_datawriter db_ddladmin
|Azure Synapse Analytics (先前為 SQL DW) | db_datareader | db_datareader、db_datawriter db_ddladmin
|

### <a name="data-provider"></a>資料提供者

若要在 Azure Data Share 中新增資料集，必須將來源 Azure 資料存放區的存取權授與提供者資料共用資源的受控識別。 例如，在儲存體帳戶的案例中，會將儲存體 Blob 資料讀取器角色授與 data share 資源的受控識別。 

當使用者透過 Azure 入口網站新增資料集，且使用者具有適當的許可權時，Azure Data Share 服務會自動完成此工作。 例如，使用者是 Azure 資料存放區的擁有者，或是已指派 Microsoft 授權/角色指派/寫入權限的自訂角色成員。 

或者，使用者可以讓 Azure 資料存放區的擁有者以手動方式將資料共用資源的受控識別新增至 Azure 資料存放區。 每個資料共用資源只需要執行一次此動作。

若要手動為資料共用資源的受控識別建立角色指派，請遵循下列步驟。  

1. 流覽至 Azure 資料存放區。
1. 選取 [存取控制 (IAM)]。
1. 選取 [ **新增角色指派**]。
1. 在 [ *角色*] 之下，選取上述角色指派表中的角色 (例如，針對 [儲存體帳戶]，選取 [ *儲存體 Blob 資料讀取器* ]) 。
1. 在 [ *選取*] 下，輸入您的 Azure Data Share 資源的名稱。
1. 按一下 [儲存]。

若要深入瞭解角色指派，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。 如果您要使用 REST Api 來共用資料，您可以使用 API 來建立角色指派，方法是參考 [使用 REST API 的 [新增] 或 [移除 Azure 角色指派](../role-based-access-control/role-assignments-rest.md)]。 

針對以 SQL 為基礎的來源，您必須使用與 Azure Data Share 資源相同名稱的 SQL Database 外部提供者建立 SQL 使用者，同時使用 Azure Active Directory 驗證連接到 SQL Database。 此使用者必須獲得 *db_datareader* 許可權。 您可以在 [Azure SQL Database 或 Synapse Analytics](how-to-share-from-sql.md) 教學課程的共用中，找到範例腳本以及 SQL 型共用的其他必要條件。 

### <a name="data-consumer"></a>資料取用者
若要接收資料，必須將目標 Azure 資料存放區的存取權授與取用者資料共用資源的受控識別。 例如，在儲存體帳戶的案例中，會將儲存體 Blob 資料參與者角色授與 data share 資源的受控識別。 

如果使用者透過 Azure 入口網站指定目標資料存放區，且使用者具有適當的許可權，則 Azure Data Share 服務會自動完成此設定。 例如，使用者是 Azure 資料存放區的擁有者，或是已指派 Microsoft 授權/角色指派/寫入權限的自訂角色成員。 

或者，使用者可以讓 Azure 資料存放區的擁有者以手動方式將資料共用資源的受控識別新增至 Azure 資料存放區。 每個資料共用資源只需要執行一次此動作。

若要手動為資料共用資源的受控識別建立角色指派，請遵循下列步驟。 

1. 流覽至 Azure 資料存放區。
1. 選取 [存取控制 (IAM)]。
1. 選取 [ **新增角色指派**]。
1. 在 [ *角色*] 之下，選取上述角色指派表中的角色 (例如，針對 [儲存體帳戶]，選取 [ *儲存體 Blob 資料讀取器* ]) 。
1. 在 [ *選取*] 下，輸入您的 Azure Data Share 資源的名稱。
1. 按一下 [儲存]。

若要深入瞭解角色指派，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。 如果您是使用 REST Api 來接收資料，您可以使用 API 來建立角色指派，方法是參考 [使用 REST API 的 [新增] 或 [移除 Azure 角色指派](../role-based-access-control/role-assignments-rest.md)]。 

若為 SQL 型目標，當使用 Azure Active Directory 驗證連接到 SQL Database 時，必須使用與 Azure Data Share 資源相同名稱 SQL Database 中的外部提供者建立 SQL 使用者。 此使用者必須獲得 *db_datareader、db_datawriter db_ddladmin* 許可權。 您可以在 [Azure SQL Database 或 Synapse Analytics](how-to-share-from-sql.md) 教學課程的共用中，找到範例腳本以及 SQL 型共用的其他必要條件。 

## <a name="resource-provider-registration"></a>資源提供者註冊 

在下列案例中，您可能需要手動將 Microsoft.datashare 資源提供者註冊至您的 Azure 訂用帳戶： 

* 在您的 Azure 租使用者中第一次觀看 Azure Data Share 邀請
* 從 Azure Data Share 資源，在不同 Azure 訂用帳戶中的 Azure 資料存放區共用資料
* 從 Azure Data Share 資源將資料接收到不同 Azure 訂用帳戶中的 Azure 資料存放區

請遵循下列步驟，將 Microsoft.datashare 資源提供者註冊至您的 Azure 訂用帳戶。 您需要 Azure 訂用帳戶的 *參與者* 存取權，才能註冊資源提供者。

1. 在 Azure 入口網站中，流覽至 [訂用帳戶 **]。**
1. 選取您要用於 Azure Data Share 的訂用帳戶。
1. 按一下 [ **資源提供者**]。
1. 搜尋 Microsoft.datashare。
1. 按一下 [註冊] 。
 
若要深入瞭解資源提供者，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)