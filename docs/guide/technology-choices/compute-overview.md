---
title: Azure コンピューティング オプションの概要
titleSuffix: Azure Application Architecture Guide
description: Azure コンピューティング オプションの概要。
author: MikeWasson
ms.date: 06/13/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 63754ce84f001226b6cddf1f30a152bcfce3e7ac
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245583"
---
# <a name="overview-of-azure-compute-options"></a>Azure コンピューティング オプションの概要

"*コンピューティング*" という用語は、アプリケーションがそこで実行されるコンピューティング リソースのホスティング モデルを指します。

## <a name="overview"></a>概要

その 1 つが**サービスとしてのインフラストラクチャ** (IaaS) です。 IaaS を使用して、関連付けられているネットワークおよびストレージ コンポーネントと共に、必要な VM をプロビジョニングします。 その後、必要なソフトウェアとアプリケーションを、こうした VM にデプロイします。 このモデルは従来のオンプレミス環境に最も近いものですが、Microsoft がインフラストラクチャを管理する点が異なります。 個々の VM は、ご自身で引き続き管理します。

**サービスとしてのプラットフォーム (PaaS)** では、マネージド ホスティング環境が提供されます。この環境では、VM やネットワーク リソースを管理せずにアプリケーションをデプロイできます。 たとえば、個々の VM を作成するのではなく、インスタンス数を指定すると、サービスによって、必要なリソースがプロビジョニング、構成、および管理されます。 Azure App Service は PaaS サービスの例です。

IaaS から純粋な PaaS への中間領域もあります。 たとえば、Azure VM は、VM Scale Sets を使用して自動スケールできます。 この自動スケール機能は厳密には PaaS ではありませんが、PaaS サービスで見つかる可能性がある管理機能の種類です。

**サービスとしての関数** (FaaS) はさらに進んで、ホスティング環境について心配する必要がありません。 コンピューティング インスタンスを作成して、そのインスタンスにコードをデプロイするのではなく、コードをデプロイするだけで、そのコードがサービスによって自動的に実行されます。 コンピューティング リソースを管理する必要はありません。 こうしたサービスは、サーバーレス アーキテクチャを使用し、トラフィックの処理に必要なレベルに合わせて、シームレスにスケールアップまたはスケールダウンします。 Azure Functions は FaaS サービスです。

IaaS は、制御性、柔軟性、および移植性に最も優れています。 FaaS では、シンプルさと弾力性のある拡大縮小が実現します。また、コードが実行されている時間に対して料金が発生するため、コスト削減につながる可能性があります。 PaaS は、この 2 つの中間に位置します。 一般的に、サービスが柔軟であるほど、リソースの構成と管理に対する責任は重くなります。 FaaS サービスでは、アプリケーションの実行がほぼ全面的に自動管理されますが、IaaS ソリューションでは、作成した VM とネットワーク コンポーネントは、ご自身でプロビジョニング、構成、および管理する必要があります。

## <a name="azure-compute-options"></a>Azure のコンピューティング オプション

Azure で現在使用できる主なコンピューティング オプションを次に示します。

- [Virtual Machines](/azure/virtual-machines/)。IaaS サービスです。仮想ネットワーク (VNet) 内で VM をデプロイおよび管理できます。
- [App Service](/azure/app-service/app-service-value-prop-what-is)。Web アプリ、モバイル アプリ バックエンド、RESTful API、または自動化されたビジネス プロセスをホストするためのマネージド PaaS サービスです。
- [Service Fabric](/azure/service-fabric/service-fabric-overview)。Azure、オンプレミスなど、多数の環境で実行できる分散システム プラットフォームです。 Service Fabric は、マシン クラスター全体のマイクロサービスのオーケストレーターです。
- [Azure Kubernetes Service](/azure/aks/) は、コンテナー化されたアプリケーションを実行するために、ホストされた Kubernetes サービスを管理します。
- [Azure Container Instances](/azure/container-instances/container-instances-overview) には、仮想マシンをプロビジョニングしたり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。
- [Azure Functions](/azure/azure-functions/functions-overview)。管理された FaaS サービスです。
- [Azure Batch](/azure/batch/batch-technical-overview)。大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションを実行するための管理されたサービスです。
- [Cloud Services](/azure/cloud-services/cloud-services-choose-me)。クラウド アプリケーションを実行するための管理されたサービスです。 PaaS ホスティング モデルが使用されます。

コンピューティング オプションを選択するときの考慮事項をいくつか次に示します。

- ホスティング モデル。 サービスはどのようにホストされますか。 このホスティング環境では、どのような要件と制限が適用されますか。
- DevOps。 アプリケーション アップグレードのための組み込みサポートはありますか。 デプロイメント モデルは何ですか。
- スケーラビリティ。 インスタンスの追加または削除はサービスによってどのように処理されますか。 負荷およびその他のメトリックに基づいて自動スケールできますか。
- 可用性。 サービス SLA は何ですか。
- コスト。 サービス自体のコストのほか、そのサービスに構築されているソリューションを管理するための運用コストを考慮してください。 たとえば、IaaS ソリューションの運用コストは高くつく可能性があります。
- 各サービスの全体的な制限事項。
- このサービスに適したアプリケーション アーキテクチャの種類。

## <a name="next-steps"></a>次の手順

[Azure コンピューティング サービスのデシジョン ツリー](./compute-decision-tree.md)は、お使いのアプリケーションのコンピューティング サービスを選択するうえで役に立ちます

Azure のコンピューティング オプション比較の詳細については、[Azure コンピューティング サービスを選択するための条件](./compute-comparison.md)に関するページをご覧ください。
