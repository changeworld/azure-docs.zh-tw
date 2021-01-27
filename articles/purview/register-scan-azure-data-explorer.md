---
title: 如何掃描 Azure 資料總管
description: 本操作指南說明如何掃描 Azure 資料總管的詳細資料。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896092"
---
# <a name="register-and-scan-azure-data-explorer"></a>註冊並掃描 Azure 資料總管

本文概述如何在 Azure 範疇中註冊 Azure 資料總管帳戶，以及如何設定掃描。

## <a name="supported-capabilities"></a>支援的功能

Azure 資料總管支援完整和增量掃描，以捕捉中繼資料和架構。 掃描也會根據系統和自訂分類規則自動分類資料。

## <a name="prerequisites"></a>先決條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
- 您必須是 Azure 範疇資料來源管理員

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描驗證

有一種方式可以設定 Azure 資料瀏覽器的驗證：

- 服務主體

### <a name="service-principal"></a>服務主體

若要使用服務主體驗證進行掃描，您可以使用現有的驗證，或建立一個新的。 

> [!Note]
> 如果需要建立新的服務主體，請依照下列步驟操作：
> 1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
> 1. 從左側功能表中，選取 [Azure Active Directory]。
> 1. 選取 **應用程式註冊**。
> 1. 選取 [+新增應用程式註冊]。
> 1. 輸入 **應用程式** 的名稱 (服務主體名稱)。
> 1. 選取 [僅此組織目錄中的帳戶]。
> 1. 在 [重新導向 URI] 中選取 [Web]，並輸入您想要的任何 URL；不一定要實際或工作。
> 1. 然後，選取 [註冊]。

需要取得服務主體的應用程式識別碼和密碼：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的服務主體
1. 從 [概觀] 複製 [應用程式 (用戶端) 識別碼] 的值，並從 [憑證和秘密] 複製 [用戶端密碼]。
1. 瀏覽至您的金鑰保存庫
1. 選取 [設定] > [秘密]
1. 選取 [+ 產生/匯入]，然後輸入您選擇的 [名稱]，以及與服務主體的 [用戶端密碼] 相同的 [值]
1. 選取 [建立] 以完成作業
1. 如果您的金鑰保存庫尚未連線至 Purview，您將需要[建立新的金鑰保存庫連線](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用服務主體[建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>授與服務主體對 Azure 資料 explorer 實例的存取權

1. 巡覽至 Azure 入口網站。 然後，流覽至您的 Azure data explorer 實例。

1. 將服務主體新增至 [**許可權**] 索引標籤中的 **AllDatabasesViewer** 角色，如下列螢幕擷取畫面所示。

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="新增許可權中的服務主體的螢幕擷取畫面" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>註冊 Azure 資料總管帳戶

若要在您的資料目錄中註冊新的 Azure 資料總管 (Kusto) 帳戶，請執行下列動作：

1. 瀏覽至您的 Purview 帳戶
1. 在左側導覽列中選取 [來源]
1. 選取 [註冊]
1. 在 [ **註冊來源**] 上，選取 [ **Azure 資料總管**
1. 選取 [繼續]

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="註冊新的資料來源" border="true":::

在 [ **註冊來源 (Azure 資料總管 (Kusto) # B3** ] 畫面上，執行下列動作：

1. 輸入會在目錄中列出的資料來源 **名稱**。
1. 選擇您要指向所需儲存體帳戶的方式：
   1. 從 **azure 訂用** 帳戶選取，從 [ **azure 訂** 用帳戶] 下拉式方塊中選取適當的訂用帳戶，然後從 [叢集 **] 下拉式方塊** 中選取適當的叢集。
   1. 或者，您也可以選取 [ **手動輸入** ]，然後輸入 (URL) 的服務端點。
1. **完成** 以註冊資料來源。

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="註冊來源選項" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)
