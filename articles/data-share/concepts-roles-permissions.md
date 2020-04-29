---
title: Azure Data Share 的角色和需求
description: 瞭解使用 Azure 資料共用來共用和接收資料所需的許可權。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265502"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和需求 

本文說明使用 Azure 資料共用服務共用和接收資料時所需的角色和許可權。 

## <a name="roles-and-requirements"></a>角色和需求

使用 Azure 資料共用服務，您可以共用資料，而不需要在資料提供者與取用者之間交換認證。 Azure 資料共用服務會使用受控識別（先前稱為 Msi）來向 Azure 資料存放區進行驗證。 

Azure 資料共用資源的受控識別必須被授與 Azure 資料存放區的存取權。 Azure 資料共用服務接著會使用此受控識別，來讀取和寫入快照式共用的資料，以及建立就地共用的符號連結。 

若要共用或接收來自 Azure 資料存放區的資料，使用者至少需要下列許可權。 以 SQL 為基礎的共用需要其他許可權。
* 寫入 Azure 資料存放區的許可權。 通常，此許可權存在於**參與者**角色中。
* 在 Azure 資料存放區中建立角色指派的許可權。 一般而言，建立角色指派的許可權會存在於**擁有**者角色、使用者存取系統管理員角色，或已指派 Microsoft 授權/角色指派/寫入權限的自訂角色中。 如果資料共用資源的受控識別已授與 Azure 資料存放區的存取權，則不需要此許可權。 請參閱下表中的必要角色。

以下是指派給資料共用資源的受控識別之角色的摘要：

| |  |  |
|---|---|---|
|**資料存放區類型**|**Data Provider 來源資料存放區**|**資料取用者目標資料存放區**|
|Azure Blob 儲存體| 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure Data Lake Gen1 | 擁有者 | 不支援
|Azure Data Lake Gen2 | 儲存體 Blob 資料讀者 | 儲存體 Blob 資料參與者
|Azure SQL Server | SQL DB 參與者 | SQL DB 參與者
|Azure 資料總管叢集 | 參與者 | 參與者
|

對於以 SQL 為基礎的共用，必須從 SQL 資料庫中的外部提供者，使用與 Azure 資料共用資源相同的名稱來建立 SQL 使用者。 以下是 SQL 使用者所需的許可權摘要。

| |  |  |
|---|---|---|
|**SQL Database 類型**|**Data Provider SQL 使用者權限**|**資料取用者 SQL 使用者權限**|
|Azure SQL Database | db_datareader | db_datareader、db_datawriter、db_ddladmin
|Azure Synapse Analytics (先前為 SQL DW) | db_datareader | db_datareader、db_datawriter、db_ddladmin
|


### <a name="data-provider"></a>資料提供者 
若要在 Azure 資料共用中新增資料集，必須將來源 Azure 資料存放區的存取權授與提供者資料共用資源的受控識別。 例如，在儲存體帳戶的案例中，會將儲存體 Blob 資料讀取者角色授與資料共用資源的受控識別。 

當使用者透過 Azure 入口網站新增資料集，而且使用者擁有適當的許可權時，Azure 資料共用服務就會自動完成這項作業。 例如，使用者是 Azure 資料存放區的擁有者，或是已指派 Microsoft 授權/角色指派/寫入權限的自訂角色的成員。 

或者，使用者可以擁有 Azure 資料存放區的擁有者，以手動方式將資料共用資源的受控識別新增至 Azure 資料存放區。 此動作只需要針對每個資料共用資源執行一次。

若要為資料共用資源的受控識別建立角色指派，請遵循下列步驟：

1. 流覽至 Azure 資料存放區。
1. 選取 **[存取控制（IAM）**]。
1. 選取 [新增角色指派]****。
1. 在 [*角色*] 下的 [角色指派] 資料表中選取角色（例如，針對 [儲存體帳戶]，選取 [*儲存體 Blob 資料讀取器*]）。
1. 在 [*選取*] 底下，輸入您的 Azure 資料共用資源名稱。
1. 按一下 *[儲存]* 。

針對以 SQL 為基礎的來源，除了上述步驟以外，您必須從 SQL 資料庫中的外部提供者，使用與 Azure 資料共用資源相同的名稱來建立 SQL 使用者。 此使用者必須被授與*db_datareader*許可權。 您可以在[共用您的資料](share-your-data.md)教學課程中，找到範例腳本以及 SQL 架構共用的其他必要條件。 

### <a name="data-consumer"></a>資料取用者
若要接收資料，取用者資料共用資源的受控識別必須被授與目標 Azure 資料存放區的存取權。 例如，在儲存體帳戶的案例中，會將儲存體 Blob 資料參與者角色授與資料共用資源的受控識別。 

如果使用者透過 Azure 入口網站指定目標資料存放區，且使用者擁有適當的許可權，Azure 資料共用服務就會自動完成此作業。 例如，使用者是 Azure 資料存放區的擁有者，或者是自訂角色的成員，其已指派 Microsoft 授權/角色指派/寫入權限。 

或者，使用者可以擁有 Azure 資料存放區的擁有者，以手動方式將資料共用資源的受控識別新增至 Azure 資料存放區。 此動作只需要針對每個資料共用資源執行一次。

若要手動為資料共用資源的受控識別建立角色指派，請遵循下列步驟：

1. 流覽至 Azure 資料存放區。
1. 選取 **[存取控制（IAM）**]。
1. 選取 [新增角色指派]****。
1. 在 [*角色*] 下的 [角色指派] 資料表中選取角色（例如，針對 [儲存體帳戶]，選取 [*儲存體 Blob 資料讀取器*]）。
1. 在 [*選取*] 底下，輸入您的 Azure 資料共用資源名稱。
1. 按一下 *[儲存]* 。

針對以 SQL 為基礎的目標，除了上述步驟，您必須從 SQL 資料庫中的外部提供者建立 SQL 使用者，其名稱與 Azure 資料共用資源相同。 此使用者必須被授與*db_datareader、db_datawriter db_ddladmin*許可權。 您可以在[接受和接收資料](subscribe-to-data-share.md)教學課程中找到範例腳本以及 SQL 架構共用的其他必要條件。 

如果您使用 REST Api 共用資料，則需要手動建立這些角色指派。 

若要深入瞭解如何新增角色指派，請參閱[此檔](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。 

## <a name="resource-provider-registration"></a>資源提供者註冊 

若要在您的 Azure 租使用者中第一次查看 Azure 資料共用邀請，您可能需要手動將 DataShare 資源提供者註冊到您的 Azure 訂用帳戶。 請遵循下列步驟，在您的 Azure 訂用帳戶中註冊 DataShare 資源提供者。 您需要 Azure 訂用帳戶的*參與者*存取權，才能註冊資源提供者。

1. 在 [Azure 入口網站中，流覽至 [訂用帳戶 **]。**
1. 選取您要用於 Azure 資料共用的訂用帳戶。
1. 按一下 [**資源提供者**]。
1. 搜尋 DataShare。
1. 按一下 [註冊]  。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)

