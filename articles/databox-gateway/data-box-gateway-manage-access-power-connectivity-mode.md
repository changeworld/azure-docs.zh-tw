---
title: Azure 資料箱閘道裝置存取、電源和連線模式
description: 說明如何針對有助於將資料傳輸至 Azure 的 Azure 資料箱閘道裝置管理存取、電源和連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786802"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>管理 Azure 資料箱閘道的存取、電源和連線模式

本文說明如何管理 Azure 資料箱閘道的存取、電源和連線模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。 

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 管理裝置存取
> * 管理連線模式
> * 管理電源

## <a name="manage-device-access"></a>管理裝置存取

您的資料箱閘道裝置存取權是透過使用裝置密碼來控制。 您可以透過本機 web UI 來變更密碼。 您也可以在 Azure 入口網站中重設裝置密碼。

### <a name="change-device-password"></a>變更裝置密碼

請在本機 UI 中執行下列步驟，以變更裝置密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. 按一下 [變更密碼]。
 
### <a name="reset-device-password"></a>重設裝置密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]。

    ![重設密碼](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 按一下 [重設]。

    ![重設密碼2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理資源存取

若要建立 Azure 資料箱閘道、IoT 中樞和 Azure 儲存體資源，您需要有資源群組層級的參與者或更高許可權。 您也需要註冊對應的資源提供者。 對於任何涉及啟用金鑰和認證的作業，也需要 Azure Active Directory 圖形 API 的許可權。 這些將在下列各節中說明。

### <a name="manage-microsoft-graph-api-permissions"></a>管理 Microsoft Graph API 許可權

當產生裝置的啟用金鑰，或執行任何需要認證的作業時，您需要 Microsoft Graph API 的許可權。 需要認證的作業可以是：

-  使用相關聯的儲存體帳戶來建立共用。
-  建立可存取裝置上共用的使用者。

您應該可以 `User` 存取 Active Directory 租使用者，因此您可以 `Read all directory objects` 。 來賓使用者沒有許可權 `Read all directory objects` 。 如果您是來賓，則產生啟用金鑰、在裝置上建立共用以及建立使用者的作業將會失敗。

如需如何提供存取權給使用者 Microsoft Graph API 的詳細資訊，請參閱 [Microsoft Graph 許可權參考](/graph/permissions-reference)。

### <a name="register-resource-providers"></a>註冊資源提供者

若要在 Azure (的 Azure Resource Manager 模型) 中布建資源，您需要支援建立該資源的資源提供者。 例如，若要布建虛擬機器，訂用帳戶中應該有 ' Microsoft. Compute ' 資源提供者。
 
資源提供者會在訂用帳戶層級上註冊。 根據預設，任何新的 Azure 訂用帳戶都會預先註冊到常用的一組資源提供者。 此清單中未包含 ' Microsoft.databoxedge ' 的資源提供者。

只要這些資源的資源提供者已經註冊，您就不需要授與訂用帳戶層級的存取權限，讓使用者能夠在具有擁有者許可權的資源群組內建立像是 ' Microsoft.databoxedge ' 的資源。

在您嘗試建立任何資源之前，請確定已在訂用帳戶中註冊資源提供者。 如果資源提供者未註冊，您必須確定建立新資源的使用者有足夠的許可權，可在訂用帳戶層級註冊必要的資源提供者。 如果您還沒有這麼做，則會看到下列錯誤：

*訂 \<Subscription name> 用帳戶沒有註冊資源提供者的許可權 (s) ： microsoft.databoxedge。*


若要取得目前訂用帳戶中已註冊的資源提供者清單，請執行下列命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

針對資料箱閘道裝置， `Microsoft.DataBoxEdge` 應該註冊。 若要註冊 `Microsoft.DataBoxEdge` ，訂用帳戶管理員應該執行下列命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

如需如何註冊資源提供者的詳細資訊，請參閱 [解決資源提供者註冊的錯誤](../azure-resource-manager/templates/error-register-resource-provider.md)。

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設的標準模式之外，您的裝置也可以在部分中斷連線或已中斷連線的模式下執行：

- **部分中斷** 連線–在此模式中，裝置無法將任何資料上傳至共用。 不過，它可以透過 Azure 入口網站來管理。

    此模式通常用來將計量付費衛星網路上的網路頻寬耗用量降至最低。 裝置監視作業仍可能需要最低網路使用量。

- 已 **中斷** 連線–在此模式中，裝置已完全與雲端中斷連線，而雲端上傳和下載都已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]。
2. 停用 [雲端上傳和下載連結]。
3. 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]。

    ![連線模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 若要在已中斷連線的模式中執行裝置，請停用 [Azure 入口網站管理]。 裝置現在只能透過本機 Web UI 管理。

    ![連接模式2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>管理電源

您可以使用本機 Web UI 關閉或重新啟動您的虛擬裝置。 建議您在重新開機裝置之前，先讓主機上的共用離線，再讓裝置離線。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 按一下 [ **關機** ] 或 [ **重新開機** ]，視您想要做的動作而定。

    ![電源設定](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 系統提示您進行確認時，按一下 [是] 以繼續。

> [!NOTE]
> 如果您關閉虛擬裝置，則必須透過 Hypervisor 管理來啟動裝置。