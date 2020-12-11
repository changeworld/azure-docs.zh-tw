---
title: 在 Azure Marketplace 中尋找租使用者識別碼、物件識別碼和合作夥伴關聯詳細資料
description: 如何在 Azure Marketplace 中尋找訂用帳戶識別碼的租使用者識別碼、物件識別碼和合作夥伴關聯詳細資料。
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109331"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>尋找租使用者識別碼、物件識別碼和合作夥伴關聯詳細資料

本文說明如何尋找租使用者識別碼、物件識別碼和合作夥伴關聯詳細資料，以及其各自的訂用帳戶識別碼。

如果您需要在 Azure Cloud Shell 中取得這些專案的螢幕擷取畫面，以用於偵錯工具的協助，請向下跳到 [尋找要進行偵錯工具的租使用者、物件和合作夥伴 ID 關聯](#find-ids-for-debugging)。

>[!Note]
> 只有訂用帳戶的擁有者具有執行這些步驟的許可權。

## <a name="find-tenant-id"></a>尋找租使用者識別碼

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 選取 **Azure Active Directory**。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure 入口網站中的 Azure Active Directory 圖示。":::

3. 選取 [概觀]。 您的租使用者識別碼應該會出現在 [ **基本資訊**] 底下。

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="選取 Azure 入口網站中的群組。":::

## <a name="find-subscriptions-and-roles"></a>尋找訂閱和角色

1. 移至 Azure 入口網站，然後選取 **Azure Active Directory** ，如上面的步驟1和2所述。
2. 選取 **訂用帳戶** 。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure 入口網站中的訂閱圖示。":::

3. 查看訂用帳戶和角色。

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure 入口網站中的 [訂閱] 畫面。":::

## <a name="find-partner-id"></a>尋找合作夥伴 ID

1. 流覽至 [訂閱] 頁面，如上一節所述。
2. 選取一個訂用帳戶。
3. 在 [ **帳單**] 下方選取 [ **合作夥伴資訊**]。

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="左側導覽功能表中的合作夥伴資訊。":::

## <a name="find-user-object-id"></a>尋找使用者 (物件識別碼) 

1. 使用適當的系統管理許可權，以所需租使用者中的帳戶登入 [Office 365 系統管理員入口網站](https://portal.office.com/adminportal/home) 。
2. 在左側功能表中，展開底部的 [系統 **管理中心** ] 區段，然後選取 [Azure Active Directory] 選項，以在新的瀏覽器視窗中啟動管理主控台。
3. 選取 [使用者]。
4. 流覽或搜尋所需的使用者，然後選取帳戶名稱以查看使用者帳戶的設定檔資訊。
5. 物件識別碼位於右邊的 [身分識別] 區段中。

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory 系統管理中心。":::

6. 在左側功能表中選取 [**存取控制] (IAM)** ，然後選取 [**角色指派**]，以尋找 **角色指派**。

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="適用于 Azure 資源的角色指派。":::

## <a name="find-ids-for-debugging"></a>尋找要進行偵錯工具的識別碼

本節說明如何尋找租使用者、物件和合作夥伴識別碼關聯以進行偵測。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 選取右上方的 PowerShell 圖示來開啟 Azure Cloud Shell。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="螢幕右上方的 PowerShell 圖示。":::

3. 選取 [ **PowerShell**]。

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="選取 PowerShell 連結。":::

4. 選取 [ **訂** 用帳戶] 方塊，選擇要連結夥伴的訂用帳戶，然後 **建立儲存體**。 這是一次性的動作;如果您已經設定儲存體，請繼續進行下一個步驟。

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="選取 [建立儲存體] 按鈕。":::

5. 建立 (或已經有) 儲存體會開啟 Azure Cloud Shell 視窗。

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Cloud Shell 視窗。":::

6. 如需合作夥伴關聯的詳細資料，請使用下列命令來安裝擴充功能：<br>`az extension add --name managementpartner`.<br>如果已安裝擴充功能，Azure Cloud Shell 將會注意：

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="顯示延伸模組的 [Azure Cloud Shell] 視窗已經安裝。":::

7. 使用下列命令檢查合作夥伴詳細資料：<br>`az managementpartner show --partner-id xxxxxx`<br>範例： `az managementpartner show --partner-id 4760962`.<br>貼上命令結果的螢幕擷取畫面，看起來會像這樣：

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="顯示先前命令的結果以查看合作夥伴識別碼的範例畫面。":::

>[!NOTE]
>如果多個訂用帳戶需要螢幕擷取畫面，請使用此命令在訂用帳戶之間切換：<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>後續步驟

- [支援的國家/區域](sell-from-countries.md)