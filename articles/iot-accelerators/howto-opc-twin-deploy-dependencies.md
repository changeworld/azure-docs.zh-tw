---
title: 如何在 Azure 中部署 OPC 對應項雲端相依性 |Microsoft Docs
description: 本文說明如何部署進行本機開發和偵測所需的 OPC 對應項 Azure 相依性。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078981"
---
# <a name="deploying-dependencies-for-local-development"></a>部署本機開發的相依性

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

本文說明如何只部署進行本機開發和偵測所需的 Azure 平臺服務。   最後，您將會部署一個資源群組，其中包含本機開發和偵測所需的所有專案。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平臺服務

1. 請確定您已安裝 PowerShell 和 [AzureRM powershell](/powershell/azure/azurerm/install-azurerm-ps) 擴充功能。  開啟命令提示字元或終端機，然後執行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 遵循提示，為您的部署指派資源群組的名稱。  腳本只會在您的 Azure 訂用帳戶中部署此資源群組的相依性，但不會部署微服務。  腳本也會在 Azure AD 中註冊應用程式。  這是支援 OAUTH 型驗證的必要項。  部署可能需要數分鐘的時間。

3. 腳本完成後，您可以選擇儲存 env 檔案。  Env 環境檔案是您想要在開發電腦上執行之所有服務和工具的設定檔。  

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

確定您使用的是簡短且簡單的資源組名。  名稱也會用來命名資源，因為它必須符合資源命名需求。  

### <a name="azure-active-directory-ad-registration"></a> (AD) 註冊 Azure Active Directory

部署腳本會嘗試在 Azure AD 中註冊 Azure AD 的應用程式。  根據您對所選 Azure AD 租使用者的許可權，這可能會失敗。 選項有三個：

1. 如果您從租使用者清單中選擇 Azure AD 租使用者，請重新開機腳本，並從清單中選擇不同的租使用者。
2. 或者，部署私用 Azure AD 租使用者、重新開機腳本，然後選擇使用它。
3. 繼續進行但不進行驗證。  由於您是在本機執行微服務，這是可接受的，但不會模仿生產環境。  

## <a name="next-steps"></a>後續步驟

現在您已成功將 OPC 對應項服務部署至現有的專案，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [瞭解如何部署 OPC 對應項模組](howto-opc-twin-deploy-modules.md)