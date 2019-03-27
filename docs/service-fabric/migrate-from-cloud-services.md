---
title: Azure Cloud Services アプリケーションを Azure Service Fabric に移行する
description: Azure Cloud Services アプリケーションを Azure Service Fabric に移行する方法。
author: MikeWasson
ms.date: 04/11/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: 66f1431f45a0c9accf3a8227fa8cbb5966568372
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248013"
---
# <a name="migrate-an-azure-cloud-services-application-to-azure-service-fabric"></a><span data-ttu-id="304bb-103">Azure Cloud Services アプリケーションを Azure Service Fabric に移行する</span><span class="sxs-lookup"><span data-stu-id="304bb-103">Migrate an Azure Cloud Services application to Azure Service Fabric</span></span> 

<span data-ttu-id="304bb-104">[![GitHub](../_images/github.png) サンプル コード][sample-code]</span><span class="sxs-lookup"><span data-stu-id="304bb-104">[![GitHub](../_images/github.png) Sample code][sample-code]</span></span>

<span data-ttu-id="304bb-105">この記事では、Azure Cloud Services のアプリケーションを Azure Service Fabric に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-105">This article describes migrating an application from Azure Cloud Services to Azure Service Fabric.</span></span> <span data-ttu-id="304bb-106">これはアーキテクチャについての決定を中心とした、推奨されるプラクティスです。</span><span class="sxs-lookup"><span data-stu-id="304bb-106">It focuses on architectural decisions and recommended practices.</span></span> 

<span data-ttu-id="304bb-107">このプロジェクトでは、Surveys と呼ばれる Cloud Services アプリケーションで作業を開始し、それを Service Fabric に移植しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-107">For this project, we started with a Cloud Services application called Surveys and ported it to Service Fabric.</span></span> <span data-ttu-id="304bb-108">目標は、できるだけ少ない変更でアプリケーションを移行することでした。</span><span class="sxs-lookup"><span data-stu-id="304bb-108">The goal was to migrate the application with as few changes as possible.</span></span> <span data-ttu-id="304bb-109">後の記事では、マイクロサービス アーキテクチャを採用することで、アプリケーションを Service Fabric 用に最適化します。</span><span class="sxs-lookup"><span data-stu-id="304bb-109">In a later article, we will optimize the application for Service Fabric by adopting a microservices architecture.</span></span>

<span data-ttu-id="304bb-110">この記事を読む前に、全体としての Service Fabric とマイクロサービスのアーキテクチャに関する基本を理解しておくと有益です。</span><span class="sxs-lookup"><span data-stu-id="304bb-110">Before reading this article, it will be useful to understand the basics of Service Fabric and microservices architectures in general.</span></span> <span data-ttu-id="304bb-111">次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-111">See the following articles:</span></span>

- <span data-ttu-id="304bb-112">[Azure Service Fabric の概要][sf-overview]</span><span class="sxs-lookup"><span data-stu-id="304bb-112">[Overview of Azure Service Fabric][sf-overview]</span></span>
- <span data-ttu-id="304bb-113">[マイクロサービスの手法でアプリケーションを構築する理由は何ですか。][sf-why-microservices]</span><span class="sxs-lookup"><span data-stu-id="304bb-113">[Why a microservices approach to building applications?][sf-why-microservices]</span></span>


## <a name="about-the-surveys-application"></a><span data-ttu-id="304bb-114">Surveys アプリケーションについて</span><span class="sxs-lookup"><span data-stu-id="304bb-114">About the Surveys application</span></span>

<span data-ttu-id="304bb-115">2012 年に、パターンとプラクティスを担当するグループが、『[Developing Multi-tenant Applications for the Cloud (クラウド向けマルチテナント アプリケーションの開発)][tailspin-book]』というタイトルの書籍のために Surveys というアプリケーションを作成しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-115">In 2012, the patterns & practices group created an application called Surveys, for a book called [Developing Multi-tenant Applications for the Cloud][tailspin-book].</span></span> <span data-ttu-id="304bb-116">この書籍は、Surveys アプリケーションの設計と実装を行う Tailspin という架空の企業のことを描写しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-116">The book describes a fictitious company named Tailspin that designs and implements the Surveys application.</span></span>

<span data-ttu-id="304bb-117">Surveys は、顧客がアンケートを作成できるマルチテナント アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="304bb-117">Surveys is a multitenant application that allows customers to create surveys.</span></span> <span data-ttu-id="304bb-118">顧客がアプリケーションにサインアップした後は、顧客の組織のメンバーが調査を作成して発行し、分析の結果を収集できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-118">After a customer signs up for the application,  members of the customer's organization can create and publish surveys, and collect the results for analysis.</span></span> <span data-ttu-id="304bb-119">アプリケーションには、ユーザーが調査を実施できるパブリック Web サイトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="304bb-119">The application includes a public website where people can take a survey.</span></span> <span data-ttu-id="304bb-120">元の Tailspin シナリオの詳細については、[ここ][tailspin-scenario]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-120">Read more about the original Tailspin scenario [here][tailspin-scenario].</span></span>

<span data-ttu-id="304bb-121">Tailspin は現在、Azure で実行される Service Fabric を使用して Surveys アプリケーションをマイクロサービス アーキテクチャに移行しようと考えています。</span><span class="sxs-lookup"><span data-stu-id="304bb-121">Now Tailspin wants to move the Surveys application to a microservices architecture, using Service Fabric running on Azure.</span></span> <span data-ttu-id="304bb-122">このアプリケーションは既に Cloud Services アプリケーションとしてデプロイされているため、Tailspin は複数フェーズのアプローチを採用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-122">Because the application is already deployed as a Cloud Services application, Tailspin adopts a multi-phase approach:</span></span>

1.  <span data-ttu-id="304bb-123">アプリケーションへの変更を最小限にしながらクラウド サービスを Service Fabric に移植します。</span><span class="sxs-lookup"><span data-stu-id="304bb-123">Port the cloud services to Service Fabric, while minimizing changes to the application.</span></span>
2.  <span data-ttu-id="304bb-124">マイクロサービス アーキテクチャに移行することで、アプリケーションを Service Fabric 用に最適化します。</span><span class="sxs-lookup"><span data-stu-id="304bb-124">Optimize the application for Service Fabric, by moving to a microservices architecture.</span></span>

<span data-ttu-id="304bb-125">この記事では最初のフェーズについて説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-125">This article describes the first phase.</span></span> <span data-ttu-id="304bb-126">後の記事では 2 番目のフェーズについて説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-126">A later article will describe the second phase.</span></span> <span data-ttu-id="304bb-127">実際のプロジェクトでは、おそらく両方のステージの時期が重なります。</span><span class="sxs-lookup"><span data-stu-id="304bb-127">In a real-world project, it's likely that both stages would overlap.</span></span> <span data-ttu-id="304bb-128">Service Fabric への移植を行うときに、アプリケーションのマイクロサービスへの再設計も開始することになります。</span><span class="sxs-lookup"><span data-stu-id="304bb-128">While porting to Service Fabric, you would also start to re-architect the application into micro-services.</span></span> <span data-ttu-id="304bb-129">その後、アーキテクチャをさらに調整する場合もあり、粒度の粗いサービスをより小さなサービスに分割する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-129">Later you might refine the architecture further, perhaps dividing coarse-grained services into smaller services.</span></span>  

<span data-ttu-id="304bb-130">アプリケーション コードは [GitHub][sample-code] から入手できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-130">The application code is available on [GitHub][sample-code].</span></span> <span data-ttu-id="304bb-131">このリポジトリには、Cloud Services アプリケーションと Service Fabric バージョンの両方が含まれています。</span><span class="sxs-lookup"><span data-stu-id="304bb-131">This repo contains both the Cloud Services application and the Service Fabric version.</span></span> 

> <span data-ttu-id="304bb-132">クラウド サービスは、書籍『*Developing Multi-tenant Applications for the Cloud (クラウド向けマルチテナント アプリケーションの開発)*』に記載された元のアプリケーションの更新されたバージョンです。</span><span class="sxs-lookup"><span data-stu-id="304bb-132">The cloud service is an updated version of the original application from the *Developing Multi-tenant Applications* book.</span></span>

## <a name="why-microservices"></a><span data-ttu-id="304bb-133">マイクロサービスを使用する理由</span><span class="sxs-lookup"><span data-stu-id="304bb-133">Why Microservices?</span></span>

<span data-ttu-id="304bb-134">マイクロサービスの詳しい説明についてはこの記事の範囲外ですが、以下に Tailspin が、マイクロサービス アーキテクチャに移行することで得られると期待しているいくつかのメリットを示します。</span><span class="sxs-lookup"><span data-stu-id="304bb-134">An in-depth discussion of microservices is beyond scope of this article, but here are some of the benefits that Tailspin hopes to get by moving to a microservices architecture:</span></span>

- <span data-ttu-id="304bb-135">**アプリケーションのアップグレード**。</span><span class="sxs-lookup"><span data-stu-id="304bb-135">**Application upgrades**.</span></span> <span data-ttu-id="304bb-136">サービスは独立してデプロイできるため、アプリケーションのアップグレードには増分アプローチを採用できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-136">Services can be deployed independently, so you can take an incremental approach to upgrading an application.</span></span>
- <span data-ttu-id="304bb-137">**回復性と障害の分離**。</span><span class="sxs-lookup"><span data-stu-id="304bb-137">**Resiliency and fault isolation**.</span></span> <span data-ttu-id="304bb-138">サービスが失敗した場合、その他のサービスは実行され続けます。</span><span class="sxs-lookup"><span data-stu-id="304bb-138">If a service fails, other services continue to run.</span></span>
- <span data-ttu-id="304bb-139">**スケーラビリティ**: </span><span class="sxs-lookup"><span data-stu-id="304bb-139">**Scalability**.</span></span> <span data-ttu-id="304bb-140">サービスは個別にスケールできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-140">Services can be scaled independently.</span></span>
- <span data-ttu-id="304bb-141">**柔軟性**。</span><span class="sxs-lookup"><span data-stu-id="304bb-141">**Flexibility**.</span></span> <span data-ttu-id="304bb-142">サービスは、テクノロジ スタックではなくビジネス シナリオを中心に設計されていて、サービスを新しいテクノロジ、フレームワーク、またはデータ ストアに移行しやすくなっています。</span><span class="sxs-lookup"><span data-stu-id="304bb-142">Services are designed around business scenarios, not technology stacks, making it easier to migrate services to new technologies, frameworks, or data stores.</span></span>
- <span data-ttu-id="304bb-143">**アジャイル開発**。</span><span class="sxs-lookup"><span data-stu-id="304bb-143">**Agile development**.</span></span> <span data-ttu-id="304bb-144">個々のサービスのコード量はモノリシック アプリケーションよりも少なく、コード ベースの理解、推論、およびテストが容易になっています。</span><span class="sxs-lookup"><span data-stu-id="304bb-144">Individual services have less code than a monolithic application, making the code base easier to understand, reason about, and test.</span></span>
- <span data-ttu-id="304bb-145">**集中的な小規模チーム**。</span><span class="sxs-lookup"><span data-stu-id="304bb-145">**Small, focused teams**.</span></span> <span data-ttu-id="304bb-146">アプリケーションは、多くの小さなサービスに分割されているため、各サービスは集中的な小規模チームで構築できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-146">Because the application is broken down into many small services, each service can be built by a small focused team.</span></span>

## <a name="why-service-fabric"></a><span data-ttu-id="304bb-147">Service Fabric を使用する理由</span><span class="sxs-lookup"><span data-stu-id="304bb-147">Why Service Fabric?</span></span>
      
<span data-ttu-id="304bb-148">Service Fabric には、以下を含め、分散システムで必要とされるほとんどの機能が組み込まれているため、Service Fabric はマイクロサービス アーキテクチャとの使用に適しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-148">Service Fabric is a good fit for a microservices architecture, because most of the features needed in a distributed system are built into Service Fabric, including:</span></span>

- <span data-ttu-id="304bb-149">**クラスターの管理**。</span><span class="sxs-lookup"><span data-stu-id="304bb-149">**Cluster management**.</span></span> <span data-ttu-id="304bb-150">Service Fabric は、ノードのフェールオーバー、正常性の監視、およびその他のクラスター管理機能に自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="304bb-150">Service Fabric automatically handles node failover, health monitoring, and other cluster management functions.</span></span>
- <span data-ttu-id="304bb-151">**水平スケーリング**。</span><span class="sxs-lookup"><span data-stu-id="304bb-151">**Horizontal scaling**.</span></span> <span data-ttu-id="304bb-152">Service Fabric クラスターにノードを追加すると、サービスは新しいノード全体にわたって分散されるため、アプリケーションは自動的にスケーリングされます。</span><span class="sxs-lookup"><span data-stu-id="304bb-152">When you add nodes to a Service Fabric cluster, the application automatically scales, as services are distributed across the new nodes.</span></span>
- <span data-ttu-id="304bb-153">**サービスの探索**。</span><span class="sxs-lookup"><span data-stu-id="304bb-153">**Service discovery**.</span></span> <span data-ttu-id="304bb-154">Service Fabric は、名前付きサービスのエンドポイントを解決できる探索サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="304bb-154">Service Fabric provides a discovery service that can resolve the endpoint for a named service.</span></span>
- <span data-ttu-id="304bb-155">**ステートレス サービスとステートフル サービス**。</span><span class="sxs-lookup"><span data-stu-id="304bb-155">**Stateless and stateful services**.</span></span> <span data-ttu-id="304bb-156">ステートフル サービスは [Reliable Collection][sf-reliable-collections] を使用します。これはキャッシュやキューの代わりに使用でき、パーティション化できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-156">Stateful services use [reliable collections][sf-reliable-collections], which can take the place of a cache or queue, and can be partitioned.</span></span>
- <span data-ttu-id="304bb-157">**アプリケーション ライフサイクル管理**。</span><span class="sxs-lookup"><span data-stu-id="304bb-157">**Application lifecycle management**.</span></span> <span data-ttu-id="304bb-158">サービスは、個別に、アプリケーションのダウンタイムなくアップグレードできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-158">Services can be upgraded independently and without application downtime.</span></span>
- <span data-ttu-id="304bb-159">マシンのクラスター全体にわたる**サービス オーケストレーション**。</span><span class="sxs-lookup"><span data-stu-id="304bb-159">**Service orchestration** across a cluster of machines.</span></span>
- <span data-ttu-id="304bb-160">リソースの消費を最適化するために**密度が向上**。</span><span class="sxs-lookup"><span data-stu-id="304bb-160">**Higher density** for optimizing resource consumption.</span></span> <span data-ttu-id="304bb-161">1 つのノードで複数のサービスをホストできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-161">A single node can host multiple services.</span></span>

<span data-ttu-id="304bb-162">Service Fabric は、Azure SQL Database、Cosmos DB、Azure Event Hubs などを含むさまざまな Microsoft サービスによって使用されており、分散クラウド アプリケーション構築用の実績あるプラットフォームとなっています。</span><span class="sxs-lookup"><span data-stu-id="304bb-162">Service Fabric is used by various Microsoft services, including Azure SQL Database, Cosmos DB, Azure Event Hubs, and others, making it a proven platform for building distributed cloud applications.</span></span> 

## <a name="comparing-cloud-services-with-service-fabric"></a><span data-ttu-id="304bb-163">Cloud Services と Service Fabric の比較</span><span class="sxs-lookup"><span data-stu-id="304bb-163">Comparing Cloud Services with Service Fabric</span></span>

<span data-ttu-id="304bb-164">次の表は、Cloud Services アプリケーションと Service Fabric アプリケーションの重要な違いの一部をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="304bb-164">The following table summarizes some of the important differences between Cloud Services and Service Fabric applications.</span></span> <span data-ttu-id="304bb-165">詳細な説明については、「[アプリケーションの移行前に、Cloud Services と Service Fabric の違いについて学習する][sf-compare-cloud-services]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-165">For a more in-depth discussion, see [Learn about the differences between Cloud Services and Service Fabric before migrating applications][sf-compare-cloud-services].</span></span>

|        | <span data-ttu-id="304bb-166">Cloud Services</span><span class="sxs-lookup"><span data-stu-id="304bb-166">Cloud Services</span></span> | <span data-ttu-id="304bb-167">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="304bb-167">Service Fabric</span></span> |
|--------|---------------|----------------|
| <span data-ttu-id="304bb-168">アプリケーションの構成</span><span class="sxs-lookup"><span data-stu-id="304bb-168">Application composition</span></span> | <span data-ttu-id="304bb-169">ロール</span><span class="sxs-lookup"><span data-stu-id="304bb-169">Roles</span></span>| <span data-ttu-id="304bb-170">サービス</span><span class="sxs-lookup"><span data-stu-id="304bb-170">Services</span></span> |
| <span data-ttu-id="304bb-171">密度</span><span class="sxs-lookup"><span data-stu-id="304bb-171">Density</span></span> |<span data-ttu-id="304bb-172">VM ごとに 1 つのロール インスタンス</span><span class="sxs-lookup"><span data-stu-id="304bb-172">One role instance per VM</span></span> | <span data-ttu-id="304bb-173">1 つのノードで複数のサービス</span><span class="sxs-lookup"><span data-stu-id="304bb-173">Multiple services in a single node</span></span> |
| <span data-ttu-id="304bb-174">最小ノード数</span><span class="sxs-lookup"><span data-stu-id="304bb-174">Minimum number of nodes</span></span> | <span data-ttu-id="304bb-175">ロールあたり 2</span><span class="sxs-lookup"><span data-stu-id="304bb-175">2 per role</span></span> | <span data-ttu-id="304bb-176">運用デプロイメントの場合はクラスターあたり 5</span><span class="sxs-lookup"><span data-stu-id="304bb-176">5 per cluster, for production deployments</span></span> |
| <span data-ttu-id="304bb-177">状態管理</span><span class="sxs-lookup"><span data-stu-id="304bb-177">State management</span></span> | <span data-ttu-id="304bb-178">ステートレス</span><span class="sxs-lookup"><span data-stu-id="304bb-178">Stateless</span></span> | <span data-ttu-id="304bb-179">ステートレスまたはステートフル\*</span><span class="sxs-lookup"><span data-stu-id="304bb-179">Stateless or stateful\*</span></span> |
| <span data-ttu-id="304bb-180">Hosting</span><span class="sxs-lookup"><span data-stu-id="304bb-180">Hosting</span></span> | <span data-ttu-id="304bb-181">Azure</span><span class="sxs-lookup"><span data-stu-id="304bb-181">Azure</span></span> | <span data-ttu-id="304bb-182">クラウドまたはオンプレミスの</span><span class="sxs-lookup"><span data-stu-id="304bb-182">Cloud or on-premises</span></span> |
| <span data-ttu-id="304bb-183">Web ホスティング</span><span class="sxs-lookup"><span data-stu-id="304bb-183">Web hosting</span></span> | <span data-ttu-id="304bb-184">IIS\*\*</span><span class="sxs-lookup"><span data-stu-id="304bb-184">IIS\*\*</span></span> | <span data-ttu-id="304bb-185">自己ホスト</span><span class="sxs-lookup"><span data-stu-id="304bb-185">Self-hosting</span></span> |
| <span data-ttu-id="304bb-186">デプロイメント モデル</span><span class="sxs-lookup"><span data-stu-id="304bb-186">Deployment model</span></span> | <span data-ttu-id="304bb-187">[クラシック デプロイ モデル][azure-deployment-models]</span><span class="sxs-lookup"><span data-stu-id="304bb-187">[Classic deployment model][azure-deployment-models]</span></span> | <span data-ttu-id="304bb-188">[Resource Manager][azure-deployment-models]</span><span class="sxs-lookup"><span data-stu-id="304bb-188">[Resource Manager][azure-deployment-models]</span></span>  |
| <span data-ttu-id="304bb-189">梱包</span><span class="sxs-lookup"><span data-stu-id="304bb-189">Packaging</span></span> | <span data-ttu-id="304bb-190">クラウド サービス パッケージ ファイル (.cspkg)</span><span class="sxs-lookup"><span data-stu-id="304bb-190">Cloud service package files (.cspkg)</span></span> | <span data-ttu-id="304bb-191">アプリケーションおよびサービス パッケージ</span><span class="sxs-lookup"><span data-stu-id="304bb-191">Application and service packages</span></span> |
| <span data-ttu-id="304bb-192">アプリケーションの更新</span><span class="sxs-lookup"><span data-stu-id="304bb-192">Application update</span></span> | <span data-ttu-id="304bb-193">VIP のスワップまたはローリング アップデート</span><span class="sxs-lookup"><span data-stu-id="304bb-193">VIP swap or rolling update</span></span> | <span data-ttu-id="304bb-194">ローリング アップデート</span><span class="sxs-lookup"><span data-stu-id="304bb-194">Rolling update</span></span> |
| <span data-ttu-id="304bb-195">自動スケール</span><span class="sxs-lookup"><span data-stu-id="304bb-195">Auto-scaling</span></span> | <span data-ttu-id="304bb-196">[組み込みのサービス][cloud-service-autoscale]</span><span class="sxs-lookup"><span data-stu-id="304bb-196">[Built-in service][cloud-service-autoscale]</span></span> | <span data-ttu-id="304bb-197">自動スケール用の VM Scale Sets</span><span class="sxs-lookup"><span data-stu-id="304bb-197">VM Scale Sets for auto scale out</span></span> |
| <span data-ttu-id="304bb-198">デバッグ</span><span class="sxs-lookup"><span data-stu-id="304bb-198">Debugging</span></span> | <span data-ttu-id="304bb-199">ローカル エミュレーター</span><span class="sxs-lookup"><span data-stu-id="304bb-199">Local emulator</span></span> | <span data-ttu-id="304bb-200">ローカル クラスター</span><span class="sxs-lookup"><span data-stu-id="304bb-200">Local cluster</span></span> |


<span data-ttu-id="304bb-201">\* ステートフル サービスが [Reliable Collection][sf-reliable-collections] を使用してレプリカ間にわたる状態を保存し、すべての読み取りがクラスター内のノードに対してローカルであるようにしています。</span><span class="sxs-lookup"><span data-stu-id="304bb-201">\* Stateful services use [reliable collections][sf-reliable-collections] to store state across replicas, so that all reads are local to the nodes in the cluster.</span></span> <span data-ttu-id="304bb-202">書き込みは、信頼性のため、ノード間でレプリケートされます。</span><span class="sxs-lookup"><span data-stu-id="304bb-202">Writes are replicated across nodes for reliability.</span></span> <span data-ttu-id="304bb-203">ステートレスなサービスは、データベースまたはその他の外部ストレージを使用して、外部の状態を持っている場合があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-203">Stateless services can have external state, using a database or other external storage.</span></span>

<span data-ttu-id="304bb-204">\*\* worker ロールは、OWIN を使用して ASP.NET Web API を自己ホストすることもできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-204">\*\* Worker roles can also self-host ASP.NET Web API using OWIN.</span></span>

## <a name="the-surveys-application-on-cloud-services"></a><span data-ttu-id="304bb-205">Cloud Services 上の Surveys アプリケーション</span><span class="sxs-lookup"><span data-stu-id="304bb-205">The Surveys application on Cloud Services</span></span>

<span data-ttu-id="304bb-206">次の図は、Cloud Services で実行中の Surveys アプリケーションのアーキテクチャを示しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-206">The following diagram shows the architecture of the Surveys application running on Cloud Services.</span></span> 

![](./images/tailspin01.png)

<span data-ttu-id="304bb-207">アプリケーションは、2 つの Web ロールと 1 つのworker ロールで構成されています。</span><span class="sxs-lookup"><span data-stu-id="304bb-207">The application consists of two web roles and a worker role.</span></span>

- <span data-ttu-id="304bb-208">**Tailspin.Web** Web ロールは、Tailspin の顧客が調査の作成と管理に使用する ASP.NET Web サイトをホストしています。</span><span class="sxs-lookup"><span data-stu-id="304bb-208">The **Tailspin.Web** web role hosts an ASP.NET website that Tailspin customers use to create and manage surveys.</span></span> <span data-ttu-id="304bb-209">顧客は、アプリケーションへのサインアップとサブスクリプションの管理にもこの Web サイトを使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-209">Customers also use this website to sign up for the application and manage their subscriptions.</span></span> <span data-ttu-id="304bb-210">最後に、Tailspin の管理者は、このロールを使用してテナントのリストを表示し、テナント データを管理できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-210">Finally, Tailspin administrators can use it to see the list of tenants and manage tenant data.</span></span> 

- <span data-ttu-id="304bb-211">**Tailspin.Web.Survey.Public** Web ロールは、Tailspin の顧客が公開する調査に人々が参加できる ASP.NET web サイトをホストしています。</span><span class="sxs-lookup"><span data-stu-id="304bb-211">The **Tailspin.Web.Survey.Public** web role hosts an ASP.NET website where people can take the surveys that Tailspin customers publish.</span></span> 

- <span data-ttu-id="304bb-212">**Tailspin.Workers.Survey** worker ロールは、バックグラウンド処理を行います。</span><span class="sxs-lookup"><span data-stu-id="304bb-212">The **Tailspin.Workers.Survey** worker role does background processing.</span></span> <span data-ttu-id="304bb-213">Web ロールはキューに作業項目を入れ、worker ロールが項目を処理します。</span><span class="sxs-lookup"><span data-stu-id="304bb-213">The web roles put work items onto a queue, and the worker role processes the items.</span></span> <span data-ttu-id="304bb-214">2 つのバックグラウンド タスクが定義されています。Azure SQL Database に調査の回答をエクスポートするタスクと、調査の回答の統計を計算するタスクです。</span><span class="sxs-lookup"><span data-stu-id="304bb-214">Two background tasks are defined: Exporting survey answers to Azure SQL Database, and calculating statistics for survey answers.</span></span>

<span data-ttu-id="304bb-215">Cloud Services に加えて、Surveys アプリケーションが他のいくつかの Azure サービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-215">In addition to Cloud Services, the Surveys application uses some other Azure services:</span></span>

- <span data-ttu-id="304bb-216">調査、調査の回答、テナント情報を格納する **Azure Storage**。</span><span class="sxs-lookup"><span data-stu-id="304bb-216">**Azure Storage** to store surveys, surveys answers, and tenant information.</span></span>

- <span data-ttu-id="304bb-217">高速な読み取りアクセスのために Azure Storage に格納されているデータの一部をキャッシュする **Azure Redis Cache**。</span><span class="sxs-lookup"><span data-stu-id="304bb-217">**Azure Redis Cache** to cache some of the data that is stored in Azure Storage, for faster read access.</span></span> 

- <span data-ttu-id="304bb-218">顧客と Tailspin 管理者を認証する **Azure Active Directory** (Azure AD)。</span><span class="sxs-lookup"><span data-stu-id="304bb-218">**Azure Active Directory** (Azure AD) to authenticate customers and Tailspin administrators.</span></span>

- <span data-ttu-id="304bb-219">分析のために調査の回答を格納する **Azure SQL Database**。</span><span class="sxs-lookup"><span data-stu-id="304bb-219">**Azure SQL Database** to store the survey answers for analysis.</span></span> 

## <a name="moving-to-service-fabric"></a><span data-ttu-id="304bb-220">Service Fabric への移行</span><span class="sxs-lookup"><span data-stu-id="304bb-220">Moving to Service Fabric</span></span>

<span data-ttu-id="304bb-221">前述のように、このフェーズの目的は、必要最低限の変更で Service Fabric に移行することでした。</span><span class="sxs-lookup"><span data-stu-id="304bb-221">As mentioned, the goal of this phase was migrating to Service Fabric with the minimum necessary changes.</span></span> <span data-ttu-id="304bb-222">そのために、元のアプリケーションの各クラウド サービス ロールに対応するステートレス サービスを作成しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-222">To that end, we created stateless services corresponding to each cloud service role in the original application:</span></span>

![](./images/tailspin02.png)

<span data-ttu-id="304bb-223">意図的に、このアーキテクチャは元のアプリケーションによく似たものにしています。</span><span class="sxs-lookup"><span data-stu-id="304bb-223">Intentionally, this architecture is very similar to the original application.</span></span> <span data-ttu-id="304bb-224">ただし、図にはいくつかの重要な違いが隠れています。</span><span class="sxs-lookup"><span data-stu-id="304bb-224">However, the diagram hides some important differences.</span></span> <span data-ttu-id="304bb-225">この記事の残りの部分では、それらの相違点について説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-225">In the rest of this article, we'll explore those differences.</span></span> 


## <a name="converting-the-cloud-service-roles-to-services"></a><span data-ttu-id="304bb-226">クラウド サービス ロールのサービスへの変換</span><span class="sxs-lookup"><span data-stu-id="304bb-226">Converting the cloud service roles to services</span></span>

<span data-ttu-id="304bb-227">前述のように、各クラウド サービス ロールを Service Fabric サービスに移行しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-227">As mentioned, we migrated each cloud service role to a Service Fabric service.</span></span> <span data-ttu-id="304bb-228">クラウド サービス ロールはステートレスであるため、このフェーズでは Service Fabric 内にステートレス サービスを作成するのが合理的でした。</span><span class="sxs-lookup"><span data-stu-id="304bb-228">Because cloud service roles are stateless, for this phase it made sense to create stateless services in Service Fabric.</span></span> 

<span data-ttu-id="304bb-229">移行については、「[Web と worker ロールを Service Fabric ステートレス サービスに変換する手順][sf-migration]」で概要が説明されているステップに従いました。</span><span class="sxs-lookup"><span data-stu-id="304bb-229">For the migration, we followed the steps outlined in [Guide to converting Web and Worker Roles to Service Fabric stateless services][sf-migration].</span></span> 

### <a name="creating-the-web-front-end-services"></a><span data-ttu-id="304bb-230">Web フロント エンド サービスの作成</span><span class="sxs-lookup"><span data-stu-id="304bb-230">Creating the web front-end services</span></span>

<span data-ttu-id="304bb-231">Service Fabric 内では、サービスは Service Fabric ランタイムによって作成されるプロセス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-231">In Service Fabric, a service runs inside a process created by the Service Fabric runtime.</span></span> <span data-ttu-id="304bb-232">Web フロント エンドの場合、サービスは IIS 内で実行されていないことになります。</span><span class="sxs-lookup"><span data-stu-id="304bb-232">For a web front end, that means the service is not running inside IIS.</span></span> <span data-ttu-id="304bb-233">代わりに、サービスは Web サーバーをホストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-233">Instead, the service must host a web server.</span></span> <span data-ttu-id="304bb-234">プロセス内で実行されるコードは Web サーバー ホストとして機能するため、このアプローチは*自己ホスト*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-234">This approach is called *self-hosting*, because the code that runs inside the process acts as the web server host.</span></span> 

<span data-ttu-id="304bb-235">自己ホストの要件により、Service Fabric サービスは ASP.NET MVC や ASP.NET Web フォームを使用できないことになります。これらのフレームワークは IIS を必要とし、自己ホストをサポートしないためです。</span><span class="sxs-lookup"><span data-stu-id="304bb-235">The requirement to self-host means that a Service Fabric service can't use ASP.NET MVC or ASP.NET Web Forms, because those frameworks require IIS and do not support self-hosting.</span></span> <span data-ttu-id="304bb-236">自己ホストには次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="304bb-236">Options for self-hosting include:</span></span>

- <span data-ttu-id="304bb-237">[ASP.NET Core][aspnet-core] ([Kestrel][kestrel] Web サーバーを使用して自己ホストされる)。</span><span class="sxs-lookup"><span data-stu-id="304bb-237">[ASP.NET Core][aspnet-core], self-hosted using the [Kestrel][kestrel] web server.</span></span> 
- <span data-ttu-id="304bb-238">[ASP.NET Web API][aspnet-webapi] ([OWIN][owin] して自己ホストされる)。</span><span class="sxs-lookup"><span data-stu-id="304bb-238">[ASP.NET Web API][aspnet-webapi], self-hosted using [OWIN][owin].</span></span>
- <span data-ttu-id="304bb-239">サードパーティ フレームワーク ([Nancy](http://nancyfx.org/) など)。</span><span class="sxs-lookup"><span data-stu-id="304bb-239">Third-party frameworks such as [Nancy](http://nancyfx.org/).</span></span>

<span data-ttu-id="304bb-240">元の Surveys アプリケーションは ASP.NET MVC を使用しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-240">The original Surveys application uses ASP.NET MVC.</span></span> <span data-ttu-id="304bb-241">ASP.NET MVC は Service Fabric で自己ホストすることはできないため、以下の移行オプションを検討しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-241">Because ASP.NET MVC cannot be self-hosted in Service Fabric, we considered the following migration options:</span></span>

- <span data-ttu-id="304bb-242">Web ロールを、自己ホストが可能な ASP.NET Core に移植する。</span><span class="sxs-lookup"><span data-stu-id="304bb-242">Port the web roles to ASP.NET Core, which can be self-hosted.</span></span>
- <span data-ttu-id="304bb-243">Web サイトを、ASP.NET Web API を使用して実装された Web API を呼び出す単一ページ アプリケーション (SPA) に変換する。</span><span class="sxs-lookup"><span data-stu-id="304bb-243">Convert the web site into a single-page application (SPA) that calls a web API implemented using ASP.NET Web API.</span></span> <span data-ttu-id="304bb-244">このためには、Web フロント エンドの完全な再設計が必要でした。</span><span class="sxs-lookup"><span data-stu-id="304bb-244">This would have required a complete redesign of the web front end.</span></span>
- <span data-ttu-id="304bb-245">ASP.NET MVC の既存のコードを保持し、Windows Server コンテナー内の IIS を Service Fabric にデプロイする。</span><span class="sxs-lookup"><span data-stu-id="304bb-245">Keep the existing ASP.NET MVC code and deploy IIS in a Windows Server container to Service Fabric.</span></span> <span data-ttu-id="304bb-246">このアプローチでは、コード変更の必要がほとんどないかまったくありません。</span><span class="sxs-lookup"><span data-stu-id="304bb-246">This approach would require little or no code change.</span></span> 

<span data-ttu-id="304bb-247">ASP.NET Core に移植するという最初のオプションを使用すると、ASP.NET Core の最新機能を利用できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-247">The first option, porting to ASP.NET Core, allowed us to take advantage of the latest features in ASP.NET Core.</span></span> <span data-ttu-id="304bb-248">この変換を行うために、[ASP.NET MVC から ASP.NET Core MVC への移行][aspnet-migration]に関するページに記載されている手順に従いました。</span><span class="sxs-lookup"><span data-stu-id="304bb-248">To do the conversion, we followed the steps described in [Migrating From ASP.NET MVC to ASP.NET Core MVC][aspnet-migration].</span></span> 

> [!NOTE]
> <span data-ttu-id="304bb-249">Kestrel と共に ASP.NET Core を使用する場合は、セキュリティ上の理由から、Kestrel の前にリバース プロキシを配置し、インターネットからのトラフィックを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-249">When using ASP.NET Core with Kestrel, you should place a reverse proxy in front of Kestrel to handle traffic from the Internet, for security reasons.</span></span> <span data-ttu-id="304bb-250">詳細については、「[Kestrel web server implementation in ASP.NET Core (ASP.NET Core での Kestrel Web サーバーの実装)][kestrel]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-250">For more information, see [Kestrel web server implementation in ASP.NET Core][kestrel].</span></span> <span data-ttu-id="304bb-251">「[アプリケーションのデプロイ](#deploying-the-application)」のセクションでは、推奨される Azure のデプロイについて説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-251">The section [Deploying the application](#deploying-the-application) describes a recommended Azure deployment.</span></span>

### <a name="http-listeners"></a><span data-ttu-id="304bb-252">HTTP リスナー</span><span class="sxs-lookup"><span data-stu-id="304bb-252">HTTP listeners</span></span>

<span data-ttu-id="304bb-253">Cloud Services では、[サービス定義ファイル][cloud-service-endpoints]で HTTP エンドポイントを宣言することで、Web ロールまたは worker ロールが HTTP エンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="304bb-253">In Cloud Services, a web or worker role exposes an HTTP endpoint by declaring it in the [service definition file][cloud-service-endpoints].</span></span> <span data-ttu-id="304bb-254">Web ロールにはエンドポイントが少なくとも 1 つ必要です。</span><span class="sxs-lookup"><span data-stu-id="304bb-254">A web role must have at least one endpoint.</span></span>

```xml
<!-- Cloud service endpoint -->
<Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
</Endpoints>
```

<span data-ttu-id="304bb-255">同様に、Service Fabric エンドポイントはサービス マニフェストで宣言されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-255">Similarly, Service Fabric endpoints are declared in a service manifest:</span></span> 

```xml
<!-- Service Fabric endpoint -->
<Endpoints>
    <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8002" />
</Endpoints>
```

<span data-ttu-id="304bb-256">クラウド サービス ロールとは異なり、Service Fabric のサービスは同じノード内に併置できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-256">Unlike a cloud service role, however, Service Fabric services can be co-located within the same node.</span></span> <span data-ttu-id="304bb-257">そのため、すべてのサービスは別個のポートでリッスンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-257">Therefore, every service must listen on a distinct port.</span></span> <span data-ttu-id="304bb-258">この記事の後方で、ポート 80 またはポート 443 上のクライアント要求が、サービスの正しいポートにルーティングされるようにする方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="304bb-258">Later in this article, we'll discuss how client requests on port 80 or port 443 get routed to the correct port for the service.</span></span>

<span data-ttu-id="304bb-259">サービスは、各エンドポイント用のリスナーを明示的に作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-259">A service must explicitly create listeners for each endpoint.</span></span> <span data-ttu-id="304bb-260">Service Fabric は通信スタックを区別しないことがその理由です。</span><span class="sxs-lookup"><span data-stu-id="304bb-260">The reason is that Service Fabric is agnostic about communication stacks.</span></span> <span data-ttu-id="304bb-261">詳細については、「[ASP.NET Core を使用したアプリケーション用の Web サービス フロントエンドの構築][sf-aspnet-core]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-261">For more information, see [Build a web service front end for your application using ASP.NET Core][sf-aspnet-core].</span></span>

## <a name="packaging-and-configuration"></a><span data-ttu-id="304bb-262">パッケージと構成</span><span class="sxs-lookup"><span data-stu-id="304bb-262">Packaging and configuration</span></span>

 <span data-ttu-id="304bb-263">1 つのクラウド サービスには、次の構成とパッケージ ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-263">A cloud service contains the following configuration and package files:</span></span>

| <span data-ttu-id="304bb-264">ファイル</span><span class="sxs-lookup"><span data-stu-id="304bb-264">File</span></span> | <span data-ttu-id="304bb-265">説明</span><span class="sxs-lookup"><span data-stu-id="304bb-265">Description</span></span> |
|------|-------------|
| <span data-ttu-id="304bb-266">サービス定義 (.csdef)</span><span class="sxs-lookup"><span data-stu-id="304bb-266">Service definition (.csdef)</span></span> | <span data-ttu-id="304bb-267">クラウド サービスを構成するために Azure によって使用される設定。</span><span class="sxs-lookup"><span data-stu-id="304bb-267">Settings used by Azure to configure the cloud service.</span></span> <span data-ttu-id="304bb-268">ロール、エンドポイント、スタートアップ タスク、および構成設定の名前を定義します。</span><span class="sxs-lookup"><span data-stu-id="304bb-268">Defines the roles, endpoints, startup tasks, and the names of configuration settings.</span></span> |
| <span data-ttu-id="304bb-269">サービス構成 (.cscfg)</span><span class="sxs-lookup"><span data-stu-id="304bb-269">Service configuration (.cscfg)</span></span> | <span data-ttu-id="304bb-270">ロール インスタンスの数、エンドポイントのポート番号、構成設定の値を含むデプロイごとの設定。</span><span class="sxs-lookup"><span data-stu-id="304bb-270">Per-deployment settings, including the number of role instances, endpoint port numbers, and the values of configuration settings.</span></span> 
| <span data-ttu-id="304bb-271">サービス パッケージ (.cspkg)</span><span class="sxs-lookup"><span data-stu-id="304bb-271">Service package (.cspkg)</span></span> | <span data-ttu-id="304bb-272">アプリケーション コード、構成、およびサービス定義ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-272">Contains the application code and configurations, and the service definition file.</span></span>  |

<span data-ttu-id="304bb-273">アプリケーション全体に対して 1 つの .csdef ファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="304bb-273">There is one .csdef file for the entire application.</span></span> <span data-ttu-id="304bb-274">ローカル、テスト、運用など、異なる環境のために複数の .cscfg ファイルを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="304bb-274">You can have multiple .cscfg files for different environments, such as local, test, or production.</span></span> <span data-ttu-id="304bb-275">サービスの実行中、.cscfg は更新可能ですが、.csdef は更新できません。</span><span class="sxs-lookup"><span data-stu-id="304bb-275">When the service is running, you can update the .cscfg but not the .csdef.</span></span> <span data-ttu-id="304bb-276">詳細については、「[クラウド サービス モデルとそのパッケージ化について][cloud-service-config]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-276">For more information, see [What is the Cloud Service model and how do I package it?][cloud-service-config]</span></span>

<span data-ttu-id="304bb-277">Service Fabric では、サービス*定義*とサービス*設定*の間に同様の区別がありますが、構造の粒度はより高くなっています。</span><span class="sxs-lookup"><span data-stu-id="304bb-277">Service Fabric has a similar division between a service *definition* and service *settings*, but the structure is more granular.</span></span> <span data-ttu-id="304bb-278">Service Fabric の構成モデルを理解するには、Service Fabric アプリケーションがどのようにパッケージ化されるかを理解すると役立ちます。</span><span class="sxs-lookup"><span data-stu-id="304bb-278">To understand Service Fabric's configuration model, it helps to understand how a Service Fabric application is packaged.</span></span> <span data-ttu-id="304bb-279">構造は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="304bb-279">Here is the structure:</span></span>

```
Application package
  - Service packages
    - Code package
    - Configuration package
    - Data package (optional)
```

<span data-ttu-id="304bb-280">デプロイするのはアプリケーション パッケージです。</span><span class="sxs-lookup"><span data-stu-id="304bb-280">The application package is what you deploy.</span></span> <span data-ttu-id="304bb-281">1 つまたは複数のサービス パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="304bb-281">It contains one or more service packages.</span></span> <span data-ttu-id="304bb-282">1 つのサービス パッケージには、コード、構成、およびデータのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-282">A service package contains code, configuration, and data packages.</span></span> <span data-ttu-id="304bb-283">コード パッケージにはサービスのバイナリが含まれていて、構成パッケージには構成設定が含まれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-283">The code package contains the binaries for the services, and the configuration package contains configuration settings.</span></span> <span data-ttu-id="304bb-284">このモデルでは、アプリケーション全体を再デプロイしなくても個々のサービスをアップグレードできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-284">This model allows you to upgrade individual services without redeploying the entire application.</span></span> <span data-ttu-id="304bb-285">また、コードの再デプロイやサービスの再起動を行わず、構成設定のみを更新できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-285">It also lets you update just the configuration settings, without redeploying the code or restarting the service.</span></span>

<span data-ttu-id="304bb-286">Service Fabric アプリケーションには以下の構成ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="304bb-286">A Service Fabric application contains the following configuration files:</span></span>

| <span data-ttu-id="304bb-287">ファイル</span><span class="sxs-lookup"><span data-stu-id="304bb-287">File</span></span> | <span data-ttu-id="304bb-288">Location</span><span class="sxs-lookup"><span data-stu-id="304bb-288">Location</span></span> | <span data-ttu-id="304bb-289">説明</span><span class="sxs-lookup"><span data-stu-id="304bb-289">Description</span></span> |
|------|----------|-------------|
| <span data-ttu-id="304bb-290">ApplicationManifest.xml</span><span class="sxs-lookup"><span data-stu-id="304bb-290">ApplicationManifest.xml</span></span> | <span data-ttu-id="304bb-291">アプリケーション パッケージ</span><span class="sxs-lookup"><span data-stu-id="304bb-291">Application package</span></span> | <span data-ttu-id="304bb-292">アプリケーションを構成するサービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="304bb-292">Defines the services that compose the application.</span></span> |
| <span data-ttu-id="304bb-293">ServiceManifest.xml</span><span class="sxs-lookup"><span data-stu-id="304bb-293">ServiceManifest.xml</span></span> | <span data-ttu-id="304bb-294">サービス パッケージ</span><span class="sxs-lookup"><span data-stu-id="304bb-294">Service package</span></span>| <span data-ttu-id="304bb-295">1 つまたは複数のサービスについて記述します。</span><span class="sxs-lookup"><span data-stu-id="304bb-295">Describes one or more services.</span></span> |
| <span data-ttu-id="304bb-296">Settings.xml</span><span class="sxs-lookup"><span data-stu-id="304bb-296">Settings.xml</span></span> | <span data-ttu-id="304bb-297">構成パッケージ</span><span class="sxs-lookup"><span data-stu-id="304bb-297">Configuration package</span></span> | <span data-ttu-id="304bb-298">サービス パッケージで定義されているサービスの構成設定が含まれます。</span><span class="sxs-lookup"><span data-stu-id="304bb-298">Contains configuration settings for the services defined in the service package.</span></span> |

<span data-ttu-id="304bb-299">詳細については、「[Service Fabric のアプリケーションのモデル化][sf-application-model]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-299">For more information, see [Model an application in Service Fabric][sf-application-model].</span></span>

<span data-ttu-id="304bb-300">複数の環境の異なる構成設定をサポートするには、「[複数の環境のアプリケーション パラメーターを管理する][sf-multiple-environments]」で説明されている次のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-300">To support different configuration settings for multiple environments, use the following approach, described in [Manage application parameters for multiple environments][sf-multiple-environments]:</span></span>

1. <span data-ttu-id="304bb-301">サービスの Setting.xml ファイルで設定を定義します。</span><span class="sxs-lookup"><span data-stu-id="304bb-301">Define the setting in the Setting.xml file for the service.</span></span>
2. <span data-ttu-id="304bb-302">アプリケーション マニフェストで、設定のオーバーライドを定義します。</span><span class="sxs-lookup"><span data-stu-id="304bb-302">In the application manifest, define an override for the setting.</span></span>
3. <span data-ttu-id="304bb-303">アプリケーション パラメーター ファイルに環境固有の設定を記入します。</span><span class="sxs-lookup"><span data-stu-id="304bb-303">Put environment-specific settings into application parameter files.</span></span>


## <a name="deploying-the-application"></a><span data-ttu-id="304bb-304">アプリケーションのデプロイ</span><span class="sxs-lookup"><span data-stu-id="304bb-304">Deploying the application</span></span>

<span data-ttu-id="304bb-305">Azure Cloud Services は管理されたサービスであるのに対して、Service Fabric はランタイムです。</span><span class="sxs-lookup"><span data-stu-id="304bb-305">Whereas Azure Cloud Services is a managed service, Service Fabric is a runtime.</span></span> <span data-ttu-id="304bb-306">Azure とオンプレミスを含めて、多くの環境で Service Fabric クラスターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-306">You can create Service Fabric clusters in many environments, including Azure and on premises.</span></span> <span data-ttu-id="304bb-307">この記事では、Azure へのデプロイに焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="304bb-307">In this article, we focus on deploying to Azure.</span></span> 

<span data-ttu-id="304bb-308">次の図は、推奨されるデプロイを示しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-308">The following diagram shows a recommended deployment:</span></span>

![](./images/tailspin-cluster.png)

<span data-ttu-id="304bb-309">Service Fabric クラスターは [VM スケール セット][vm-scale-sets]にデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="304bb-309">The Service Fabric cluster is deployed to a [VM scale set][vm-scale-sets].</span></span> <span data-ttu-id="304bb-310">スケール セットは、まったく同じ VM のセットをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。</span><span class="sxs-lookup"><span data-stu-id="304bb-310">Scale sets are an Azure Compute resource that can be used to deploy and manage a set of identical VMs.</span></span> 

<span data-ttu-id="304bb-311">前述のように、Kestrel Web サーバーには、セキュリティ上の理由でリバース プロキシが必要です。</span><span class="sxs-lookup"><span data-stu-id="304bb-311">As mentioned, the Kestrel web server requires a reverse proxy for security reasons.</span></span> <span data-ttu-id="304bb-312">この図は、さまざまな第 7 層負荷分散機能を提供する Azure サービスである [Azure Application Gateway][application-gateway] を示しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-312">This diagram shows [Azure Application Gateway][application-gateway], which is an Azure service that offers various layer 7 load balancing capabilities.</span></span> <span data-ttu-id="304bb-313">これは、クライアント接続を終了して、バックエンドのエンドポイントへの要求を転送し、リバース プロキシ サービスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="304bb-313">It acts as a reverse-proxy service, terminating the client connection and forwarding requests to back-end endpoints.</span></span> <span data-ttu-id="304bb-314">nginx など、異なるリバース プロキシ ソリューションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-314">You might use a different reverse proxy solution, such as nginx.</span></span>  

### <a name="layer-7-routing"></a><span data-ttu-id="304bb-315">第 7 層のルーティング</span><span class="sxs-lookup"><span data-stu-id="304bb-315">Layer 7 routing</span></span>

<span data-ttu-id="304bb-316">[元の Surveys アプリケーション](https://msdn.microsoft.com/library/hh534477.aspx#sec21)では、1 つの Web ロールがポート 80 でリッスンし、別の Web ロールがポート 443 でリッスンしていました。</span><span class="sxs-lookup"><span data-stu-id="304bb-316">In the [original Surveys application](https://msdn.microsoft.com/library/hh534477.aspx#sec21), one web role listened on port 80, and the other web role listened on port 443.</span></span> 

| <span data-ttu-id="304bb-317">公開サイト</span><span class="sxs-lookup"><span data-stu-id="304bb-317">Public site</span></span> | <span data-ttu-id="304bb-318">Survey 管理サイト</span><span class="sxs-lookup"><span data-stu-id="304bb-318">Survey management site</span></span> |
|-------------|------------------------|
| `http://tailspin.cloudapp.net` | `https://tailspin.cloudapp.net` |

<span data-ttu-id="304bb-319">その他に、第 7 層のルーティングを使用するオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="304bb-319">Another option is to use layer 7 routing.</span></span> <span data-ttu-id="304bb-320">このアプローチでは、異なる URL パスはバック エンドの異なるポート番号にルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="304bb-320">In this approach, different URL paths get routed to different port numbers on the back end.</span></span> <span data-ttu-id="304bb-321">たとえば、公開サイトには `/public/` で始まる URL パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-321">For example, the public site might use URL paths starting with `/public/`.</span></span> 

<span data-ttu-id="304bb-322">第 7 層のルーティングには以下のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="304bb-322">Options for layer 7 routing include:</span></span>

- <span data-ttu-id="304bb-323">Application Gateway を使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-323">Use Application Gateway.</span></span> 

- <span data-ttu-id="304bb-324">nginx などのネットワーク仮想アプライアンス (NVA) を使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-324">Use a network virtual appliance (NVA), such as nginx.</span></span>

- <span data-ttu-id="304bb-325">カスタム ゲートウェイをステートレス サービスとして記述します。</span><span class="sxs-lookup"><span data-stu-id="304bb-325">Write a custom gateway as a stateless service.</span></span>

<span data-ttu-id="304bb-326">公開の HTTP エンドポイントを持つサービスが 2 つ以上あっても、1 つのドメイン名を持つ 1 つのサイトとして表示する場合は、このアプローチを検討してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-326">Consider this approach if you have two or more services with public HTTP endpoints, but want them to appear as one site with a single domain name.</span></span>

> <span data-ttu-id="304bb-327">お勧め*しない*アプローチの 1 つは、外部クライアントが Service Fabric の[リバース プロキシ][sf-reverse-proxy]経由で要求を送信するのを許可することです。</span><span class="sxs-lookup"><span data-stu-id="304bb-327">One approach that we *don't* recommend is allowing external clients to send requests through the Service Fabric [reverse proxy][sf-reverse-proxy].</span></span> <span data-ttu-id="304bb-328">これは可能ですが、リバース プロキシはサービス間の通信を目的としています。</span><span class="sxs-lookup"><span data-stu-id="304bb-328">Although this is possible, the reverse proxy is intended for service-to-service communication.</span></span> <span data-ttu-id="304bb-329">これを外部のクライアントに開くことで、クラスター内で実行されている、HTTP エンドポイントを持つサービスを*すべて*公開することになります。</span><span class="sxs-lookup"><span data-stu-id="304bb-329">Opening it to external clients exposes *any* service running in the cluster that has an HTTP endpoint.</span></span>

### <a name="node-types-and-placement-constraints"></a><span data-ttu-id="304bb-330">ノードの種類と配置の制約</span><span class="sxs-lookup"><span data-stu-id="304bb-330">Node types and placement constraints</span></span>

<span data-ttu-id="304bb-331">前に示したデプロイでは、すべてのサービスがすべてのノードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-331">In the deployment shown above, all the services run on all the nodes.</span></span> <span data-ttu-id="304bb-332">ただし、サービスをグループ化して、特定のサービスがクラスター内の特定のノードでのみ実行されるようにもできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-332">However, you can also group services, so that certain services run only on particular nodes within the cluster.</span></span> <span data-ttu-id="304bb-333">このアプローチは、次のような理由で使用します。</span><span class="sxs-lookup"><span data-stu-id="304bb-333">Reasons to use this approach include:</span></span>

- <span data-ttu-id="304bb-334">タイプが異なる VM タイプで一部のサービスを実行する。</span><span class="sxs-lookup"><span data-stu-id="304bb-334">Run some services on different VM types.</span></span> <span data-ttu-id="304bb-335">たとえば、一部のサービスがコンピューティング集中型であったり、GPU を必要としたりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-335">For example, some services might be compute-intensive or require GPUs.</span></span> <span data-ttu-id="304bb-336">Service Fabric クラスター内には、複数タイプの VM を混在できます。</span><span class="sxs-lookup"><span data-stu-id="304bb-336">You can have a mix of VM types in your Service Fabric cluster.</span></span>
- <span data-ttu-id="304bb-337">セキュリティ上の理由で、バックエンド サービスからフロントエンド サービスを分離する。</span><span class="sxs-lookup"><span data-stu-id="304bb-337">Isolate front-end services from back-end services, for security reasons.</span></span> <span data-ttu-id="304bb-338">すべてのフロントエンド サービスは 1 セットのノードで実行され、バックエンド サービスは同じクラスター内の異なるノードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-338">All the front-end services will run on one set of nodes, and the back-end services will run on different nodes in the same cluster.</span></span>
- <span data-ttu-id="304bb-339">スケール要件が異なる。</span><span class="sxs-lookup"><span data-stu-id="304bb-339">Different scale requirements.</span></span> <span data-ttu-id="304bb-340">一部のサービスは、他のサービスよりも多くのノードで実行する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-340">Some services might need to run on more nodes than other services.</span></span> <span data-ttu-id="304bb-341">たとえば、フロントエンド ノードとバックエンド ノードを定義する場合、各セットを個別にスケールできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-341">For example, if you define front-end nodes and back-end nodes, each set can be scaled independently.</span></span>

<span data-ttu-id="304bb-342">次の図は、フロントエンド サービスとバックエンド サービスを分けたクラスターを示しています。</span><span class="sxs-lookup"><span data-stu-id="304bb-342">The following diagram shows a cluster that separates front-end and back-end services:</span></span>

![](././images/node-placement.png)

<span data-ttu-id="304bb-343">このアプローチの実装方法:</span><span class="sxs-lookup"><span data-stu-id="304bb-343">To implement this approach:</span></span>

1.  <span data-ttu-id="304bb-344">クラスターを作成するときに、ノード タイプを 2 つ以上定義します。</span><span class="sxs-lookup"><span data-stu-id="304bb-344">When you create the cluster, define two or more node types.</span></span> 
2.  <span data-ttu-id="304bb-345">サービスごとに[配置の制約][sf-placement-constraints]を使用して、サービスをノード タイプに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="304bb-345">For each service, use [placement constraints][sf-placement-constraints] to assign the service to a node type.</span></span>

<span data-ttu-id="304bb-346">Azure にデプロイするときに、各ノード タイプは別個の VM スケール セットにデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="304bb-346">When you deploy to Azure, each node type is deployed to a separate VM scale set.</span></span> <span data-ttu-id="304bb-347">Service Fabric クラスターは、すべてのノード タイプにまたがります。</span><span class="sxs-lookup"><span data-stu-id="304bb-347">The Service Fabric cluster spans all node types.</span></span> <span data-ttu-id="304bb-348">詳細については、「[Azure Service Fabric ノードの種類と仮想マシン スケール セット][sf-node-types]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-348">For more information, see [The relationship between Service Fabric node types and Virtual Machine Scale Sets][sf-node-types].</span></span>

> <span data-ttu-id="304bb-349">クラスターに複数のノード タイプがある場合、1 つのノード タイプが*プライマリ* ノード タイプとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-349">If a cluster has multiple node types, one node type is designated as the *primary* node type.</span></span> <span data-ttu-id="304bb-350">Cluster Management Service などの Service Fabric ランタイム サービスは、プライマリ ノード タイプで実行されます。</span><span class="sxs-lookup"><span data-stu-id="304bb-350">Service Fabric runtime services, such as the Cluster Management Service, run on the primary node type.</span></span> <span data-ttu-id="304bb-351">運用環境では、プライマリ ノード タイプには少なくとも 5 つのノードをプロビジョニングしてください。</span><span class="sxs-lookup"><span data-stu-id="304bb-351">Provision at least 5 nodes for the primary node type in a production environment.</span></span> <span data-ttu-id="304bb-352">その他のノード タイプには、少なくとも 2 つのノードが必要です。</span><span class="sxs-lookup"><span data-stu-id="304bb-352">The other node type should have at least 2 nodes.</span></span>

## <a name="configuring-and-managing-the-cluster"></a><span data-ttu-id="304bb-353">クラスターの構成と管理</span><span class="sxs-lookup"><span data-stu-id="304bb-353">Configuring and managing the cluster</span></span>

<span data-ttu-id="304bb-354">クラスターをセキュリティで保護し、承認されていないユーザーがクラスターに接続しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-354">Clusters must be secured to prevent unauthorized users from connecting to your cluster.</span></span> <span data-ttu-id="304bb-355">クライアントの認証には Azure AD を、ノード間のセキュリティのためには X.509 証明書を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="304bb-355">It is recommended to use Azure AD to authenticate clients, and X.509 certificates for node-to-node security.</span></span> <span data-ttu-id="304bb-356">詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ][sf-security]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-356">For more information, see [Service Fabric cluster security scenarios][sf-security].</span></span>

<span data-ttu-id="304bb-357">公開の HTTPS エンドポイントを構成するには、「[サービス マニフェストにリソースを指定する][sf-manifest-resources]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-357">To configure a public HTTPS endpoint, see [Specify resources in a service manifest][sf-manifest-resources].</span></span>

<span data-ttu-id="304bb-358">クラスターに VM を追加することで、アプリケーションをスケールアウトできます。</span><span class="sxs-lookup"><span data-stu-id="304bb-358">You can scale out the application by adding VMs to the cluster.</span></span> <span data-ttu-id="304bb-359">VM スケール セットは、パフォーマンス カウンターに基づく自動スケール ルールを使用した自動スケールをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="304bb-359">VM scale sets support auto-scaling using auto-scale rules based on performance counters.</span></span> <span data-ttu-id="304bb-360">詳細については、「[自動スケール ルールを使用した Service Fabric クラスターのスケールインとスケールアウト][sf-auto-scale]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-360">For more information, see [Scale a Service Fabric cluster in or out using auto-scale rules][sf-auto-scale].</span></span>

<span data-ttu-id="304bb-361">クラスターの実行中には、1 つの場所ですべてのノードからログを収集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="304bb-361">While the cluster is running, you should collect logs from all the nodes in a central location.</span></span> <span data-ttu-id="304bb-362">詳細については、[Azure 診断でログを収集する方法][sf-logs]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="304bb-362">For more information, see [Collect logs by using Azure Diagnostics][sf-logs].</span></span>   


## <a name="conclusion"></a><span data-ttu-id="304bb-363">まとめ</span><span class="sxs-lookup"><span data-stu-id="304bb-363">Conclusion</span></span>

<span data-ttu-id="304bb-364">Surveys アプリケーションの Service Fabric への移植は、かなりわかりやすいものでした。</span><span class="sxs-lookup"><span data-stu-id="304bb-364">Porting the Surveys application to Service Fabric was fairly straightforward.</span></span> <span data-ttu-id="304bb-365">要約すると、以下のことを行いました。</span><span class="sxs-lookup"><span data-stu-id="304bb-365">To summarize, we did the following:</span></span>

- <span data-ttu-id="304bb-366">ロールをステートレス サービスに変換しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-366">Converted the roles to stateless services.</span></span>
- <span data-ttu-id="304bb-367">Web フロントエンドを ASP.NET Core に変換しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-367">Converted the web front ends to ASP.NET Core.</span></span>
- <span data-ttu-id="304bb-368">パッケージと構成のファイルを Service Fabric モデルに変更しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-368">Changed the packaging and configuration files to the Service Fabric model.</span></span>

<span data-ttu-id="304bb-369">さらに、デプロイを Cloud Services から VM スケール セットで実行される Service Fabric クラスターに変更しました。</span><span class="sxs-lookup"><span data-stu-id="304bb-369">In addition, the deployment changed from Cloud Services to a Service Fabric cluster running in a VM Scale Set.</span></span>

## <a name="next-steps"></a><span data-ttu-id="304bb-370">次の手順</span><span class="sxs-lookup"><span data-stu-id="304bb-370">Next steps</span></span>

<span data-ttu-id="304bb-371">Survey アプリケーションは適切に移植されました。Tailspin が次に求めているのは、独立したサービスのデプロイ、バージョン管理など、Service Fabric 機能を利用することです。</span><span class="sxs-lookup"><span data-stu-id="304bb-371">Now that the Surveys application has been successfully ported, Tailspin wants to take advantage of Service Fabric features such as independent service deployment and versioning.</span></span> <span data-ttu-id="304bb-372">「[Refactor an Azure Service Fabric Application migrated from Azure Cloud Services (Azure Cloud Services から移行した Azure Service Fabric アプリケーションをリファクタリングする)][refactor-surveys]」では、Tailspin が、このような Service Fabric 機能を利用するために、こうしたサービスをどのように詳細なアーキテクチャに分解したかを説明しています</span><span class="sxs-lookup"><span data-stu-id="304bb-372">Learn how Tailspin decomposed these services to a more granular architecture to take advantage of these Service Fabric features in [Refactor an Azure Service Fabric Application migrated from Azure Cloud Services][refactor-surveys]</span></span>

<!-- links -->

[application-gateway]: /azure/application-gateway/
[aspnet-core]: /aspnet/core/
[aspnet-webapi]: https://www.asp.net/web-api
[aspnet-migration]: /aspnet/core/migration/mvc
[aspnet-hosting]: /aspnet/core/fundamentals/hosting
[aspnet-webapi]: https://www.asp.net/web-api
[azure-deployment-models]: /azure/azure-resource-manager/resource-manager-deployment-model
[cloud-service-autoscale]: /azure/cloud-services/cloud-services-how-to-scale-portal
[cloud-service-config]: /azure/cloud-services/cloud-services-model-and-package
[cloud-service-endpoints]: /azure/cloud-services/cloud-services-enable-communication-role-instances#worker-roles-vs-web-roles
[kestrel]: /aspnet/core/fundamentals/servers/kestrel
[lb-probes]: /azure/load-balancer/load-balancer-custom-probe-overview
[owin]: https://www.asp.net/aspnet/overview/owin-and-katana
[refactor-surveys]: refactor-migrated-app.md
[sample-code]: https://github.com/mspnp/cloud-services-to-service-fabric
[sf-application-model]: /azure/service-fabric/service-fabric-application-model
[sf-aspnet-core]: /azure/service-fabric/service-fabric-add-a-web-frontend
[sf-auto-scale]: /azure/service-fabric/service-fabric-cluster-scale-up-down
[sf-compare-cloud-services]: /azure/service-fabric/service-fabric-cloud-services-migration-differences
[sf-connect-and-communicate]: /azure/service-fabric/service-fabric-connect-and-communicate-with-services
[sf-containers]: /azure/service-fabric/service-fabric-containers-overview
[sf-logs]: /azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad
[sf-manifest-resources]: /azure/service-fabric/service-fabric-service-manifest-resources
[sf-migration]: /azure/service-fabric/service-fabric-cloud-services-migration-worker-role-stateless-service
[sf-multiple-environments]: /azure/service-fabric/service-fabric-manage-multiple-environment-app-configuration
[sf-node-types]: /azure/service-fabric/service-fabric-cluster-nodetypes
[sf-overview]: /azure/service-fabric/service-fabric-overview
[sf-placement-constraints]: /azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description
[sf-reliable-collections]: /azure/service-fabric/service-fabric-reliable-services-reliable-collections
[sf-reliable-services]: /azure/service-fabric/service-fabric-reliable-services-introduction
[sf-reverse-proxy]: /azure/service-fabric/service-fabric-reverseproxy
[sf-security]: /azure/service-fabric/service-fabric-cluster-security
[sf-why-microservices]: /azure/service-fabric/service-fabric-overview-microservices
[tailspin-book]: https://msdn.microsoft.com/library/ff966499.aspx
[tailspin-scenario]: https://msdn.microsoft.com/library/hh534482.aspx
[unity]: https://msdn.microsoft.com/library/ff647202.aspx
[vm-scale-sets]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
