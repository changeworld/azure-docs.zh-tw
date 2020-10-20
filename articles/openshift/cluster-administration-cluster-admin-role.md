---
title: Azure Red Hat OpenShift 叢集系統管理員角色 |Microsoft Docs
description: Azure Red Hat OpenShift cluster administrator 角色的指派和使用方式
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f8b84461cc96c64ce2f97a8b4bb69246f5a0dccc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221174"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客戶系統管理員角色

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 將于2022年6月30日淘汰。 支援建立新的 Azure Red Hat OpenShift 3.11 叢集會繼續到2020年11月30日。 淘汰之後，剩餘的 Azure Red Hat OpenShift 3.11 叢集將會關閉以防止安全性弱點。
> 
> 遵循本指南來 [建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md)叢集。
> 如果您有特定問題， [請洽詢我們](mailto:arofeedback@microsoft.com)。

您是 Azure Red Hat OpenShift 叢集的叢集系統管理員。 您的帳戶對所有使用者建立的專案都有提高的許可權和存取權。

當您的帳戶已系結客戶-系統管理員叢集授權角色時，就可以自動管理專案。

> [!Note] 
> 客戶-管理叢集的叢集角色與叢集管理員叢集角色不同。

例如，您可以執行與一組動詞命令相關聯的動作， (`create`) 以 () 的一組資源名稱操作 `templates` 。 若要查看這些角色及其動詞和資源集合的詳細資料，請執行下列命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

動詞名稱不一定會直接對應至 `oc` 命令。 它們通常等同于您可以執行的 CLI 作業類型。 

例如，擁有 `list` 動詞表示您可以顯示資源名稱的所有物件清單 (`oc get`) 。 `get`動詞命令表示如果您知道特定物件的名稱 () ，就可以顯示特定物件的詳細資料 `oc describe` 。

## <a name="configure-the-customer-administrator-role"></a>設定客戶系統管理員角色

您只能藉由提供旗標，在叢集建立期間設定客戶-系統管理員叢集叢集角色 `--customer-admin-group-id` 。 此欄位目前無法在 Azure 入口網站中設定。 若要瞭解如何設定 Azure Active Directory 和系統管理員群組，請參閱 [Azure Red Hat 的 Azure Active Directory 整合 OpenShift](howto-aad-app-configuration.md)。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>確認客戶系統管理員角色中的成員資格

若要確認客戶管理群組中的成員資格，請嘗試 OpenShift CLI 命令 `oc get nodes` 或 `oc projects` 。 `oc get nodes` 如果您具有客戶-系統管理員叢集角色，則會顯示節點清單，如果您只有客戶-系統管理員-專案角色，則會顯示許可權錯誤。 `oc projects` 會顯示叢集中的所有專案，而不是只顯示您正在使用的專案。

若要進一步探索叢集中的角色和許可權，您可以使用 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) 命令。

## <a name="next-steps"></a>後續步驟

設定客戶-管理叢集叢集角色：
> [!div class="nextstepaction"]
> [適用于 Azure Red Hat OpenShift 的 Azure Active Directory 整合](howto-aad-app-configuration.md)
