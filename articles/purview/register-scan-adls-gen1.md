---
title: 註冊並掃描 Azure Data Lake Storage (ADLS) Gen1
description: 本教學課程說明如何在 Azure 範疇中掃描 Azure Data Lake Storage Gen1 的資料。
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693717"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>註冊並掃描 Azure Data Lake Storage Gen1

本文概述如何在 Azure 範疇中註冊 Azure Data Lake Storage Gen1 作為資料來源，並在其上設定掃描。

> [!Note]
> Azure Data Lake Storage Gen2 現已全面上市。 我們建議您現在就開始使用。 如需詳細資訊，請參閱[產品頁面](https://azure.microsoft.com/services/storage/data-lake-storage/)。

## <a name="supported-capabilities"></a>支援的功能

Azure Data Lake Storage Gen1 的資料來源支援下列功能：

- 在 Azure Data Lake Storage Gen1 中捕捉中繼資料和分類的 **完整和增量掃描**

- ADF 複製/資料流程活動的資料 **資產之間的** 歷程

## <a name="prerequisites"></a>必要條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
- 您必須是 Azure 範疇資料來源管理員

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描驗證

以下是支援 Azure Data Lake Storage Gen1 的驗證方法：

- 受控識別
- 服務主體

### <a name="managed-identity-recommended"></a> (建議的受控識別) 

為了方便和安全性，您可能會想要使用範疇 MSI 來驗證您的資料存放區。

若要使用資料目錄的 MSI 設定掃描，您必須先授與範疇帳戶掃描資料來源的許可權。 您必須在設定掃描 *之前* 完成此步驟，否則您的掃描將會失敗。

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>將資料目錄 MSI 新增至 Azure Data Lake Storage Gen1 帳戶

您可以在訂用帳戶、資源群組或資源層級新增目錄的 MSI，視您想要讓其擁有掃描許可權的內容而定。

> [!Note]
> 您必須是訂用帳戶的擁有者，才能夠在 Azure 資源上新增受控識別。

1. 從 [Azure 入口網站](https://portal.azure.com)中，尋找訂用帳戶、資源群組或資源 (例如，您想要允許目錄掃描的 Azure Data Lake Storage Gen1 儲存體帳戶) 。

2. 按一下 **[總覽**]，然後選取 [**資料瀏覽器**]

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="選擇存取控制":::

3. 按一下頂端導覽上的 [ **存取** ]

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="按一下 [存取]":::

4. 按一下 [新增]。 在選取的使用者或群組選取專案中新增 **範疇目錄** 。 選取 [ **讀取** ] 和 [ **執行** ] 許可權。 請務必選擇 [新增至] 選項中的 **這個資料夾和所有子** 系，如下列螢幕擷取畫面所示，然後按一下 **[確定** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI 驗證詳細資料":::]。

5. 如果您的金鑰保存庫尚未連線到範疇，您將需要 [建立新的金鑰保存庫連接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最後，使用服務主體[建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描
> [!Note]
> 當您在資料來源上新增目錄的 MSI 之後，請等候最多15分鐘的時間，才能在設定掃描之前傳播許可權。

大約15分鐘之後，目錄應該具有適當的許可權，才能掃描資源 (s) 。

### <a name="service-principal"></a>服務主體

若要使用服務主體，您必須先依照下列步驟建立一個：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

2. 從左側功能表中，選取 [Azure Active Directory]。

3. 選取 **應用程式註冊**。

4. 選取 [+新增應用程式註冊]。

5. 輸入 **應用程式** 的名稱 (服務主體名稱)。

6. 選取 [僅此組織目錄中的帳戶]。

7. 針對 [重新 **導向 URI** ] 選取 [ **Web** ]，然後輸入您想要的任何 URL;它不一定是真正的或工作。

8. 然後，選取 [註冊]。

9. 從顯示名稱和應用程式識別碼複製值。

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>將資料目錄服務主體新增至 Azure Data Lake Storage Gen1 帳戶
1. 從 [Azure 入口網站](https://portal.azure.com)中，尋找訂用帳戶、資源群組或資源 (例如，您想要允許目錄掃描的 Azure Data Lake Storage Gen1 儲存體帳戶) 。

2. 按一下 **[總覽**]，然後選取 [**資料瀏覽器**]

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="選擇存取控制":::

3. 按一下頂端導覽上的 [ **存取** ]

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="按一下 [存取]":::

4. 按一下 [新增]。 將 **服務主體應用程式** 新增至選取的使用者或群組選取專案。 選取 [ **讀取** ] 和 [ **執行** ] 許可權。 請務必選擇 [新增至] 選項中的 **這個資料夾和所有子** 系，如下列螢幕擷取畫面所示，然後按一下 **[確定** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="服務主體驗證詳細資料":::]。

5. 如果您的金鑰保存庫尚未連線到範疇，您將需要 [建立新的金鑰保存庫連接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最後，使用服務主體 [建立新的認證](manage-credentials.md#create-a-new-credential) ，以設定您的掃描。

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>註冊 Azure Data Lake Storage Gen1 資料來源

若要在您的資料目錄中註冊新的 ADLS Gen1 帳戶，請執行下列動作：

1. 流覽至您的範疇資料目錄。
2. 選取左側導覽列上的 [ **來源** ]。
3. 選取 [註冊]
4. 在 [ **註冊來源**] 上，選取 [ **Azure Data Lake Storage Gen1**]。 
5. 選取 [繼續]。

在 [註冊來源 (] Azure Data Lake Storage Gen1) 畫面上，執行下列動作：

1. 輸入會在目錄中列出的資料來源 **名稱**。
2. 選擇您的訂用帳戶以篩選出儲存體帳戶
3. 選取儲存體帳戶
4. 選取集合或建立新的集合 (選擇性)
5. 完成以註冊資料來源。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)
