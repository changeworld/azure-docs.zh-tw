---
title: 針對 Azure Red Hat OpenShift 進行疑難排解
description: 針對 Azure Red Hat OpenShift 的常見問題進行疑難排解並解決其問題
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 55360ef295ff80b700b059d053203458f9f384db
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469077"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>針對 Azure Red Hat OpenShift 進行疑難排解

本文將詳細說明建立或管理 Microsoft Azure Red Hat OpenShift 叢集時所遇到的一些常見問題。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重試建立失敗的叢集

如果使用 CLI 命令建立 Azure Red Hat OpenShift 叢集 `az` 失敗，則重試建立將會繼續失敗。
使用 `az openshift delete` 刪除失敗的叢集，然後建立全新的叢集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隱藏的 Azure Red Hat OpenShift 叢集資源群組

目前， `Microsoft.ContainerService/openShiftManagedClusters` Azure 入口網站中隱藏了 Azure CLI (命令) 自動建立的資源 `az openshift create` 。 在 **資源群組** 視圖中，選取 [ **顯示隱藏的類型** ] 以查看資源群組。

![入口網站中 [隱藏的類型] 核取方塊的螢幕擷取畫面](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>建立叢集會導致錯誤，指出找不到任何已註冊的資源提供者

如果建立叢集會導致錯誤 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` ，則您是預覽的一部分，現在需要 [購買 Azure 虛擬機器保留實例](https://aka.ms/openshift/buy) 以使用正式推出的產品。 保留可透過預先支付完全受控的 Azure 服務來減少您的支出。 請參閱 [*什麼是 Azure 保留*](../cost-management-billing/reservations/save-compute-costs-reservations.md) 專案，以深入瞭解保留專案，以及如何節省您的費用。

## <a name="next-steps"></a>接下來的步驟

- 請嘗試 [Red Hat OpenShift 說明中心](https://help.openshift.com/) ，以深入瞭解 OpenShift 的疑難排解。

- 尋找 [Azure Red Hat OpenShift 相關](openshift-faq.md)常見問題的解答。