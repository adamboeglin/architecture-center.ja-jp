---
title: Windows VM を使用した、セキュリティで保護された Web アプリのビルド
titleSuffix: Azure Example Scenarios
description: スケール セット、Application Gateway、ロード バランサーを使用して、セキュリティで保護された多層 Web アプリケーションを、Azure 上の Windows Server を使用して構築します。
author: iainfoulds
ms.date: 12/06/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: seodec18, Windows
social_image_url: /azure/architecture/example-scenario/infrastructure/media/architecture-regulated-multitier-app.png
ms.openlocfilehash: 2e3bb28abf870a1ce358dd67efeb3f422b983651
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639939"
---
# <a name="building-secure-web-applications-with-windows-virtual-machines-on-azure"></a><span data-ttu-id="3ef6d-103">Azure 上の Windows Server を使用した、セキュリティで保護された Web アプリケーションの構築</span><span class="sxs-lookup"><span data-stu-id="3ef6d-103">Building secure web applications with Windows virtual machines on Azure</span></span>

<span data-ttu-id="3ef6d-104">このシナリオでは、セキュリティ保護された多層 Web アプリケーションを Microsoft Azure で実行するためのアーキテクチャと設計のガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-104">This scenario provides architecture and design guidance for running secure, multi-tier web applications on Microsoft Azure.</span></span> <span data-ttu-id="3ef6d-105">この例の ASP.NET アプリケーションは、仮想マシンを使用する保護されたバックエンド Microsoft SQL Server クラスターに安全に接続します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-105">In this example, an ASP.NET application securely connects to a protected back-end Microsoft SQL Server cluster using virtual machines.</span></span>

<span data-ttu-id="3ef6d-106">これまでは、安全なインフラストラクチャを提供するために、従来のオンプレミスのアプリケーションとサービスを組織が維持する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-106">Traditionally, organizations had to maintain legacy on-premises applications and services to provide a secure infrastructure.</span></span> <span data-ttu-id="3ef6d-107">これらの Windows Server アプリケーションを Azure に安全にデプロイすることにより、組織は自身のデプロイを最新化し、オンプレミスの運用コストと管理オーバーヘッドを減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-107">By deploying these Windows Server applications securely in Azure, organizations can modernize their deployments and reduce their on-premises operating costs and management overhead.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="3ef6d-108">関連するユース ケース</span><span class="sxs-lookup"><span data-stu-id="3ef6d-108">Relevant use cases</span></span>

<span data-ttu-id="3ef6d-109">このシナリオを適用できるいくつかの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-109">A few examples of where this scenario may apply:</span></span>

- <span data-ttu-id="3ef6d-110">セキュリティで保護されたクラウド環境におけるアプリケーション デプロイの最新化。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-110">Modernizing application deployments in a secure cloud environment.</span></span>
- <span data-ttu-id="3ef6d-111">従来のオンプレミスのアプリケーションとサービスの管理のオーバーヘッドの軽減。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-111">Reducing the management overhead of legacy on-premises applications and services.</span></span>
- <span data-ttu-id="3ef6d-112">新しいアプリケーション プラットフォームでの医療と患者体験の向上。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-112">Improving patient healthcare and experience with new application platforms.</span></span>

## <a name="architecture"></a><span data-ttu-id="3ef6d-113">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="3ef6d-113">Architecture</span></span>

![規制対象業界向けの多層 Windows Server アプリケーションに関与する Azure コンポーネントのアーキテクチャ概要][architecture]

<span data-ttu-id="3ef6d-115">このシナリオでは、バックエンド データベースに接続されているフロント エンド Web アプリケーション (両方とも Windows Server 2016 で実行されています) を示します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-115">This scenario shows a front-end web application connecting to a back-end database, both running on Windows Server 2016.</span></span> <span data-ttu-id="3ef6d-116">このシナリオのデータ フローは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-116">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="3ef6d-117">ユーザーが Azure Application Gateway 経由で、フロントエンドの ASP.NET アプリケーションにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-117">Users access the front-end ASP.NET application through an Azure Application Gateway.</span></span>
2. <span data-ttu-id="3ef6d-118">Application Gateway は、Azure 仮想マシン スケール セット内でトラフィックを VM インスタンスに分散します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-118">The Application Gateway distributes traffic to VM instances within an Azure virtual machine scale set.</span></span>
3. <span data-ttu-id="3ef6d-119">このアプリケーションは、Azure Load Balancer を使用して、バックエンド層の Microsoft SQL Server クラスターに接続します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-119">The application connects to Microsoft SQL Server cluster in a back-end tier via an Azure load balancer.</span></span> <span data-ttu-id="3ef6d-120">これらのバックエンド SQL Server インスタンスは別個の Azure 仮想ネットワークにあり、トラフィック フローを制限するネットワーク セキュリティ グループの規則によってセキュリティで保護されています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-120">These back-end SQL Server instances are in a separate Azure virtual network, secured by network security group rules that limit traffic flow.</span></span>
4. <span data-ttu-id="3ef6d-121">ロード バランサーは、SQL Server のトラフィックを、別の仮想マシン スケール セット内の VM インスタンスに分散します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-121">The load balancer distributes SQL Server traffic to VM instances in another virtual machine scale set.</span></span>
5. <span data-ttu-id="3ef6d-122">Azure Blob Storage は、バックエンド層の SQL Server クラスター用の[クラウド監視][cloud-witness]として機能します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-122">Azure Blob Storage acts as a [cloud witness][cloud-witness] for the SQL Server cluster in the back-end tier.</span></span> <span data-ttu-id="3ef6d-123">VNet 内からの接続は、Azure Storage の VNet サービス エンドポイントで有効にされます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-123">The connection from within the VNet is enabled with a VNet Service Endpoint for Azure Storage.</span></span>

### <a name="components"></a><span data-ttu-id="3ef6d-124">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3ef6d-124">Components</span></span>

- <span data-ttu-id="3ef6d-125">[Azure Application Gateway][appgateway-docs] は、アプリケーション対応のレイヤー 7 Web トラフィック ロード バランサーで、特定のルーティング規則に基づいてトラフィックを分散できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-125">[Azure Application Gateway][appgateway-docs] is a layer 7 web traffic load balancer that is application-aware and can distribute traffic based on specific routing rules.</span></span> <span data-ttu-id="3ef6d-126">App Gateway で、SSL オフロードを処理し、Web サーバーのパフォーマンスを向上させることもできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-126">App Gateway can also handle SSL offloading for improved web server performance.</span></span>
- <span data-ttu-id="3ef6d-127">[Azure Virtual Network][vnet-docs] を使用すると、VM などのリソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-127">[Azure Virtual Network][vnet-docs] allows resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="3ef6d-128">仮想ネットワークにより、分離性、セグメント化、トラフィックのフィルター処理とルーティングが提供され、場所間の接続が可能になります。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-128">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="3ef6d-129">このシナリオでは適切な NSG で結合されている 2 つ仮想ネットワークを使用して、[非武装地帯][dmz] (DMZ) と、アプリケーション コンポーネントの分離性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-129">Two virtual networks combined with the appropriate NSGs are used in this scenario to provide a [demilitarized zone][dmz] (DMZ) and isolation of the application components.</span></span> <span data-ttu-id="3ef6d-130">仮想ネットワーク ピアリングによって、2 つのネットワークが接続されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-130">Virtual network peering connects the two networks together.</span></span>
- <span data-ttu-id="3ef6d-131">[Azure 仮想マシン スケール セット][scaleset-docs]では、負荷分散が行われる同一の VM のグループを作成して管理できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-131">[Azure virtual machine scale set][scaleset-docs] lets you create and manager a group of identical, load balanced, VMs.</span></span> <span data-ttu-id="3ef6d-132">需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-132">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="3ef6d-133">このシナリオでは 2 つの別個の仮想マシン スケール セットが使用されます。1 つはフロントエンドの ASP.NET アプリケーション インスタンス用、もう 1 つはバックエンドの SQL Server クラスター VM インスタンス用です。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-133">Two separate virtual machine scale sets are used in this scenario - one for the front-end ASP.NET application instances, and one for the back-end SQL Server cluster VM instances.</span></span> <span data-ttu-id="3ef6d-134">PowerShell の Desired State Configuration (DSC) または Azure カスタム スクリプト拡張機能を使用すると、必要なソフトウェアおよび構成設定で VM インスタンスをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-134">PowerShell desired state configuration (DSC) or the Azure custom script extension can be used to provision the VM instances with the required software and configuration settings.</span></span>
- <span data-ttu-id="3ef6d-135">[Azure ネットワーク セキュリティ グループ][nsg-docs]には、ソースまたはターゲット IP アドレス、ポート、およびプロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-135">[Azure network security groups][nsg-docs] contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="3ef6d-136">このシナリオの仮想ネットワークは、アプリケーション コンポーネント間のトラフィック フローを制限するネットワーク セキュリティ グループ規則によって保護されています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-136">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
- <span data-ttu-id="3ef6d-137">[Azure Load Balancer][loadbalancer-docs] は、規則と正常性プローブに従って受信トラフィックを分散します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-137">[Azure load balancer][loadbalancer-docs] distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="3ef6d-138">ロード バランサーは、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-138">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="3ef6d-139">このシナリオでは内部ロード バランサーを使用して、フロントエンド アプリケーション層からバックエンド SQL Server クラスターへのトラフィックを分散します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-139">An internal load balancer is used in this scenario to distribute traffic from the front-end application tier to the back-end SQL Server cluster.</span></span>
- <span data-ttu-id="3ef6d-140">[Azure Blob Storage][cloudwitness-docs] は、SQL Server クラスター用のクラウド監視の場所として機能します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-140">[Azure Blob Storage][cloudwitness-docs] acts a Cloud Witness location for the SQL Server cluster.</span></span> <span data-ttu-id="3ef6d-141">この監視は、クォーラムの決定に追加の投票が必要な、クラスター操作と意思決定で使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-141">This witness is used for cluster operations and decisions that require an additional vote to decide quorum.</span></span> <span data-ttu-id="3ef6d-142">クラウド監視を使用すると、従来のファイル共有監視として機能する追加の VM が不要になります。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-142">Using Cloud Witness removes the need for an additional VM to act as a traditional File Share Witness.</span></span>

### <a name="alternatives"></a><span data-ttu-id="3ef6d-143">代替手段</span><span class="sxs-lookup"><span data-stu-id="3ef6d-143">Alternatives</span></span>

- <span data-ttu-id="3ef6d-144">インフラストラクチャはオペレーティング システムに依存しないため、Linux と Windows は同じ意味で使用できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-144">Linux and Windows can be used interchangeably since the infrastructure isn't dependent on the operating system.</span></span>

- <span data-ttu-id="3ef6d-145">バックエンド データ ストアの代わりに、[Linux 用 SQL Server][sql-linux] を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-145">[SQL Server for Linux][sql-linux] can replace the back-end data store.</span></span>

- <span data-ttu-id="3ef6d-146">データ ストアの代わりに、[Cosmos DB](/azure/cosmos-db/introduction) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-146">[Cosmos DB](/azure/cosmos-db/introduction) is another alternative for the data store.</span></span>

## <a name="considerations"></a><span data-ttu-id="3ef6d-147">考慮事項</span><span class="sxs-lookup"><span data-stu-id="3ef6d-147">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="3ef6d-148">可用性</span><span class="sxs-lookup"><span data-stu-id="3ef6d-148">Availability</span></span>

<span data-ttu-id="3ef6d-149">このシナリオの VM インスタンスは、[可用性ゾーン](/azure/availability-zones/az-overview)にまたがってデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-149">The VM instances in this scenario are deployed across [Availability Zones](/azure/availability-zones/az-overview).</span></span> <span data-ttu-id="3ef6d-150">それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-150">Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking.</span></span> <span data-ttu-id="3ef6d-151">有効な各リージョンには、少なくとも 3 つの可用性ゾーンが存在します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-151">Each enabled region has a minimum of three availability zones.</span></span> <span data-ttu-id="3ef6d-152">このようにゾーン間で VM インスタンスを分散させることにより、アプリケーション層に高可用性が実現します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-152">This distribution of VM instances across zones provides high availability to the application tiers.</span></span>

<span data-ttu-id="3ef6d-153">データベース層は、AlwaysOn 可用性グループを使用するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-153">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="3ef6d-154">この SQL Server 構成により、クラスター内の 1 つのプライマリ データベースが、最大 8 つのセカンダリ データベースと共に構成されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-154">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="3ef6d-155">プライマリ データベースで問題が発生した場合、クラスターは、セカンダリ データベースのいずれかにフェールオーバーします。これにより、アプリケーションを引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-155">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, which allows the application to continue to be available.</span></span> <span data-ttu-id="3ef6d-156">詳細については、[SQL Server 用の Always On 可用性グループの概要][sqlalwayson-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-156">For more information, see [Overview of Always On availability groups for SQL Server][sqlalwayson-docs].</span></span>

### <a name="scalability"></a><span data-ttu-id="3ef6d-157">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="3ef6d-157">Scalability</span></span>

<span data-ttu-id="3ef6d-158">このシナリオでは、フロントエンド コンポーネントとバックエンド コンポーネントに対して、仮想マシン スケール セットを使用します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-158">This scenario uses virtual machine scale sets for the front-end and back-end components.</span></span> <span data-ttu-id="3ef6d-159">スケール セットにより、顧客の要求に応じて、または定義されているスケジュールに基づいて、フロントエンド アプリケーション層を実行する VM インスタンスの数を自動的にスケーリングできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-159">With scale sets, the number of VM instances that run the front-end application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="3ef6d-160">詳細については、[仮想マシン スケール セットでの自動スケールの概要][vmssautoscale-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-160">For more information, see [Overview of autoscale with virtual machine scale sets][vmssautoscale-docs].</span></span>

<span data-ttu-id="3ef6d-161">スケーラビリティに関する他のトピックについては、Azure アーキテクチャ センターの「[スケーラビリティのチェックリスト][scalability]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-161">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="3ef6d-162">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="3ef6d-162">Security</span></span>

<span data-ttu-id="3ef6d-163">フロントエンド アプリケーション層へのすべての仮想ネットワーク トラフィックが、ネットワーク セキュリティ グループによって保護されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-163">All the virtual network traffic into the front-end application tier and protected by network security groups.</span></span> <span data-ttu-id="3ef6d-164">フロントエンド アプリケーション層 VM インスタンスのみがバックエンド データベース層にアクセスできるように、規則によってトラフィックのフローが制限されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-164">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="3ef6d-165">データベース層からの送信インターネット トラフィックは許可されません。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-165">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="3ef6d-166">攻撃フットプリントを減らすために、ダイレクト リモート管理ポートは開かれていません。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-166">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="3ef6d-167">詳細については、[Azure ネットワーク セキュリティ グループ][nsg-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-167">For more information, see [Azure network security groups][nsg-docs].</span></span>

<span data-ttu-id="3ef6d-168">Payment Card Industry Data Security Standards (PCI DSS 3.2) の展開のガイダンスについては、[コンプライアンス インフラストラクチャ][pci-dss]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-168">To view guidance on deploying Payment Card Industry Data Security Standards (PCI DSS 3.2) [compliant infrastructure][pci-dss].</span></span> <span data-ttu-id="3ef6d-169">セキュリティで保護されたシナリオの設計に関する一般的なガイダンスについては、「[Azure のセキュリティのドキュメント][security]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-169">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="3ef6d-170">回復性</span><span class="sxs-lookup"><span data-stu-id="3ef6d-170">Resiliency</span></span>

<span data-ttu-id="3ef6d-171">このシナリオでは、可用性ゾーンおよび仮想マシン スケール セットと組み合わて、Azure Application Gateway とロード バランサーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-171">In combination with the use of Availability Zones and virtual machine scale sets, this scenario uses Azure Application Gateway and load balancer.</span></span> <span data-ttu-id="3ef6d-172">これら 2 つのネットワーク コンポーネントは、接続されている VM インスタンスにトラフィックを分散します。また、コンポーネントには正常性プローブが含まれ、正常な状態の VM にのみトラフィックが分散されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-172">These two networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="3ef6d-173">2 つの Application Gateway インスタンスは、アクティブ/パッシブ構成で構成され、ゾーン冗長ロード バランサーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-173">Two Application Gateway instances are configured in an active-passive configuration, and a zone-redundant load balancer is used.</span></span> <span data-ttu-id="3ef6d-174">トラフィックを中断し、エンドユーザーへのアクセスに影響を及ぼす可能性のある問題に対する回復性が、この構成によってネットワーク リソースとアプリケーションで実現します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-174">This configuration makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="3ef6d-175">回復性があるソリューションの設計に関する一般的なガイダンスについては、「[Designing reliable Azure applications](../../reliability/index.md)」 (信頼性の高い Azure アプリケーションの設計) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-175">For general guidance on designing resilient solutions, see [Designing reliable Azure applications](../../reliability/index.md).</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="3ef6d-176">シナリオのデプロイ</span><span class="sxs-lookup"><span data-stu-id="3ef6d-176">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="3ef6d-177">前提条件</span><span class="sxs-lookup"><span data-stu-id="3ef6d-177">Prerequisites</span></span>

- <span data-ttu-id="3ef6d-178">既存の Azure アカウントが必要です。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-178">You must have an existing Azure account.</span></span> <span data-ttu-id="3ef6d-179">Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-179">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

- <span data-ttu-id="3ef6d-180">バックエンド スケール セットに SQL Server クラスターをデプロイするには、Azure Active Directory (AD) Domain Services 内のドメインが必要です。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-180">To deploy a SQL Server cluster into the back-end scale set, you would need a domain in Azure Active Directory (AD) Domain Services.</span></span>

### <a name="deploy-the-components"></a><span data-ttu-id="3ef6d-181">コンポーネントをデプロイする</span><span class="sxs-lookup"><span data-stu-id="3ef6d-181">Deploy the components</span></span>

<span data-ttu-id="3ef6d-182">Azure Resource Manager テンプレートを使用して、このシナリオのコア インフラストラクチャをデプロイするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-182">To deploy the core infrastructure for this scenario with an Azure Resource Manager template, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="3ef6d-183">**[Deploy to Azure]\(Azure にデプロイ\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-183">Select the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="3ef6d-184">Azure portal でテンプレートのデプロイが開くまで待ってから、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-184">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   - <span data-ttu-id="3ef6d-185">リソース グループの **[新規作成]** を選択し、テキスト ボックスに名前 (例: *myWindowsscenario*) を入力します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-185">Choose to **Create new** resource group, then provide a name such as *myWindowsscenario* in the text box.</span></span>
   - <span data-ttu-id="3ef6d-186">**[場所]** ドロップダウン ボックスでリージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-186">Select a region from the **Location** drop-down box.</span></span>
   - <span data-ttu-id="3ef6d-187">仮想マシン スケール セット インスタンスのユーザー名と安全なパスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-187">Provide a username and secure password for the virtual machine scale set instances.</span></span>
   - <span data-ttu-id="3ef6d-188">使用条件を確認し、**[上記の使用条件に同意する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-188">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   - <span data-ttu-id="3ef6d-189">**[購入]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-189">Select the **Purchase** button.</span></span>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="3ef6d-190">デプロイが完了するまでに 15 から 20 分かかることがあります。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-190">It can take 15-20 minutes for the deployment to complete.</span></span>

## <a name="pricing"></a><span data-ttu-id="3ef6d-191">価格</span><span class="sxs-lookup"><span data-stu-id="3ef6d-191">Pricing</span></span>

<span data-ttu-id="3ef6d-192">このシナリオの実行コストを調べることができるように、すべてのサービスがコスト計算ツールで事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-192">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="3ef6d-193">特定のユース ケースについて価格の変化を確認するには、予想されるトラフィックに合わせて該当する変数を変更します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-193">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="3ef6d-194">ご自身のアプリケーションを実行するスケール セット VM インスタンスの数に基づいて、3 つのサンプル コスト プロファイルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-194">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications.</span></span>

- <span data-ttu-id="3ef6d-195">[Small][small-pricing]: この価格例は、2 つのフロントエンドおよび 2 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-195">[Small][small-pricing]: this pricing example correlates to two front-end and two back-end VM instances.</span></span>
- <span data-ttu-id="3ef6d-196">[Medium][medium-pricing]: この価格例は、20 つのフロントエンドおよび 5 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-196">[Medium][medium-pricing]: this pricing example correlates to 20 front-end and 5 back-end VM instances.</span></span>
- <span data-ttu-id="3ef6d-197">[Large][large-pricing]: この価格例は、100 つのフロントエンドおよび 10 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-197">[Large][large-pricing]: this pricing example correlates to 100 front-end and 10 back-end VM instances.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3ef6d-198">関連リソース</span><span class="sxs-lookup"><span data-stu-id="3ef6d-198">Related resources</span></span>

<span data-ttu-id="3ef6d-199">このシナリオでは、Microsoft SQL Server クラスターを実行するバックエンド仮想マシン スケール セットを使用しました。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-199">This scenario used a back-end virtual machine scale set that runs a Microsoft SQL Server cluster.</span></span> <span data-ttu-id="3ef6d-200">アプリケーション データ用に、安全でスケーラブルなデータベース層として Cosmos DB を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-200">Cosmos DB could also be used as a scalable and secure database tier for the application data.</span></span> <span data-ttu-id="3ef6d-201">[Azure 仮想ネットワーク サービス エンドポイント][vnetendpoint-docs]を使用すると、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-201">An [Azure virtual network service endpoint][vnetendpoint-docs] allows you to secure your critical Azure service resources to only your virtual networks.</span></span> <span data-ttu-id="3ef6d-202">このシナリオでは、VNet エンドポイントを使用することで、フロントエンド アプリケーション層と Cosmos DB の間のトラフィックをセキュリティで保護できます。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-202">In this scenario, VNet endpoints allow you to secure traffic between the front-end application tier and Cosmos DB.</span></span> <span data-ttu-id="3ef6d-203">詳しくは、「[Azure Cosmos DB の概要](/azure/cosmos-db/introduction)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-203">For more information, see the [Azure Cosmos DB overview](/azure/cosmos-db/introduction).</span></span>

<span data-ttu-id="3ef6d-204">詳細な実装ガイドについては、[SQL Server を使用した N 層アプリケーションの参照アーキテクチャ][ntiersql-ra]に関するページを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3ef6d-204">For more detailed implementation guides, review the [reference architecture for N-tier applications using SQL Server][ntiersql-ra].</span></span>

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[cloud-witness]: /windows-server/failover-clustering/deploy-cloud-witness
[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93
