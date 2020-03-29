---
title: 排除 Azure 紅帽打開移位故障
description: 使用 Azure 紅帽開放Shift 解決和解決常見問題
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274919"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure 紅帽打開移位的故障排除

本文詳細介紹了創建或管理 Microsoft Azure 紅帽 OpenShift 群集時遇到的一些常見問題。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重試創建失敗的群集

如果使用`az`CLI 命令創建 Azure 紅帽 OpenShift 群集失敗，則重試創建將繼續失敗。
用於`az openshift delete`刪除失敗的群集，然後創建一個全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隱藏的 Azure 紅帽開放移位群集資源組

目前，Azure `Microsoft.ContainerService/openShiftManagedClusters` CLI（`az openshift create`命令）自動創建的資源隱藏在 Azure 門戶中。 在 **"資源組"** 視圖中，選中 **"顯示隱藏類型**以查看資源組"。

![門戶中隱藏類型核取方塊的螢幕截圖](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>創建群集會導致錯誤，未找到註冊的資來源提供者

如果創建群集會導致錯誤，`No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`則您是預覽的一部分，現在需要購買 Azure[虛擬機器預留實例](https://aka.ms/openshift/buy)才能使用通常可用的產品。 預留通過為完全託管的 Azure 服務預付費用來減少支出。 請參閱[*什麼是 Azure 預留*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)，瞭解有關預訂及其如何為您節省資金的詳細資訊。

## <a name="next-steps"></a>後續步驟

- 有關 OpenShift 故障排除的更多問題，請嘗試[紅帽 OpenShift 説明中心](https://help.openshift.com/)。

- 查找有關[Azure 紅帽開放Shift](openshift-faq.md)的常見問題的解答。
