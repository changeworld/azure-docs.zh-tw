---
title: Azure 資料盒閘道設備訪問、電源和連接模式
description: 說明如何針對有助於將資料傳輸至 Azure 的 Azure 資料箱閘道裝置管理存取、電源和連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474436"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>管理 Azure 資料盒閘道的訪問、電源和連接模式

本文說明如何管理 Azure 資料箱閘道的存取、電源和連線模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 管理裝置存取
> * 管理連線模式
> * 管理電源

## <a name="manage-device-access"></a>管理裝置存取

對資料盒閘道設備的訪問由使用裝置密碼控制。 您可以通過本地 Web UI 更改密碼。 您還可以在 Azure 門戶中重置裝置密碼。

### <a name="change-device-password"></a>變更裝置密碼

按照本地 UI 中的這些步驟更改裝置密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]****。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. 按一下 [變更密碼]****。
 
### <a name="reset-device-password"></a>重置裝置密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]****。

    ![重設密碼](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 按一下 [重設]****。

    ![重設密碼](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理資源存取

要創建資料框邊緣/資料框閘道、IoT 中心和 Azure 存儲資源，需要在資源組級別作為參與者或更高版本的許可權。 您還需要註冊相應的資來源提供者。 對於涉及啟用金鑰和憑據的任何操作，還需要對 Azure 活動目錄圖形 API 的許可權。 這些在以下各節仲介紹。

### <a name="manage-microsoft-graph-api-permissions"></a>管理 Microsoft 圖形 API 許可權

為數據框邊緣設備生成啟用金鑰或執行任何需要憑據的操作時，您需要對 Microsoft 圖形 API 的許可權。 需要憑據的操作可以是：

-  使用關聯的存儲帳戶創建共用。
-  創建可以訪問設備上共用的使用者。

您應該具有對活動`User`目錄租戶的存取權限，因為您需要能夠`Read all directory objects`。 不能是來賓使用者，因為他們沒有 許可權`Read all directory objects`。 如果您是來賓，則產生啟用金鑰、在 Data Box Edge 裝置上建立共用或建立使用者等作業都將會失敗。

有關如何向使用者提供對 Microsoft 圖形 API 的訪問的詳細資訊，請參閱[Microsoft 圖形許可權引用](https://docs.microsoft.com/graph/permissions-reference)。

### <a name="register-resource-providers"></a>註冊資源提供者

要在 Azure 中預配資源（在 Azure 資源管理器模型中），需要支援創建該資源的資來源提供者。 例如，要預配虛擬機器，應在訂閱中提供"Microsoft.Compute"資來源提供者。
 
資源提供者會在訂用帳戶層級上註冊。 根據預設，任何新的 Azure 訂用帳戶都會預先註冊到常用的一組資源提供者。 此清單中不包括"Microsoft.DataBoxEdge"的資來源提供者。

只要這些資源的資源供應商已經擁有擁有者許可權，使用者就無需向訂閱級別授予存取權限，即可在其資源組中創建"Microsoft.DataBoxEdge"等資源。註冊。

在嘗試創建任何資源之前，請確保資來源提供者已註冊到訂閱中。 如果未註冊資來源提供者，則需要確保創建新資源的使用者有足夠的許可權在訂閱級別上註冊所需的資來源提供者。 如果您尚未這樣做，您將看到以下錯誤：

*訂閱\<訂閱名稱>無權註冊資來源提供者：Microsoft.DataBoxEdge。*


要獲取當前訂閱中已註冊資來源提供者的清單，運行以下命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

對於資料框邊緣設備，`Microsoft.DataBoxEdge`應註冊。 要註冊`Microsoft.DataBoxEdge`，訂閱管理員應運行以下命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

有關如何註冊資來源提供者的詳細資訊，請參閱[解決資來源提供者註冊的錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)。

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設標準模式，您的裝置也可以在已部分中斷連線或已中斷連線的模式中執行。 每種模式的說明如下：

- **已部分中斷連線** – 在此模式中，裝置無法將任何資料上傳到共用，不過可透過 Azure 入口網站加以管理。

    此模式通常使用於計量付費的衛星網路，其目標在於減少網路頻寬使用量。 裝置監視作業仍可能需要最低網路使用量。

- **已中斷連線** – 在此模式中，裝置完全與雲端中斷連線，且雲端上傳和下載皆已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]****。
2. 停用 [雲端上傳和下載連結]****。
3. 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]****。

    ![連線模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 若要在已中斷連線的模式中執行裝置，請停用 [Azure 入口網站管理]****。 裝置現在只能透過本機 Web UI 管理。

    ![連線模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>管理電源

您可以使用本機 Web UI 關閉或重新啟動您的虛擬裝置。 我們建議在重新開機之前，先讓主機上的共用離線，然後再讓裝置離線。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]****。
2. 視您想要進行的動作而定，按一下 [關機]**** 或 [重新啟動]****。

    ![電源設定](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 系統提示您進行確認時，按一下 [是]**** 以繼續。

> [!NOTE]
> 如果您關閉虛擬裝置，則必須透過 Hypervisor 管理來啟動裝置。
