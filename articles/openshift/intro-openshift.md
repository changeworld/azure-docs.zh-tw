---
title: Azure Red Hat OpenShift 簡介
description: 了解 Microsoft Azure Red Hat OpenShift 的功能與優點，以部署及管理容器型應用程式。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "82628515"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* 服務可讓您部署完全受控的 [OpenShift](https://www.openshift.com/) 叢集。

Azure Red Hat OpenShift 可擴充 [Kubernetes](https://kubernetes.io/)。 使用 Kubernetes 在生產環境中執行容器需要額外的工具和資源。 這通常包括需要調整映像登錄、儲存體管理、網路解決方案，以及記錄和監視工具，這些都必須一起進行版本設定和測試。 要建置容器型應用程式，則需進一步進行與中介軟體、架構、資料庫和 CI/CD 工具的整合工作。 Azure Red Hat OpenShift 將其全數結合到單一平台中，不僅讓 IT 小組的作業更為簡便，同時也為應用程式小組提供他們必須執行的功能。

Azure Red Hat OpenShift 由 Red Hat 與 Microsoft 共同設計、運作和支援，以提供整合的支援體驗。 無須執行任何虛擬機器，也不需要修補。 Microsoft 和 Red Hat 會為您修補、更新及監控主要節點、基礎結構和應用程式節點。 Azure Red Hat OpenShift 叢集會部署到您的 Azure 訂用帳戶中，並且包含在您的 Azure 帳單上。

您可以自行選擇登錄、網路功能、儲存體和 CI/CD 解決方案，或使用內建解決方案來進行自動化原始程式碼管理、容器和應用程式建置、部署、調整、健康情況管理等功能。 Azure Red Hat OpenShift 可透過 Azure Active Directory 提供整合式登入體驗。

若要開始使用，請完成[建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

## <a name="access-security-and-monitoring"></a>存取、安全性和監視

為了提升安全性和管理能力，Azure Red Hat OpenShift 可讓您與 Azure Active Directory (Azure AD) 整合，並使用 Kubernetes 角色型存取控制 (RBAC)。 您也可以監視叢集與資源的健康情況。

## <a name="cluster-and-node"></a>叢集與節點

Azure Red Hat OpenShift 節點執行於 Azure 虛擬機器上。 您可以將儲存體連線到節點和 Pod 並升級叢集元件。

## <a name="next-steps"></a>後續步驟

了解 Azure Red Hat OpenShift 的必要條件：

> [!div class="nextstepaction"]
> [設定開發環境](tutorial-create-cluster.md)
