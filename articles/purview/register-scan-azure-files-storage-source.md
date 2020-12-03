---
title: 如何掃描 Azure 檔案儲存體
description: 本操作指南說明如何掃描 Azure 檔案的詳細資料。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552189"
---
# <a name="register-and-scan-azure-files"></a>註冊並掃描 Azure 檔案儲存體

## <a name="supported-capabilities"></a>支援的功能

Azure 檔案儲存體支援完整和增量掃描，以根據系統和客戶分類來捕捉中繼資料，並對中繼資料套用分類。

## <a name="prerequisites"></a>先決條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
- 您必須是資料來源管理員才能設定和排程掃描，請參閱 [目錄許可權](catalog-permissions.md) 以取得詳細資料。

## <a name="register-an-azure-files-storage-account"></a>註冊 Azure 檔案儲存體儲存體帳戶

若要在您的資料目錄中註冊新的 Azure 檔案儲存體帳戶，請執行下列動作：

1. 流覽至您的範疇資料目錄。
1. 選取左側導覽列上的 [ **管理中心** ]。
1. 選取 [來源] 底下的 [ **資料來源** ] **和 [掃描**]。
1. 選取 [+ 新增]  。
1. 在 [ **註冊來源**] 上，選取 [ **Azure 檔案儲存體**]。 選取 [繼續]。

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="註冊新的資料來源" border="true":::

在 [ **註冊來源 (] Azure 檔案儲存體)** 畫面上，執行下列動作：

1. 輸入將在目錄中列出資料來源的 **名稱** 。
1. 選擇您想要指向所需儲存體帳戶的方式：
   1. 從 **azure 訂用** 帳戶選取，從 [ **azure 訂** 用帳戶] 下拉式方塊中選取適當的訂用帳戶，並從 [ **儲存體帳戶名稱** ] 下拉式方塊中選取適當的儲存體帳戶。
   1. 或者，您也可以選取 [ **手動輸入** ]，然後輸入 (URL) 的服務端點。
1. **完成** 以註冊資料來源。

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="註冊來源選項" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>設定掃描的驗證

若要使用帳戶金鑰設定 Azure 檔案儲存體儲存體的驗證，請執行下列動作：

1. 選取 [驗證方法] 作為 **帳戶金鑰**。
2. 選取 [ **從 Azure 訂用** 帳戶] 選項。
3. 挑選 Azure 檔案儲存體帳戶所在的 Azure 訂用帳戶。
4. 從清單中選取您的儲存體帳戶名稱。
5. 按一下 [完成] 。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>後續步驟

- [流覽 Azure 範疇資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)