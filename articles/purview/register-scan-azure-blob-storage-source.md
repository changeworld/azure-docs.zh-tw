---
title: 如何掃描 Azure 儲存體 blob
description: 瞭解如何在 Azure 範疇資料目錄中掃描 Azure blob 儲存體。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880636"
---
# <a name="register-and-scan-azure-blob-storage"></a>註冊和掃描 Azure Blob 儲存體

本文概述如何在範疇中註冊 Azure Blob 儲存體帳戶，以及如何設定掃描。

## <a name="supported-capabilities"></a>支援的功能

Azure Blob 儲存體支援完整和增量掃描來捕捉中繼資料和架構。 它也會根據系統和自訂分類規則自動分類資料。

## <a name="prerequisites"></a>先決條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
- 您必須是 Azure 範疇資料來源管理員

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描驗證

有三種方式可以設定 Azure blob 儲存體的驗證：

- 受控識別
- 帳戶金鑰
- 服務主體

### <a name="managed-identity-recommended"></a> (建議的受控識別) 

當您選擇 [ **受控識別**] 時，若要設定連線，您必須先授與範疇帳戶掃描資料來源的許可權：

1. 瀏覽至儲存體帳戶。
1. 從左側導覽功能表中選取 [存取控制 (IAM)]。 
1. 選取 [+ 新增]。
1. 將 **角色** 設定為 **儲存體 Blob 資料讀取器** ，並在 [ **選取** 輸入] 方塊底下輸入您的 Azure 範疇帳戶名稱。 然後，選取 [儲存]，將此角色指派提供給您的 Purview 帳戶。

> [!Note]
> 如需詳細資訊，請參閱[使用 Azure Active Directory 授權存取 blob 和佇列](../storage/common/storage-auth-aad.md)的步驟

### <a name="account-key"></a>帳戶金鑰

當選取驗證方法為 **帳戶金鑰** 時，您必須取得存取金鑰，並將其儲存在金鑰保存庫中：

1. 流覽至您的儲存體帳戶
1. 選取 **> 存取金鑰的設定**
1. 複製您的 *金鑰* ，並將其儲存在某處以進行後續步驟
1. 瀏覽至您的金鑰保存庫
1. 選取 [設定] > [秘密]
1. 選取 [ **+ 產生/匯入**]，並輸入 **名稱** 和 **值** 作為儲存體帳戶的 *金鑰*
1. 選取 [建立] 以完成作業
1. 如果您的金鑰保存庫尚未連線至 Purview，您將需要[建立新的金鑰保存庫連線](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用金鑰 [建立新的認證](manage-credentials.md#create-a-new-credential) 來設定您的掃描

### <a name="service-principal"></a>服務主體

若要使用服務主體，您可以使用現有的主題或建立新的。 

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>將 blob 儲存體的存取權授與服務主體

1. 瀏覽至儲存體帳戶。
1. 從左側導覽功能表中選取 [存取控制 (IAM)]。 
1. 選取 [+ 新增]。
1. 將 **角色** 設定為 **儲存體 Blob 資料讀取器** ，並在 [ **選取** 輸入] 方塊下輸入您的服務主體名稱或物件識別碼。 然後選取 [ **儲存** ]，將此角色指派給您的服務主體。

## <a name="firewall-settings"></a>防火牆設定

> [!NOTE]
> 如果您已啟用儲存體帳戶的防火牆，您必須在設定掃描時使用 **受控識別** 驗證方法。

1. 移至[Azure 入口網站](https://portal.azure.com)中的儲存體帳戶
1. 流覽至 [ **設定] > 網路** 和
1. 選擇 [**允許存取來源**] 下的 [**選取的網路**]
1. 在 [**防火牆**] 區段中，選取 [**允許信任的 Microsoft 服務存取此儲存體帳戶**]，然後按下 [**儲存**]

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="顯示防火牆設定的螢幕擷取畫面":::

## <a name="register-an-azure-blob-storage-account"></a>註冊 Azure Blob 儲存體帳戶

若要在您的資料目錄中註冊新的 blob 帳戶，請執行下列動作：

1. 瀏覽至您的 Purview 帳戶
1. 在左側導覽列中選取 [來源]
1. 選取 [註冊]
1. 在 [ **註冊來源**] 上，選取 **Azure Blob 儲存體**
1. 選取 [繼續]

在 [ **註冊來源 (] Azure Blob 儲存體)** 畫面上，執行下列動作：

1. 輸入會在目錄中列出的資料來源 **名稱**。 
1. 選擇您的訂用帳戶以篩選出儲存體帳戶
1. 選取儲存體帳戶
1. 選取集合或建立新的集合 (選擇性)
1. **完成** 以註冊資料來源。

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="註冊來源選項" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)