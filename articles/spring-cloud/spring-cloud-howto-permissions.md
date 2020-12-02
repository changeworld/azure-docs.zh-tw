---
title: 如何-在 Azure 春季雲端中使用許可權
description: 本文說明如何在 Azure 春季雲端中建立許可權的自訂角色。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498637"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>如何在 Azure 春季雲端中使用許可權
本文說明如何建立自訂角色，以將許可權委派給 Azure 春季雲端資源。 自訂角色會以各種股票許可權擴充 [Azure 內建角色](../role-based-access-control/built-in-roles.md) 。

我們將會執行下列自訂角色：

* **開發人員角色**： 
    * 部署
    * 測試
    * 重新開機應用程式
    * 可以對 git 存放庫中的應用程式設定進行套用及變更
    * 可以取得記錄資料流程
* **Ops-網站可靠性工程**： 
    * 重新開機應用程式
    * 取得記錄資料流程
    * 無法對應用程式或設定進行變更
* **Azure Pipelines/Jenkins/Github 動作角色**：
    * 可以執行建立、讀取、更新、刪除作業
    * 可以使用 Terraform 或 ARM 範本，在 Azure 中的雲端和應用程式中建立及設定所有專案： Azure Pipelines、Jenkins 或 GitHub Actions

## <a name="define-developer-role"></a>定義開發人員角色

開發人員角色包含重新開機應用程式和查看其記錄資料流程的許可權，但無法變更應用程式、設定。

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>流覽訂用帳戶和資源群組的存取控制 (IAM) 

遵循下列步驟以開始定義角色。

1. 在 Azure 入口網站中，開啟您想要從中指派自訂角色的訂用帳戶和資源群組。
2. 開啟 [ **存取控制] (IAM)**。
3. 按一下 [+ 新增]。
4. 按一下 [ **新增自訂角色**]。
5. 按 [下一步]  。

   ![建立自訂角色](media/spring-cloud-permissions/create-custom-role.png)

6. 按一下 [新增權限]。

   ![新增許可權開始](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>搜尋 Azure 春季雲端許可權：
7. 在搜尋方塊中，搜尋 *Microsoft. app*。
選取 [ *Microsoft Azure 春季雲端*]。

   ![選取 Azure 春天雲端](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 選取開發人員角色的許可權：

從 **AppPlatform/春季** 選取：
* 寫入：建立或更新 Azure 春季雲端服務實例
* 讀取：取得 Azure 春季雲端服務實例
* 其他：列出 Azure 春季雲端服務實例的測試金鑰

從 **AppPlatform/春季/apps** 選取：
* 讀取：讀取 Microsoft Azure 春季雲端應用程式
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式資源上傳 URL

從 **AppPlatform/春季/apps/** 系結，選取：
* 讀取：讀取 Microsoft Azure 春季 Cloud application binding

從 **AppPlatform/春季/apps/部署** 中，選取：
* 寫入：撰寫 Microsoft Azure 春季雲端應用程式部署
* 讀取：讀取 Microsoft Azure 春季雲端應用程式部署
* 其他：開始 Microsoft Azure 春季雲端應用程式部署
* 其他：停止 Microsoft Azure 春季雲端應用程式部署
* 其他：重新開機 Microsoft Azure 春季雲端應用程式部署
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式部署記錄檔 URL

從 **AppPlatform/春季/apps/網域** 選取：
* 讀取：讀取 Microsoft Azure 春季 Cloud 應用程式自訂網域

從 **AppPlatform/春季/** certificate 選取：
* 讀取：讀取 Microsoft Azure 春季雲端憑證

從 **AppPlatform/地點/operationResults/春季** 選取：
* 讀取：讀取作業結果

從 **AppPlatform/位置/>operationstatus/operationId** 選取：
* 讀取：讀取作業狀態

    [![建立 Developler 許可權 ](media/spring-cloud-permissions/developer-permissions-box.png)](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. 按一下 [加入]  。

10. 檢查許可權。

11. 按一下 [檢閱及建立]。

## <a name="define-devops-engineer-role"></a>定義 DevOps 工程師角色
此程式會定義具有部署、測試及重新開機 Azure 春季雲端應用程式許可權的角色。

1. 重複此程式，以流覽訂用帳戶、資源群組，以及存取存取控制 (IAM) 。
2. 選取 DevOps 工程師角色的許可權：

從 **AppPlatform/春季** 選取：
* 寫入：建立或更新 Azure 春季雲端服務實例
* 刪除：刪除 Azure 春季雲端服務實例
* 讀取：取得 Azure 春季雲端服務實例
* 其他：啟用 Azure 春季雲端服務實例測試端點
* 其他：停用 Azure 春季雲端服務實例測試端點
* 其他：列出 Azure 春季雲端服務實例的測試金鑰
* 其他：重新產生 Azure 春季 Cloud 服務實例的測試金鑰

從 **AppPlatform/春季/apps** 選取：
* 寫入：撰寫 Microsoft Azure 的春季雲端應用程式
* 刪除：刪除 Microsoft Azure 的春季雲端應用程式
* 讀取：讀取 Microsoft Azure 春季雲端應用程式
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式資源上傳 URL
* 其他：驗證 Microsoft Azure 春季 Cloud 應用程式自訂網域

從 **AppPlatform/春季/apps/** 系結，選取：
* 寫入：寫入 Microsoft Azure 春季 Cloud 應用程式系結
* Delete：刪除 Microsoft Azure 春季 Cloud application binding
* 讀取：讀取 Microsoft Azure 春季 Cloud application binding

從 **AppPlatform/春季/apps/部署** 中，選取：
* 寫入：撰寫 Microsoft Azure 春季雲端應用程式部署
* 刪除：刪除 Azure 春季 Cloud 應用程式部署
* 讀取：讀取 Microsoft Azure 春季雲端應用程式部署
* 其他：開始 Microsoft Azure 春季雲端應用程式部署
* 其他：停止 Microsoft Azure 春季雲端應用程式部署
* 其他：重新開機 Microsoft Azure 春季雲端應用程式部署
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式部署記錄檔 URL

從 **AppPlatform/春季/apps/部署/sku** 選取：
* 讀取：列出應用程式部署可用的 sku

從 **AppPlatform/位置**，選取：
* 其他：檢查名稱可用性

從 Microsoft. AppPlatform/位置/operationResults/彈簧 select： Read： Read 作業結果

從 **AppPlatform/位置/>operationstatus/operationId** 選取：
* 讀取：讀取作業狀態

從 **AppPlatform/sku** 選取：
* 讀取：列出可用的 sku

   [![Dev/Op 許可權 ](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. 按一下 [加入]  。

4. 檢查許可權。

5. 按一下 [檢閱及建立]。

## <a name="define-ops---site-reliability-engineering-role"></a>定義 Ops-網站可靠性工程角色
此程式會定義具有部署、測試及重新開機 Azure 春季雲端應用程式許可權的角色。

1. 重複此程式，以流覽訂用帳戶、資源群組，以及存取存取控制 (IAM) 。

2. 選取 Ops-Site 可靠性工程角色的許可權：

從 **AppPlatform/春季** 選取：
* 讀取：取得 Azure 春季雲端服務實例
* 其他：列出 Azure 春季雲端服務實例的測試金鑰

從 **AppPlatform/春季/apps** 選取：
* 讀取：讀取 Microsoft Azure 春季雲端應用程式

從 **AppPlatform/apps/部署** 中，選取：
* 讀取：讀取 Microsoft Azure 春季雲端應用程式部署
* 其他：開始 Microsoft Azure 春季雲端應用程式部署
* 其他：停止 Microsoft Azure 春季雲端應用程式部署
* 其他：重新開機 Microsoft Azure 春季雲端應用程式部署

從 **AppPlatform/地點/operationResults/春季** 選取：
* 讀取：讀取作業結果

從 **AppPlatform/位置/>operationstatus/operationId** 選取：
* 讀取：讀取作業狀態

   [![Ops/SRE 許可權 ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. 按一下 [加入]  。

4. 檢查許可權。

5. 按一下 [檢閱及建立]。

## <a name="define-azure-pipelinesprovisioning-role"></a>定義 Azure Pipelines/布建角色
此 Jenkins/Github 動作角色可在 Azure 春季雲端和應用程式中使用服務實例來建立和設定所有專案。 此角色適用于發行或部署程式碼。

1. 重複此程式，以流覽訂用帳戶、資源群組，以及存取存取控制 (IAM) 。

2. 開啟 [ **許可權** ] 選項。

3. 選取 Azure Pipelines/布建角色的許可權：
  
從 **AppPlatform/春季** 選取：
* 寫入：建立或更新 Azure 春季雲端服務實例
* 刪除：刪除 Azure 春季雲端服務實例
* 讀取：取得 Azure 春季雲端服務實例
* 其他：啟用 Azure 春季雲端服務實例測試端點
* 其他：停用 Azure 春季雲端服務實例測試端點
* 其他：列出 Azure 春季雲端服務實例的測試金鑰
* 其他：重新產生 Azure 春季 Cloud 服務實例的測試金鑰

從 **AppPlatform/春季/apps** 選取：
* 寫入：撰寫 Microsoft Azure 的春季雲端應用程式
* 刪除：刪除 Microsoft Azure 的春季雲端應用程式
* 讀取：讀取 Microsoft Azure 春季雲端應用程式
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式資源上傳 URL
* 其他：驗證 Microsoft Azure 春季 Cloud 應用程式自訂網域

從 **AppPlatform/春季/apps/** 系結，選取：
* 寫入：寫入 Microsoft Azure 春季 Cloud 應用程式系結
* Delete：刪除 Microsoft Azure 春季 Cloud application binding
* 讀取：讀取 Microsoft Azure 春季 Cloud application binding

從 **AppPlatform/春季/apps/部署** 中，選取：
* 寫入：撰寫 Microsoft Azure 春季雲端應用程式部署
* 刪除：刪除 Azure 春季 Cloud 應用程式部署
* 讀取：讀取 Microsoft Azure 春季雲端應用程式部署
* 其他：開始 Microsoft Azure 春季雲端應用程式部署
* 其他：停止 Microsoft Azure 春季雲端應用程式部署
* 其他：重新開機 Microsoft Azure 春季雲端應用程式部署
* 其他：取得 Microsoft Azure 春季 Cloud 應用程式部署記錄檔 URL

從 **AppPlatform/sku** 選取：
* 讀取：列出可用的 sku

從 **AppPlatform/位置**，選取：
* 其他：檢查名稱可用性

從 **AppPlatform/地點/operationResults/春季** 選取：
* 讀取：讀取作業結果

從 **AppPlatform/位置/>operationstatus/operationId** 選取：
* 讀取：讀取作業狀態

從 **AppPlatform/sku** 選取：
* 讀取：列出可用的 sku

   [![管線許可權 ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. 按一下 [加入]  。

5. 檢查許可權。

6. 按一下 [檢閱及建立]。


## <a name="see-also"></a>另請參閱
* [使用 Azure 入口網站建立或更新 Azure 自訂角色](../role-based-access-control/custom-roles-portal.md) (機器翻譯)

如需定義自訂許可權的三種方法的詳細資訊，請參閱：
* [複製角色](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [從頭開始](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [從 JSON 啟動](../role-based-access-control/custom-roles-portal.md#start-from-json)