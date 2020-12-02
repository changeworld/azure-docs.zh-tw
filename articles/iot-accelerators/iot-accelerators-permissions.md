---
title: 使用 Azure IoT 解決方案網站 - Azure | Microsoft Docs
description: 說明如何使用 AzureIoTSolutions.com 網站來部署解決方案加速器。
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 6585718a549c0e0a6eac1ab50788798e1021fef9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446808"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>使用 AzureIoTSolutions.com 網站部署解決方案加速器

您可以從 [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)，將 Azure IoT 解決方案加速器部署至 Azure 訂用帳戶。 AzureIoTSolutions.com 會同時裝載 Microsoft 開放原始碼和合作夥伴解決方案加速器。 這些解決方案加速器會與 [Azure IoT 參考架構](/azure/architecture/reference-architectures/iot) \(英文\) 一致。 您可以使用該網站，將解決方案加速器快速部署為示範或生產環境。

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> 如果您需要更充分掌控部署程序，可以使用 [CLI 來部署解決方案加速器](iot-accelerators-remote-monitoring-deploy-cli.md)。

您可以在下列設定中部署解決方案加速器：

* **標準**：用於開發生產環境的擴展基礎結構部署。 Azure Container Service 會將微服務部署到多個 Azure 虛擬機器。 Kubernetes 會協調裝載個別微服務的 Docker 容器。
* **基本**：適用于示範或測試部署的成本較低版本。 所有微服務都將部署到單一 Azure 虛擬機器。
* **Local**：用於測試和開發的本機電腦部署。 這種方法會將微服務部署到本機 Docker 容器，並連線到 IoT 中樞、Azure Cosmos DB，以及雲端中的 Azure 儲存體服務。

每個解決方案加速器都會使用不同的 Azure 服務 (例如 IoT 中樞、Azure 串流分析及 Cosmos DB) 組合。 如需詳細資訊，請瀏覽 [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) 並選取解決方案加速器。

## <a name="sign-in-at-azureiotsolutionscom"></a>在 azureiotsolutions.com 上登入

您必須先在 AzureIoTSolutions.com 上使用與 Azure 訂用帳戶相關聯的認證登入，然後才能部署解決方案加速器。 如果您的帳戶與一個以上的 Microsoft Azure Active Directory (AD) 租用戶相關聯，您可以使用 **帳戶選取項目下拉式清單** 來選擇要使用的目錄。

您用以部署解決方案加速器、管理使用者及管理 Azure 服務的權限，取決於您在所選取目錄中的角色。 與解決方案加速器相關聯的常見 Azure AD 角色包括：

* **全域管理員**：每個 Azure AD 租使用者可以有許多 [全域管理員](../active-directory/roles/permissions-reference.md) ：

  * 當您建立 Azure AD 租用戶時，您預設會是該租用戶的全域管理員。
  * 全域管理員可以部署基本和標準的解決方案加速器。

* **網域使用者**：每個 Azure AD 租使用者可以有許多網域使用者。 網域使用者可以部署基本的解決方案加速器。

* **來賓使用者**：每個 Azure AD 租使用者可以有許多來賓使用者。 來賓使用者無法在 Azure AD 租用戶中部署解決方案加速器。

如需 Azure AD 中使用者和角色的詳細資訊，請參閱下列資源：

* [在 Azure Active Directory 中建立使用者](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [將使用者指派給應用程式](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>選擇您的裝置

AzureIoTSolutions.com 網站會連結至 [Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)。

此目錄會列出數百個認證的 IoT 硬體裝置，您可以將這些裝置連線到解決方案加速器，以開始建置您的 IoT 解決方案。

![裝置目錄](media/iot-accelerators-permissions/devicecatalog.png)

如果您是硬體製造商，請按一下 [成為夥伴]，以了解如何在 IoT 認證方案中與 Microsoft 成為夥伴。

## <a name="next-steps"></a>後續步驟

若要試用其中一個 IoT 解決方案加速器，請參閱快速入門：

* [試用遠端監視解決方案](quickstart-remote-monitoring-deploy.md)
* [試用連線工廠解決方案](quickstart-connected-factory-deploy.md)
* [試用預測性維護解決方案](quickstart-predictive-maintenance-deploy.md)
* [試用裝置模擬解決方案](quickstart-device-simulation-deploy.md)