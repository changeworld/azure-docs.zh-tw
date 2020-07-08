---
title: 針對 Azure Red Hat OpenShift 進行疑難排解
description: 針對 Azure Red Hat OpenShift 的常見問題進行疑難排解並加以解決
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76274919"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的疑難排解

本文詳細說明建立或管理 Microsoft Azure Red Hat OpenShift 叢集時所遇到的一些常見問題。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重試建立失敗的叢集

如果使用 CLI 命令建立 Azure Red Hat OpenShift 叢集 `az` 失敗，則重試建立會繼續失敗。
使用 `az openshift delete` 刪除失敗的叢集，然後建立全新的叢集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隱藏的 Azure Red Hat OpenShift 叢集資源群組

目前， `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI （命令）自動建立的資源 `az openshift create` 會在 Azure 入口網站中隱藏。 在**資源群組**視圖中，選取 [**顯示隱藏的類型**] 以查看資源群組。

![入口網站中 [隱藏類型] 核取方塊的螢幕擷取畫面](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>建立叢集會導致找不到已註冊的資源提供者時發生錯誤

如果建立叢集導致錯誤 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` ，表示您是預覽的一部分，現在需要[購買 Azure 虛擬機器保留實例](https://aka.ms/openshift/buy)，才能使用正式推出的產品。 保留會藉由預先支付完全受控的 Azure 服務，來減少您的支出。 請參閱[*什麼是 Azure 保留*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)專案，以深入瞭解保留專案，以及如何為您節省費用。

## <a name="next-steps"></a>後續步驟

- 請嘗試[Red Hat OpenShift 說明中心](https://help.openshift.com/)，以取得 OpenShift 疑難排解的詳細資訊。

- 尋找[關於 Azure Red Hat OpenShift 常見問題](openshift-faq.md)的解答。
