---
title: Azure 紅帽開放移位群集管理員角色 |微軟文檔
description: Azure 紅帽開放移位群集管理員角色的分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139091"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure 紅帽開放轉移客戶管理員角色

您是 Azure 紅帽開放Shift 群集的群集管理員。 您的帳戶具有對所有使用者創建專案的許可權和存取權限。

當您的帳戶將客戶管理員群集授權角色綁定到該角色時，它可以自動管理專案。

> [!Note] 
> 客戶-管理員群集群集角色與群集管理員群集角色不同。

例如，可以執行與一組謂詞 （`create`） 關聯的操作，以對一組資源名稱 （`templates`） 進行操作。 要查看這些角色及其謂詞和資源集的詳細資訊，請運行以下命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

動詞名稱不一定全部直接映射到`oc`命令。 它們更一般地等同于您可以執行的 CLI 操作類型。 

例如，`list`使用謂詞意味著可以顯示資源名稱 （ 的所有`oc get`物件的清單）。 該`get`謂詞表示，如果知道特定物件的名稱 （），`oc describe`則可以顯示其詳細資訊。

## <a name="configure-the-customer-administrator-role"></a>配置客戶管理員角色

只能在群集創建期間通過提供標誌`--customer-admin-group-id`來配置客戶管理員群集群集角色。 此欄位當前無法在 Azure 門戶中配置。 要瞭解如何配置 Azure 活動目錄和管理員組，請參閱[Azure 紅帽 OpenShift 的 Azure 活動目錄集成](howto-aad-app-configuration.md)。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>確認客戶管理員角色的成員身份

要確認您在客戶管理組中的成員資格，請嘗試 OpenShift CLI`oc get nodes`命令`oc projects`或 。 `oc get nodes`如果具有客戶管理員群集角色，將顯示節點清單;如果只有客戶管理員-專案角色，則顯示許可權錯誤。 `oc projects`將顯示群集中的所有專案，而不是只顯示您正在處理的專案。

要進一步流覽群集中的角色和許可權，可以使用 該[`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings)命令。

## <a name="next-steps"></a>後續步驟

配置客戶管理員群集角色：
> [!div class="nextstepaction"]
> [Azure 紅帽開放移位的 Azure 活動目錄集成](howto-aad-app-configuration.md)
