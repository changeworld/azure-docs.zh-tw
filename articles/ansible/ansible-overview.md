---
title: 使用 Ansible 搭配 Azure
description: 簡介使用 Ansible 自動佈建雲端、進行組態管理和應用程式部署。
keywords: ansible, azure, devops, 概觀, 雲端佈建, 組態管理, 應用程式部署, ansible 模組, ansible 腳本
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193560"
---
# <a name="using-ansible-with-azure"></a>使用 Ansible 搭配 Azure

[Ansible](https://www.ansible.com) 是一項開放原始碼產品，可自動佈建雲端、進行組態管理和應用程式部署。 您可以使用 Ansible 來佈建虛擬機器、容器和網路，並完成雲端基礎結構。 此外，Ansible 可讓您將環境中的資源部署和設定自動化。

本文提供基本概觀，說明使用 Ansible 搭配 Azure 的一些優點。

## <a name="ansible-playbooks"></a>Ansible 腳本

[Ansible 劇本](https://docs.ansible.com/ansible/latest/playbooks.html)可讓您指示 Ansible 設定您的環境。 劇本會使用 YAML 編碼，讓一般人能夠讀取。 「教學課程」一節提供使用劇本安裝和設定 Azure 資源的許多範例。 

## <a name="ansible-modules"></a>Ansible 模組

Ansible 包含 [Ansible 模組](https://docs.ansible.com/ansible/latest/modules_by_category.html)的套件，可直接在遠端主機上執行，或是透過[劇本](https://docs.ansible.com/ansible/latest/playbooks.html)執行。 使用者可建立自己的模組。 模組可用來控制系統資源 (例如服務、套件或檔案)，或是執行系統命令。

針對與 Azure 服務的互動，Ansible 包含一組 [Ansible 雲端模組](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)。 這些模組可讓您在 Azure 上建立及協調您的基礎結構。 

## <a name="migrate-existing-workload-to-azure"></a>將現有的工作負載移轉至 Azure

在您使用 Ansible 定義基礎結構之後，您可以套用應用程式的腳本，讓 Azure 視需要自動調整您的環境。 

## <a name="automate-cloud-native-application-in-azure"></a>將 Azure 中的雲端原生應用程式自動化

Ansible 可讓您使用諸如 [Azure Functions](https://azure.microsoft.com//services/functions/) 和 [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/) 等 Azure 微服務，將 Azure 中的雲端原生應用程式自動化。  

## <a name="manage-deployments-with-dynamic-inventory"></a>使用動態清查管理部署

Ansible 能夠透過其[動態清查](https://docs.ansible.com/ansible/intro_dynamic_inventory.html)功能來提取 Azure 資源中的清查。 接著，您可以將現有的 Azure 部署標記，並透過 Ansible 管理這些標記的部署。

## <a name="additional-azure-marketplace-options"></a>Azure Marketplace 的其他選項

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower)是 Red Hat 提供的 Azure Marketplace 映像。 

Ansible Tower 是 Ansible 的網頁型 UI 和儀表板，具有下列功能：

* 可讓您定義角色型存取控制、作業排程，以及圖形化清查管理。 
* 包含 REST API 和 CLI，讓您可以將 Tower 插入現有的工具和程序中。 
* 支援劇本執行的即時輸出。 
* 為認證 (例如 Azure 和 SSH 金鑰) 加密，讓您可以直接委派工作而無須公開認證。

## <a name="ansible-module-and-version-matrix-for-azure"></a>Azure 的 Ansible 模組和版本對照表

Ansible 包含一組用來佈建和設定 Azure 資源的模組。 這些資源包括虛擬機器、擴展集、網路服務和容器服務。 [Ansible 矩陣](./ansible-matrix.md)會列出適用於 Azure 的 Ansible 模組及其隨附的 Ansible 版本。

## <a name="next-steps"></a>後續步驟

- [快速入門：將適用於 Azure 的 Ansible 解決方案範本部署至 CentOS](./ansible-deploy-solution-template.md)
- [快速入門：使用 Ansible 在 Azure 中設定 Linux 虛擬機器](./ansible-install-configure.md)