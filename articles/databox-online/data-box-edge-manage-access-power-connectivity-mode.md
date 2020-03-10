---
title: Azure Data Box Edge 裝置存取、電源和連線模式 |Microsoft Docs
description: 說明如何管理可協助將資料傳輸至 Azure 之 Azure Data Box Edge 裝置的存取、電源和連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 7ce4b9dda853e63e427757317abc2f7c878ba3a4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384705"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>管理 Azure Data Box Edge 的存取、電源和連線模式

本文說明如何管理 Azure Data Box Edge 的存取、電源和連線模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 管理裝置存取
> * 管理連線模式
> * 管理電源


## <a name="manage-device-access"></a>管理裝置存取

您 Data Box Edge 裝置的存取權是使用裝置密碼來控制。 您可以透過本機 web UI 變更密碼。 您也可以在 Azure 入口網站中重設裝置密碼。

### <a name="change-device-password"></a>變更裝置密碼

請在本機 UI 中執行下列步驟，以變更裝置密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 選取 [變更密碼]。
 
### <a name="reset-device-password"></a>重設裝置密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 選取 [**重設**]。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理資源存取

若要建立您的 Data Box Edge/Data Box Gateway、IoT 中樞和 Azure 儲存體資源，您需要在資源群組層級以參與者或更高的許可權來進行。 您也需要註冊對應的資源提供者。 對於涉及啟用金鑰和認證的任何作業，也需要 Azure Active Directory 圖形 API 的許可權。 下列各節將說明這些情況。

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>管理 Microsoft Azure Active Directory Graph API 許可權

產生 Data Box Edge 裝置的啟用金鑰，或執行任何需要認證的作業時，您需要 Azure Active Directory 圖形 API 的許可權。 需要認證的作業可以是：

-  使用相關聯的儲存體帳戶建立共用。
-  建立可存取裝置上共用的使用者。

您應該可以在 Active Directory 租使用者上擁有 `User` 的存取權，因為您需要能夠 `Read all directory objects`。 您不能是來賓使用者，因為他們沒有 `Read all directory objects`的許可權。 如果您是來賓，則作業（例如產生啟用金鑰、在 Data Box Edge 裝置上建立共用、建立使用者、Edge 計算角色設定、重設裝置密碼）都會失敗。

如需有關如何提供使用者存取權給 Azure Active Directory 圖形 API 的詳細資訊，請參閱系統[管理員、使用者和來賓使用者的預設存取權](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。

### <a name="register-resource-providers"></a>註冊資源提供者

若要在 Azure 中布建資源（在 Azure Resource Manager 模型中），您需要可支援建立該資源的資源提供者。 例如，若要布建虛擬機器，您應該在訂用帳戶中有一個可用的「Microsoft 計算」資源提供者。
 
資源提供者會在訂用帳戶層級上註冊。 根據預設，任何新的 Azure 訂用帳戶都會預先註冊到常用的一組資源提供者。 ' DataBoxEdge ' 的資源提供者並未包含在此清單中。

您不需要將存取權限授與訂用帳戶層級，使用者就能夠在其擁有擁有權的資源群組中建立像是 ' DataBoxEdge ' 的資源，只要這些資源的資源提供者已存在經過.

在您嘗試建立任何資源之前，請確定已在訂用帳戶中註冊資源提供者。 如果資源提供者未註冊，您必須確定建立新資源的使用者有足夠的許可權可在訂用帳戶層級上註冊所需的資源提供者。 如果您還沒有這麼做，則會看到下列錯誤：

*訂用帳戶 \<訂用帳戶名稱 > 沒有註冊資源提供者的許可權： DataBoxEdge。*


若要取得目前訂用帳戶中已註冊的資源提供者清單，請執行下列命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

針對 Data Box Edge 裝置，應該註冊 `Microsoft.DataBoxEdge`。 若要註冊 `Microsoft.DataBoxEdge`，訂用帳戶管理員應執行下列命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

如需如何註冊資源提供者的詳細資訊，請參閱[解決資源提供者註冊的錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)。

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設的完全連線模式以外，您的裝置也可以在部分連線或完全離線的模式下執行。 每種模式的說明如下：

- **完全連接**-這是裝置運作的一般預設模式。 雲端上傳和下載的資料都會在此模式中啟用。 您可以使用 Azure 入口網站或本機 web UI 來管理裝置。

- **部分中斷**連線–在此模式中，裝置無法上傳或下載任何共用資料，不過可以透過 Azure 入口網站來管理。

    此模式通常使用於計量付費的衛星網路，其目標在於減少網路頻寬使用量。 裝置監視作業仍可能需要最低網路使用量。

- **已中斷連線** – 在此模式中，裝置完全與雲端中斷連線，且雲端上傳和下載皆已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]。
2. 從下拉式清單中，選取您想要在其中操作裝置的模式。 您可以選取 [**完全連接**]、[**部分連線**] 和 [**完全中斷**連線]。 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]。

    ![連線模式](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理電源

您可以使用本機 web UI 關閉或重新開機您的實體裝置。 建議您在重新開機之前，先將資料伺服器上的共用離線，然後再讓裝置離線。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 選取 [**關機**] 或 [**重新開機**]，視您想要執行的動作而定。

    ![電源設定](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 當系統提示您確認時，請選取 **[是]** 繼續進行。

> [!NOTE]
> 如果您關閉實體裝置，就必須按下裝置上的電源按鈕來開啟它。

## <a name="next-steps"></a>後續步驟

- 了解如何[管理共用](data-box-edge-manage-shares.md)。
