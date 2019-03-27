---
title: 基本的な Web アプリケーション
titleSuffix: Azure Reference Architectures
description: Azure で実行する基本的な Web アプリケーションの推奨アーキテクチャ。
author: MikeWasson
ms.date: 12/12/2017
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 95f634284fe821386704174894a85a4dbca815f7
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248667"
---
# <a name="run-a-basic-web-application-in-azure"></a><span data-ttu-id="8ac42-103">Azure で基本的な Web アプリケーションを実行する</span><span class="sxs-lookup"><span data-stu-id="8ac42-103">Run a basic web application in Azure</span></span>

<span data-ttu-id="8ac42-104">この参照アーキテクチャは、[Azure App Service][app-service] と [Azure SQL Database][sql-db] を使用する Web アプリケーションを対象とした実証済みプラクティスを示しています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-104">This reference architecture shows proven practices for a web application that uses [Azure App Service][app-service] and [Azure SQL Database][sql-db].</span></span> <span data-ttu-id="8ac42-105">[**このソリューションをデプロイします**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="8ac42-105">[**Deploy this solution**](#deploy-the-solution).</span></span>

![Azure の基本的な Web アプリケーションの参照アーキテクチャ](./images/basic-web-app.png)

<span data-ttu-id="8ac42-107">"*このアーキテクチャの [Visio ファイル][visio-download]をダウンロードします。*"</span><span class="sxs-lookup"><span data-stu-id="8ac42-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="8ac42-108">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="8ac42-108">Architecture</span></span>

> [!NOTE]
> <span data-ttu-id="8ac42-109">このアーキテクチャでは、アプリケーション開発については説明しません。また、特定のアプリケーション フレームワークを前提としていません。</span><span class="sxs-lookup"><span data-stu-id="8ac42-109">This architecture does not focus on application development, and does not assume any particular application framework.</span></span> <span data-ttu-id="8ac42-110">目的は、さまざまな Azure サービスがどのように組み合わせるかを理解することです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-110">The goal is to understand how various Azure services fit together.</span></span>
>

<span data-ttu-id="8ac42-111">アーキテクチャには次のコンポーネントがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-111">The architecture has the following components:</span></span>

- <span data-ttu-id="8ac42-112">**リソース グループ**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-112">**Resource group**.</span></span> <span data-ttu-id="8ac42-113">[リソース グループ](/azure/azure-resource-manager/resource-group-overview)は、Azure リソースの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-113">A [resource group](/azure/azure-resource-manager/resource-group-overview) is a logical container for Azure resources.</span></span>

- <span data-ttu-id="8ac42-114">**App Service アプリ**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-114">**App Service app**.</span></span> <span data-ttu-id="8ac42-115">[Azure App Service][app-service] は、クラウド アプリケーションを作成およびデプロイするためのフル マネージドのプラットフォームです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-115">[Azure App Service][app-service] is a fully managed platform for creating and deploying cloud applications.</span></span>

- <span data-ttu-id="8ac42-116">**App Service プラン**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-116">**App Service plan**.</span></span> <span data-ttu-id="8ac42-117">[App Service プラン][app-service-plans]は、アプリをホストする管理された仮想マシン (VM) を提供します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-117">An [App Service plan][app-service-plans] provides the managed virtual machines (VMs) that host your app.</span></span> <span data-ttu-id="8ac42-118">プランに関連付けられているすべてのアプリが同じ VM インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-118">All apps associated with a plan run on the same VM instances.</span></span>

- <span data-ttu-id="8ac42-119">**デプロイ スロット**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-119">**Deployment slots**.</span></span>  <span data-ttu-id="8ac42-120">[デプロイ スロット][deployment-slots]を使用すると、デプロイをステージングし、運用環境のデプロイとスワップできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-120">A [deployment slot][deployment-slots] lets you stage a deployment and then swap it with the production deployment.</span></span> <span data-ttu-id="8ac42-121">このようにして、運用環境に直接デプロイするのを回避します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-121">That way, you avoid deploying directly into production.</span></span> <span data-ttu-id="8ac42-122">具体的な推奨事項については、[管理容易性](#manageability-considerations)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-122">See the [Manageability](#manageability-considerations) section for specific recommendations.</span></span>

- <span data-ttu-id="8ac42-123">**IP アドレス**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-123">**IP address**.</span></span> <span data-ttu-id="8ac42-124">App Service アプリには、パブリック IP アドレスとドメイン名があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-124">The App Service app has a public IP address and a domain name.</span></span> <span data-ttu-id="8ac42-125">ドメイン名は `azurewebsites.net` のサブドメインです (`contoso.azurewebsites.net` など)。</span><span class="sxs-lookup"><span data-stu-id="8ac42-125">The domain name is a subdomain of `azurewebsites.net`, such as `contoso.azurewebsites.net`.</span></span>

- <span data-ttu-id="8ac42-126">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-126">**Azure DNS**.</span></span> <span data-ttu-id="8ac42-127">[Azure DNS][azure-dns] は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-127">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="8ac42-128">Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-128">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span> <span data-ttu-id="8ac42-129">カスタム ドメイン名 (`contoso.com` など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-129">To use a custom domain name (such as `contoso.com`) create DNS records that map the custom domain name to the IP address.</span></span> <span data-ttu-id="8ac42-130">詳細については、[Azure App Service でのカスタム ドメイン名の構成][custom-domain-name]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-130">For more information, see [Configure a custom domain name in Azure App Service][custom-domain-name].</span></span>

- <span data-ttu-id="8ac42-131">**Azure SQL データベース**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-131">**Azure SQL Database**.</span></span> <span data-ttu-id="8ac42-132">[SQL Database][sql-db] は、クラウドのサービスとしてのリレーショナル データベースです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-132">[SQL Database][sql-db] is a relational database-as-a-service in the cloud.</span></span> <span data-ttu-id="8ac42-133">SQL Database は、そのコード ベースを Microsoft SQL Server データベース エンジンと共有しています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-133">SQL Database shares its code base with the Microsoft SQL Server database engine.</span></span> <span data-ttu-id="8ac42-134">アプリケーションの要件に応じて、[Azure Database for MySQL](/azure/mysql) または [Azure Database for PostgreSQL](/azure/postgresql) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-134">Depending on your application requirements, you can also use [Azure Database for MySQL](/azure/mysql) or [Azure Database for PostgreSQL](/azure/postgresql).</span></span> <span data-ttu-id="8ac42-135">これらは、それぞれオープン ソースの MySQL Server および Postgres データベース エンジンに基づく、フル マネージドのデータベース サービスです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-135">These are fully managed database services, based on the open source MySQL Server and Postgres database engines, respectively.</span></span>

- <span data-ttu-id="8ac42-136">**論理サーバー**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-136">**Logical server**.</span></span> <span data-ttu-id="8ac42-137">Azure SQL Database では、論理サーバーがデータベースをホストします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-137">In Azure SQL Database, a logical server hosts your databases.</span></span> <span data-ttu-id="8ac42-138">論理サーバーごとに複数のデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-138">You can create multiple databases per logical server.</span></span>

- <span data-ttu-id="8ac42-139">**Azure Storage**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-139">**Azure Storage**.</span></span> <span data-ttu-id="8ac42-140">診断ログを格納する BLOB コンテナーを持つ Azure ストレージ アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-140">Create an Azure storage account with a blob container to store diagnostic logs.</span></span>

- <span data-ttu-id="8ac42-141">**Azure Active Directory (Azure AD)**。</span><span class="sxs-lookup"><span data-stu-id="8ac42-141">**Azure Active Directory (Azure AD)**.</span></span> <span data-ttu-id="8ac42-142">Azure AD または他の認証 ID プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-142">Use Azure AD or another identity provider for authentication.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8ac42-143">Recommendations</span><span class="sxs-lookup"><span data-stu-id="8ac42-143">Recommendations</span></span>

<span data-ttu-id="8ac42-144">実際の要件は、ここで説明するアーキテクチャとは異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-144">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="8ac42-145">このセクションに記載されている推奨事項は原案として使用してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-145">Use the recommendations in this section as a starting point.</span></span>

### <a name="app-service-plan"></a><span data-ttu-id="8ac42-146">App Service プラン</span><span class="sxs-lookup"><span data-stu-id="8ac42-146">App Service plan</span></span>

<span data-ttu-id="8ac42-147">Standard レベルまたは Premium レベルを使用してください。どちらもスケールアウト、自動スケール、および Secure Sockets Layer (SSL) をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-147">Use the Standard or Premium tiers, because they support scale out, autoscale, and secure sockets layer (SSL).</span></span> <span data-ttu-id="8ac42-148">各レベルが、コア数とメモリが異なる複数の "*インスタンス サイズ*" をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-148">Each tier supports several *instance sizes* that differ by number of cores and memory.</span></span> <span data-ttu-id="8ac42-149">レベルまたはインスタンス サイズは、プランの作成後に変更できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-149">You can change the tier or instance size after you create a plan.</span></span> <span data-ttu-id="8ac42-150">App Service プランの詳細については、「[App Service の価格][app-service-plans-tiers]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-150">For more information about App Service plans, see [App Service Pricing][app-service-plans-tiers].</span></span>

<span data-ttu-id="8ac42-151">アプリが停止されても、App Service プランのインスタンスは課金の対象となります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-151">You are charged for the instances in the App Service plan, even if the app is stopped.</span></span> <span data-ttu-id="8ac42-152">使用していないプラン (テスト デプロイなど) は必ず削除してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-152">Make sure to delete plans that you aren't using (for example, test deployments).</span></span>

### <a name="sql-database"></a><span data-ttu-id="8ac42-153">SQL Database</span><span class="sxs-lookup"><span data-stu-id="8ac42-153">SQL Database</span></span>

<span data-ttu-id="8ac42-154">[V12 バージョン][sql-db-v12]の SQL Database を使用してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-154">Use the [V12 version][sql-db-v12] of SQL Database.</span></span> <span data-ttu-id="8ac42-155">SQL Database は、Basic、Standard、および Premium [サービス レベル][sql-db-service-tiers]をサポートしており、各レベルに、[データベース トランザクション ユニット (DTU)][sql-dtu] で測定された複数のパフォーマンス レベルが存在します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-155">SQL Database supports Basic, Standard, and Premium [service tiers][sql-db-service-tiers], with multiple performance levels within each tier measured in [Database Transaction Units (DTUs)][sql-dtu].</span></span> <span data-ttu-id="8ac42-156">キャパシティ プランニングを実行し、要件に合うレベルとパフォーマンス レベルを選択してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-156">Perform capacity planning and choose a tier and performance level that meets your requirements.</span></span>

### <a name="region"></a><span data-ttu-id="8ac42-157">リージョン</span><span class="sxs-lookup"><span data-stu-id="8ac42-157">Region</span></span>

<span data-ttu-id="8ac42-158">ネットワークの待機時間を最小限に抑えるには、App Service プランと SQL Database を同じリージョンでプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-158">Provision the App Service plan and the SQL Database in the same region to minimize network latency.</span></span> <span data-ttu-id="8ac42-159">一般的には、ユーザーに最も近いリージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-159">Generally, choose the region closest to your users.</span></span>

<span data-ttu-id="8ac42-160">リソース グループにもリージョンがあり、これによりデプロイ メタデータの格納場所が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-160">The resource group also has a region, which specifies where deployment metadata is stored.</span></span> <span data-ttu-id="8ac42-161">リソース グループとそのリソースは同じリージョンに配置してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-161">Put the resource group and its resources in the same region.</span></span> <span data-ttu-id="8ac42-162">これにより、デプロイ時の可用性が向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-162">This can improve availability during deployment.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="8ac42-163">スケーラビリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ac42-163">Scalability considerations</span></span>

<span data-ttu-id="8ac42-164">Azure App Service の主な利点は、負荷に応じてアプリケーションをスケーリングできることです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-164">A major benefit of Azure App Service is the ability to scale your application based on load.</span></span> <span data-ttu-id="8ac42-165">アプリケーションのスケーリングを計画する場合の考慮事項を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-165">Here are some considerations to keep in mind when planning to scale your application.</span></span>

### <a name="scaling-the-app-service-app"></a><span data-ttu-id="8ac42-166">App Service アプリのスケーリング</span><span class="sxs-lookup"><span data-stu-id="8ac42-166">Scaling the App Service app</span></span>

<span data-ttu-id="8ac42-167">App Service アプリをスケーリングする方法は 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-167">There are two ways to scale an App Service app:</span></span>

- <span data-ttu-id="8ac42-168">"*スケールアップ*" とは、インスタンス サイズを変更することです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-168">*Scale up*, which means changing the instance size.</span></span> <span data-ttu-id="8ac42-169">インスタンス サイズにより、各 VM インスタンスのメモリ、コア数、およびストレージが決まります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-169">The instance size determines the memory, number of cores, and storage on each VM instance.</span></span> <span data-ttu-id="8ac42-170">手動でスケールアップするには、インスタンス サイズまたはプラン レベルを変更します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-170">You can scale up manually by changing the instance size or the plan tier.</span></span>

- <span data-ttu-id="8ac42-171">"*スケールアウト*" とは、負荷の増加に対応するためにインスタンスを追加することです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-171">*Scale out*, which means adding instances to handle increased load.</span></span> <span data-ttu-id="8ac42-172">価格レベルごとに、インスタンスの最大数が設定されています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-172">Each pricing tier has a maximum number of instances.</span></span>

  <span data-ttu-id="8ac42-173">自分でインスタンス数を変更して手動でスケールアウトすることも、[自動スケール][web-app-autoscale]を使用して、スケジュールやパフォーマンス メトリックに基づいて、インスタンスが Azure によって自動的に追加または削除されるように指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-173">You can scale out manually by changing the instance count, or use [autoscaling][web-app-autoscale] to have Azure automatically add or remove instances based on a schedule and/or performance metrics.</span></span> <span data-ttu-id="8ac42-174">各スケール操作は迅速に、通常は数秒以内で行われます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-174">Each scale operation happens quickly&mdash;typically within seconds.</span></span>

  <span data-ttu-id="8ac42-175">自動スケールを有効にするには、インスタンスの最小数と最大数を定義する自動スケール "*プロファイル*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-175">To enable autoscaling, create an autoscale *profile* that defines the minimum and maximum number of instances.</span></span> <span data-ttu-id="8ac42-176">プロファイルのスケジュールを設定できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-176">Profiles can be scheduled.</span></span> <span data-ttu-id="8ac42-177">たとえば、平日と週末で別個のプロファイルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-177">For example, you might create separate profiles for weekdays and weekends.</span></span> <span data-ttu-id="8ac42-178">必要に応じて、どのタイミングでインスタンスを追加または削除するかを指定するルールをプロファイルに適用します </span><span class="sxs-lookup"><span data-stu-id="8ac42-178">Optionally, a profile contains rules for when to add or remove instances.</span></span> <span data-ttu-id="8ac42-179">(CPU 使用率が5 分にわたって 70% を超えた場合にインスタンスを 2 つ追加する、など。)</span><span class="sxs-lookup"><span data-stu-id="8ac42-179">(Example: Add two instances if CPU usage is above 70% for 5 minutes.)</span></span>

<span data-ttu-id="8ac42-180">Web アプリのスケーリングに関する推奨事項:</span><span class="sxs-lookup"><span data-stu-id="8ac42-180">Recommendations for scaling a web app:</span></span>

- <span data-ttu-id="8ac42-181">スケールアップとスケールダウンはできるだけ行わないでください。これによりアプリケーションの再起動がトリガーされる場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-181">As much as possible, avoid scaling up and down, because it may trigger an application restart.</span></span> <span data-ttu-id="8ac42-182">代わりに、通常の負荷でパフォーマンスの要件を満たすレベルとサイズを選択したうえで、インスタンスをスケールアウトして、トラフィック量の変化に対処します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-182">Instead, select a tier and size that meet your performance requirements under typical load and then scale out the instances to handle changes in traffic volume.</span></span>
- <span data-ttu-id="8ac42-183">自動スケールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-183">Enable autoscaling.</span></span> <span data-ttu-id="8ac42-184">アプリケーションに予測可能な通常のワークロードがある場合は、プロファイルを作成することで、事前にインスタンス数のスケジュールを設定します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-184">If your application has a predictable, regular workload, create profiles to schedule the instance counts ahead of time.</span></span> <span data-ttu-id="8ac42-185">ワークロードを予測できない場合は、ルール ベースの自動スケールを使用して負荷の変化に対応します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-185">If the workload is not predictable, use rule-based autoscaling to react to changes in load as they occur.</span></span> <span data-ttu-id="8ac42-186">両方のアプローチを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-186">You can combine both approaches.</span></span>
- <span data-ttu-id="8ac42-187">一般的に、CPU 使用率は自動スケールのルールのメトリックとして適切です。</span><span class="sxs-lookup"><span data-stu-id="8ac42-187">CPU usage is generally a good metric for autoscale rules.</span></span> <span data-ttu-id="8ac42-188">ただし、アプリケーションのロード テストを行い、潜在的なボトルネックを特定したうえで、そのデータに基づいて自動スケールのルールを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-188">However, you should load test your application, identify potential bottlenecks, and base your autoscale rules on that data.</span></span>
- <span data-ttu-id="8ac42-189">自動スケールのルールには、"*クールダウン*" 期間が含まれます。これは、スケール アクションの完了後、新しいスケール アクションが開始するまでの待機時間です。</span><span class="sxs-lookup"><span data-stu-id="8ac42-189">Autoscale rules include a *cool-down* period, which is the interval to wait after a scale action has completed before starting a new scale action.</span></span> <span data-ttu-id="8ac42-190">クールダウン期間により、スケーリングが再度行われる前に、システムを安定させることができます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-190">The cool-down period lets the system stabilize before scaling again.</span></span> <span data-ttu-id="8ac42-191">インスタンスを追加する場合はクールダウン期間を短くし、インスタンスを削除する場合はクールダウン期間を長くします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-191">Set a shorter cool-down period for adding instances, and a longer cool-down period for removing instances.</span></span> <span data-ttu-id="8ac42-192">たとえば、インスタンスを追加するときは 5 分に、インスタンスを削除するときは 60 分に設定します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-192">For example, set 5 minutes to add an instance, but 60 minutes to remove an instance.</span></span> <span data-ttu-id="8ac42-193">負荷の高い場所に新しいインスタンスを追加して過度のトラフィックを処理し、段階的にスケールバックすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-193">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

### <a name="scaling-sql-database"></a><span data-ttu-id="8ac42-194">SQL Database のスケーリング</span><span class="sxs-lookup"><span data-stu-id="8ac42-194">Scaling SQL Database</span></span>

<span data-ttu-id="8ac42-195">SQL Database に高いサービス レベルまたはパフォーマンス レベルが必要な場合は、アプリケーションのダウンタイムを伴わずに個々のデータベースをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-195">If you need a higher service tier or performance level for SQL Database, you can scale up individual databases with no application downtime.</span></span> <span data-ttu-id="8ac42-196">詳細については、[SQL Database のオプションとパフォーマンス:各サービス レベルで使用できる内容についての理解][sql-db-scale]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-196">For more information, see [SQL Database options and performance: Understand what's available in each service tier][sql-db-scale].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="8ac42-197">可用性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ac42-197">Availability considerations</span></span>

<span data-ttu-id="8ac42-198">この資料の作成時点では、App Service のサービス レベル アグリーメント (SLA) は 99.95% です。また、Basic、Standard、および Premium レベルの SQL Database の SLA は 99.99% です。</span><span class="sxs-lookup"><span data-stu-id="8ac42-198">At the time of writing, the service level agreement (SLA) for App Service is 99.95% and the SLA for SQL Database is 99.99% for Basic, Standard, and Premium tiers.</span></span>

> [!NOTE]
> <span data-ttu-id="8ac42-199">App Service の SLA は、1 つのインスタンスおよび複数のインスタンスの両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-199">The App Service SLA applies to both single and multiple instances.</span></span>
>

### <a name="backups"></a><span data-ttu-id="8ac42-200">バックアップ</span><span class="sxs-lookup"><span data-stu-id="8ac42-200">Backups</span></span>

<span data-ttu-id="8ac42-201">データ損失が発生した場合は、SQL Database により、ポイントインタイム リストアと geo リストアが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-201">In the event of data loss, SQL Database provides point-in-time restore and geo-restore.</span></span> <span data-ttu-id="8ac42-202">こうした機能はすべてのレベルで使用でき、自動的に有効になっています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-202">These features are available in all tiers and are automatically enabled.</span></span> <span data-ttu-id="8ac42-203">バックアップをスケジュールまたは管理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8ac42-203">You don't need to schedule or manage the backups.</span></span>

- <span data-ttu-id="8ac42-204">ポイントインタイム リストアを使用して、データベースを以前の特定の時点に戻すことで[人為的ミスから復旧][sql-human-error]できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-204">Use point-in-time restore to [recover from human error][sql-human-error] by returning the database to an earlier point in time.</span></span>
- <span data-ttu-id="8ac42-205">geo リストアを使用して、データベースを geo 冗長バックアップから復元することで[サービス停止から復旧][sql-outage-recovery]できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-205">Use geo-restore to [recover from a service outage][sql-outage-recovery] by restoring a database from a geo-redundant backup.</span></span>

<span data-ttu-id="8ac42-206">詳細については、[SQL Database を使用したクラウド ビジネス継続性とデータベース ディザスター リカバリー][sql-backup]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-206">For more information, see [Cloud business continuity and database disaster recovery with SQL Database][sql-backup].</span></span>

<span data-ttu-id="8ac42-207">App Service により、ご使用のアプリケーション ファイルで[バックアップおよび復元][web-app-backup]機能をご利用いただけます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-207">App Service provides a [backup and restore][web-app-backup] feature for your application files.</span></span> <span data-ttu-id="8ac42-208">ただし、バックアップ ファイルに含まれるアプリ設定はプレーンテキストであり、これには接続文字列などのシークレットが含まれる可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-208">However, be aware that the backed-up files include app settings in plain text and these may include secrets, such as connection strings.</span></span> <span data-ttu-id="8ac42-209">App Service のバックアップ機能を使用して SQL データベースをバックアップすることは避けてください。このバックアップにより、データベースが SQL .bacpac にエクスポートされ、[DTU][sql-dtu] を消費するためです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-209">Avoid using the App Service backup feature to back up your SQL databases because it exports the database to a SQL .bacpac file, consuming [DTUs][sql-dtu].</span></span> <span data-ttu-id="8ac42-210">代わりに、上で説明した SQL Database のポイントインタイム リストアを使用します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-210">Instead, use SQL Database point-in-time restore described above.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="8ac42-211">管理容易性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ac42-211">Manageability considerations</span></span>

<span data-ttu-id="8ac42-212">運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-212">Create separate resource groups for production, development, and test environments.</span></span> <span data-ttu-id="8ac42-213">これにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てを行いやすくなります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-213">This makes it easier to manage deployments, delete test deployments, and assign access rights.</span></span>

<span data-ttu-id="8ac42-214">リソースをリソース グループに割り当てるときは、以下の事項を検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-214">When assigning resources to resource groups, consider the following:</span></span>

- <span data-ttu-id="8ac42-215">ライフサイクル。</span><span class="sxs-lookup"><span data-stu-id="8ac42-215">Lifecycle.</span></span> <span data-ttu-id="8ac42-216">一般的に、同じライフサイクルのリソースは、同じリソース グループに追加します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-216">In general, put resources with the same lifecycle into the same resource group.</span></span>
- <span data-ttu-id="8ac42-217">アクセス。</span><span class="sxs-lookup"><span data-stu-id="8ac42-217">Access.</span></span> <span data-ttu-id="8ac42-218">[ロールベースのアクセス制御][rbac] (RBAC) を使用して、アクセス ポリシーをグループのリソースに適用します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-218">You can use [role-based access control][rbac] (RBAC) to apply access policies to the resources in a group.</span></span>
- <span data-ttu-id="8ac42-219">課金。</span><span class="sxs-lookup"><span data-stu-id="8ac42-219">Billing.</span></span> <span data-ttu-id="8ac42-220">リソース グループのロールアップ コストを表示できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-220">You can view the rolled-up costs for the resource group.</span></span>

<span data-ttu-id="8ac42-221">詳細については、「[Azure Resource Manager の概要](/azure/azure-resource-manager/resource-group-overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-221">For more information, see [Azure Resource Manager overview](/azure/azure-resource-manager/resource-group-overview).</span></span>

### <a name="deployment"></a><span data-ttu-id="8ac42-222">Deployment</span><span class="sxs-lookup"><span data-stu-id="8ac42-222">Deployment</span></span>

<span data-ttu-id="8ac42-223">デプロイには 2 つの手順が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-223">Deployment involves two steps:</span></span>

1. <span data-ttu-id="8ac42-224">Azure リソースのプロビジョニング。</span><span class="sxs-lookup"><span data-stu-id="8ac42-224">Provisioning the Azure resources.</span></span> <span data-ttu-id="8ac42-225">この手順には [Azure Resource Manager テンプレート][arm-template]を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-225">We recommend that you use [Azure Resource Manager templates][arm-template] for this step.</span></span> <span data-ttu-id="8ac42-226">テンプレートにより、PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用したデプロイが自動化しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-226">Templates make it easier to automate deployments via PowerShell or the Azure command line interface (CLI).</span></span>
2. <span data-ttu-id="8ac42-227">アプリケーションのデプロイ (コード、バイナリ、およびコンテンツ ファイル)。</span><span class="sxs-lookup"><span data-stu-id="8ac42-227">Deploying the application (code, binaries, and content files).</span></span> <span data-ttu-id="8ac42-228">ローカル Git リポジトリからのデプロイ、Visual Studio の使用、クラウド ベースのソース管理からの継続的デプロイなど、複数のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-228">You have several options, including deploying from a local Git repository, using Visual Studio, or continuous deployment from cloud-based source control.</span></span> <span data-ttu-id="8ac42-229">[Azure App Service へのアプリのデプロイ][deploy]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-229">See [Deploy your app to Azure App Service][deploy].</span></span>

<span data-ttu-id="8ac42-230">App Service アプリには、`production` という名前のデプロイ スロットが必ず 1 つ含まれます。これは実稼働の運用サイトを表します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-230">An App Service app always has one deployment slot named `production`, which represents the live production site.</span></span> <span data-ttu-id="8ac42-231">更新プログラムをデプロイするステージング スロットを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-231">We recommend creating a staging slot for deploying updates.</span></span> <span data-ttu-id="8ac42-232">ステージング スロットを使用する利点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-232">The benefits of using a staging slot include:</span></span>

- <span data-ttu-id="8ac42-233">デプロイが成功していることを確認してから、それを運用環境にスワップできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-233">You can verify the deployment succeeded, before swapping it into production.</span></span>
- <span data-ttu-id="8ac42-234">ステージング スロットにデプロイすることで、運用環境にスワップする前に、すべてのインスタンスを確実にウォームアップできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-234">Deploying to a staging slot ensures that all instances are warmed up before being swapped into production.</span></span> <span data-ttu-id="8ac42-235">ウォームアップとコールドスタートに時間がかかるアプリケーションは多数あります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-235">Many applications have a significant warmup and cold-start time.</span></span>

<span data-ttu-id="8ac42-236">また、3 番目のスロットを作成して、前回正常起動時のデプロイを保持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-236">We also recommend creating a third slot to hold the last-known-good deployment.</span></span> <span data-ttu-id="8ac42-237">ステージングと運用をスワップしたら、これまでの運用環境のデプロイ (現在ステージング スロットにある) を、前回正常起動時デプロイ用のスロットに移動します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-237">After you swap staging and production, move the previous production deployment (which is now in staging) into the last-known-good slot.</span></span> <span data-ttu-id="8ac42-238">このようにして、後で問題を見つけた場合に、前回正常起動時のバージョンにすばやく戻ることができます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-238">That way, if you discover a problem later, you can quickly revert to the last-known-good version.</span></span>

<span data-ttu-id="8ac42-239">![[1]][1]</span><span class="sxs-lookup"><span data-stu-id="8ac42-239">![[1]][1]</span></span>

<span data-ttu-id="8ac42-240">前のバージョンに戻す場合は、データベース スキーマのすべての変更に下位互換性があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-240">If you revert to a previous version, make sure any database schema changes are backward compatible.</span></span>

<span data-ttu-id="8ac42-241">同じ App Service プラン内のすべてのアプリが同じ VM インスタンスを共有するため、運用環境のデプロイのスロットをテスト用に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-241">Don't use slots on your production deployment for testing because all apps within the same App Service plan share the same VM instances.</span></span> <span data-ttu-id="8ac42-242">たとえば、ロード テストは実稼働の運用サイトの機能を低下させる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-242">For example, load tests might degrade the live production site.</span></span> <span data-ttu-id="8ac42-243">代わりに、運用とテスト用に別々の App Service プランを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-243">Instead, create separate App Service plans for production and test.</span></span> <span data-ttu-id="8ac42-244">テスト デプロイを別のプランに入れて、運用バージョンから切り離してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-244">By putting test deployments into a separate plan, you isolate them from the production version.</span></span>

### <a name="configuration"></a><span data-ttu-id="8ac42-245">構成</span><span class="sxs-lookup"><span data-stu-id="8ac42-245">Configuration</span></span>

<span data-ttu-id="8ac42-246">構成設定を[アプリ設定][app-settings]として格納します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-246">Store configuration settings as [app settings][app-settings].</span></span> <span data-ttu-id="8ac42-247">Resource Manager テンプレート内で、または PowerShell を使用して、アプリ設定を定義します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-247">Define the app settings in your Resource Manager templates, or using PowerShell.</span></span> <span data-ttu-id="8ac42-248">実行時、アプリケーションはアプリ設定を環境変数として使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-248">At runtime, app settings are available to the application as environment variables.</span></span>

<span data-ttu-id="8ac42-249">パスワード、アクセス キー、または接続文字列を、ソース管理にチェックインしないでください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-249">Never check passwords, access keys, or connection strings into source control.</span></span> <span data-ttu-id="8ac42-250">代わりに、これらをパラメーターとして、デプロイ スクリプトに渡します。スクリプトは、こうした値をアプリ設定として格納します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-250">Instead, pass these as parameters to a deployment script that stores these values as app settings.</span></span>

<span data-ttu-id="8ac42-251">デプロイ スロットをスワップすると、アプリ設定は既定でスワップされます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-251">When you swap a deployment slot, the app settings are swapped by default.</span></span> <span data-ttu-id="8ac42-252">運用環境とステージングで異なる設定が必要な場合は、スワップされないスロット固有のアプリ設定を作成できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-252">If you need different settings for production and staging, you can create app settings that stick to a slot and don't get swapped.</span></span>

### <a name="diagnostics-and-monitoring"></a><span data-ttu-id="8ac42-253">診断および監視</span><span class="sxs-lookup"><span data-stu-id="8ac42-253">Diagnostics and monitoring</span></span>

<span data-ttu-id="8ac42-254">アプリケーションのログ記録や Web サーバーのログ記録を含む、[診断のログ記録][diagnostic-logs]を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-254">Enable [diagnostics logging][diagnostic-logs], including application logging and web server logging.</span></span> <span data-ttu-id="8ac42-255">Blob Storage を使用するようにログ記録を構成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-255">Configure logging to use Blob storage.</span></span> <span data-ttu-id="8ac42-256">パフォーマンス上の理由から、診断ログ用に別のストレージ アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-256">For performance reasons, create a separate storage account for diagnostic logs.</span></span> <span data-ttu-id="8ac42-257">ログとアプリケーション データに同じストレージ アカウントを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-257">Don't use the same storage account for logs and application data.</span></span> <span data-ttu-id="8ac42-258">ログ記録に関する詳細なガイダンスについては、[監視と診断のガイダンス][monitoring-guidance]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-258">For more detailed guidance on logging, see [Monitoring and diagnostics guidance][monitoring-guidance].</span></span>

<span data-ttu-id="8ac42-259">[New Relic][new-relic]、[Application Insights][app-insights] などのサービスを使用して、負荷がかかった状態のアプリケーションのパフォーマンスと動作を監視してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-259">Use a service such as [New Relic][new-relic] or [Application Insights][app-insights] to monitor application performance and behavior under load.</span></span> <span data-ttu-id="8ac42-260">Application Insights の[データ速度の制限][app-insights-data-rate]に気を付けてください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-260">Be aware of the [data rate limits][app-insights-data-rate] for Application Insights.</span></span>

<span data-ttu-id="8ac42-261">[Azure DevOps][azure-devops] や [Visual Studio Team Foundation Server][tfs] などのツールを使用して、ロード テストを行います。</span><span class="sxs-lookup"><span data-stu-id="8ac42-261">Perform load testing, using a tool such as [Azure DevOps][azure-devops] or [Visual Studio Team Foundation Server][tfs].</span></span> <span data-ttu-id="8ac42-262">クラウド アプリケーションのパフォーマンス分析の概要については、「[Performance Analysis Primer (パフォーマンス分析の手引き)][perf-analysis]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-262">For a general overview of performance analysis in cloud applications, see [Performance Analysis Primer][perf-analysis].</span></span>

<span data-ttu-id="8ac42-263">アプリケーションのトラブルシューティングのヒント:</span><span class="sxs-lookup"><span data-stu-id="8ac42-263">Tips for troubleshooting your application:</span></span>

- <span data-ttu-id="8ac42-264">Azure Portal の[トラブルシューティング ブレード][troubleshoot-blade]を使用して、一般的な問題の解決策を見つけます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-264">Use the [troubleshoot blade][troubleshoot-blade] in the Azure portal to find solutions to common problems.</span></span>
- <span data-ttu-id="8ac42-265">[ログ ストリーミング][web-app-log-stream]を有効にして、ほぼリアルタイムでログ情報を監視します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-265">Enable [log streaming][web-app-log-stream] to see logging information in near-real time.</span></span>
- <span data-ttu-id="8ac42-266">[Kudu ダッシュボード][kudu]には、アプリケーションを監視およびデバッグするためのツールがいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-266">The [Kudu dashboard][kudu] has several tools for monitoring and debugging your application.</span></span> <span data-ttu-id="8ac42-267">詳細については、[知っておくべき Azure Websites のオンライン ツール][kudu]に関するページ (ブログ記事) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-267">For more information, see [Azure Websites online tools you should know about][kudu] (blog post).</span></span> <span data-ttu-id="8ac42-268">Kudu ダッシュボードには Azure Portal からアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-268">You can reach the Kudu dashboard from the Azure portal.</span></span> <span data-ttu-id="8ac42-269">アプリのブレードを開き、**[ツール]**、**Kudu** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-269">Open the blade for your app and click **Tools**, then click **Kudu**.</span></span>
- <span data-ttu-id="8ac42-270">Visual Studio を使用する場合、デバッグとトラブルシューティングのヒントについては、「[Visual Studio を使用した Azure App Service のトラブルシューティング][troubleshoot-web-app]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-270">If you use Visual Studio, see the article [Troubleshoot a web app in Azure App Service using Visual Studio][troubleshoot-web-app] for debugging and troubleshooting tips.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="8ac42-271">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ac42-271">Security considerations</span></span>

<span data-ttu-id="8ac42-272">このセクションでは、この記事で説明している Azure サービスに固有のセキュリティの考慮事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-272">This section lists security considerations that are specific to the Azure services described in this article.</span></span> <span data-ttu-id="8ac42-273">これはセキュリティ上のベスト プラクティスを網羅した一覧ではありません。</span><span class="sxs-lookup"><span data-stu-id="8ac42-273">It's not a complete list of security best practices.</span></span> <span data-ttu-id="8ac42-274">その他のセキュリティの考慮事項については、[Azure App Service でのアプリのセキュリティ保護][app-service-security]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-274">For some additional security considerations, see [Secure an app in Azure App Service][app-service-security].</span></span>

### <a name="sql-database-auditing"></a><span data-ttu-id="8ac42-275">SQL Database 監査</span><span class="sxs-lookup"><span data-stu-id="8ac42-275">SQL Database auditing</span></span>

<span data-ttu-id="8ac42-276">監査により、規定遵守を維持したり、ビジネス上の懸念やセキュリティ違反の疑いを示す差異や異常に対する分析情報を得たりすることが容易になります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-276">Auditing can help you maintain regulatory compliance and get insight into discrepancies and irregularities that could indicate business concerns or suspected security violations.</span></span> <span data-ttu-id="8ac42-277">「[SQL Database 監査の使用][sql-audit]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-277">See [Get started with SQL database auditing][sql-audit].</span></span>

### <a name="deployment-slots"></a><span data-ttu-id="8ac42-278">デプロイ スロット</span><span class="sxs-lookup"><span data-stu-id="8ac42-278">Deployment slots</span></span>

<span data-ttu-id="8ac42-279">デプロイ スロットごとにパブリック IP アドレスがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-279">Each deployment slot has a public IP address.</span></span> <span data-ttu-id="8ac42-280">開発チームと DevOps チームのメンバーのみがこうしたエンドポイントにアクセスできるように、[Azure Active Directory ログイン][aad-auth]を使用して、非運用スロットをセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-280">Secure the nonproduction slots using [Azure Active Directory login][aad-auth] so that only members of your development and DevOps teams can reach those endpoints.</span></span>

### <a name="logging"></a><span data-ttu-id="8ac42-281">ログの記録</span><span class="sxs-lookup"><span data-stu-id="8ac42-281">Logging</span></span>

<span data-ttu-id="8ac42-282">ユーザーのパスワードなど、不正アクセスにつながる情報はログに記録しないようにします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-282">Logs should never record users' passwords or other information that might be used to commit identity fraud.</span></span> <span data-ttu-id="8ac42-283">このような詳細情報は、データを格納する前に除外してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-283">Scrub those details from the data before storing it.</span></span>

### <a name="ssl"></a><span data-ttu-id="8ac42-284">SSL</span><span class="sxs-lookup"><span data-stu-id="8ac42-284">SSL</span></span>

<span data-ttu-id="8ac42-285">App Service アプリには、追加コストなしで、`azurewebsites.net` のサブドメインに SSL エンドポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-285">An App Service app includes an SSL endpoint on a subdomain of `azurewebsites.net` at no additional cost.</span></span> <span data-ttu-id="8ac42-286">SSL エンドポイントには、`*.azurewebsites.net` ドメインのワイルドカード証明書が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-286">The SSL endpoint includes a wildcard certificate for the `*.azurewebsites.net` domain.</span></span> <span data-ttu-id="8ac42-287">カスタム ドメイン名を使用する場合は、カスタム ドメインに合致する証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-287">If you use a custom domain name, you must provide a certificate that matches the custom domain.</span></span> <span data-ttu-id="8ac42-288">最も簡単な方法は、Azure Portal から直接に証明書を購入することです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-288">The simplest approach is to buy a certificate directly through the Azure portal.</span></span> <span data-ttu-id="8ac42-289">他の証明機関から証明書をインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-289">You can also import certificates from other certificate authorities.</span></span> <span data-ttu-id="8ac42-290">詳細については、「[Azure App Service の SSL 証明書を購入して構成する][ssl-cert]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-290">For more information, see [Buy and Configure an SSL Certificate for your Azure App Service][ssl-cert].</span></span>

<span data-ttu-id="8ac42-291">セキュリティのベスト プラクティスとして、アプリは HTTP 要求をリダイレクトすることで HTTPS を強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-291">As a security best practice, your app should enforce HTTPS by redirecting HTTP requests.</span></span> <span data-ttu-id="8ac42-292">これをアプリケーション内で実装するか、[Azure App Service でのアプリの HTTPS の有効化][ssl-redirect]に関するページの説明に従って、URL 書き換え規則を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ac42-292">You can implement this inside your application or use a URL rewrite rule as described in [Enable HTTPS for an app in Azure App Service][ssl-redirect].</span></span>

### <a name="authentication"></a><span data-ttu-id="8ac42-293">Authentication</span><span class="sxs-lookup"><span data-stu-id="8ac42-293">Authentication</span></span>

<span data-ttu-id="8ac42-294">Azure AD、Facebook、Google、Twitter などの ID プロバイダー (IDP) を使用して認証することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ac42-294">We recommend authenticating through an identity provider (IDP), such as Azure AD, Facebook, Google, or Twitter.</span></span> <span data-ttu-id="8ac42-295">認証フローには OAuth 2 または OpenID Connect (OIDC) を使用してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-295">Use OAuth 2 or OpenID Connect (OIDC) for the authentication flow.</span></span> <span data-ttu-id="8ac42-296">Azure AD には、ユーザーとグループの管理、アプリケーション ロールの作成、オンプレミス ID の統合、およびバックエンド サービス (Office 365、Skype for Business など) の使用に関する機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-296">Azure AD provides functionality to manage users and groups, create application roles, integrate your on-premises identities, and consume backend services such as Office 365 and Skype for Business.</span></span>

<span data-ttu-id="8ac42-297">ユーザーのログインおよび資格情報をアプリケーションで直接管理するのは避けてください。これにより、攻撃対象領域が発生する可能性があるからです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-297">Avoid having the application manage user logins and credentials directly, as it creates a potential attack surface.</span></span>  <span data-ttu-id="8ac42-298">少なくとも、メール確認、パスワード復元、および多要素認証が必要です。また、パスワードの強度を検証し、パスワード ハッシュを安全に格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ac42-298">At a minimum, you would need to have email confirmation, password recovery, and multi-factor authentication; validate password strength; and store password hashes securely.</span></span> <span data-ttu-id="8ac42-299">大規模な ID プロバイダーはこれをすべて処理し、セキュリティ プラクティスを常に監視し、強化しています。</span><span class="sxs-lookup"><span data-stu-id="8ac42-299">The large identity providers handle all of those things for you, and are constantly monitoring and improving their security practices.</span></span>

<span data-ttu-id="8ac42-300">[App Service 認証][app-service-auth]を使用して、OAuth/OIDC 認証フローを実装することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-300">Consider using [App Service authentication][app-service-auth] to implement the OAuth/OIDC authentication flow.</span></span> <span data-ttu-id="8ac42-301">App Service 認証の利点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-301">The benefits of App Service authentication include:</span></span>

- <span data-ttu-id="8ac42-302">構成が簡単。</span><span class="sxs-lookup"><span data-stu-id="8ac42-302">Easy to configure.</span></span>
- <span data-ttu-id="8ac42-303">シンプルな認証シナリオではコードが不要。</span><span class="sxs-lookup"><span data-stu-id="8ac42-303">No code is required for simple authentication scenarios.</span></span>
- <span data-ttu-id="8ac42-304">ユーザーに代わって、OAuth アクセス トークンを使用してリソースを消費できるよう、委任承認をサポート。</span><span class="sxs-lookup"><span data-stu-id="8ac42-304">Supports delegated authorization using OAuth access tokens to consume resources on behalf of the user.</span></span>
- <span data-ttu-id="8ac42-305">組み込みのトークン キャッシュを提供。</span><span class="sxs-lookup"><span data-stu-id="8ac42-305">Provides a built-in token cache.</span></span>

<span data-ttu-id="8ac42-306">App Service 認証の制限は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8ac42-306">Some limitations of App Service authentication:</span></span>

- <span data-ttu-id="8ac42-307">カスタマイズのオプションに制限がある。</span><span class="sxs-lookup"><span data-stu-id="8ac42-307">Limited customization options.</span></span>
- <span data-ttu-id="8ac42-308">ログイン セッションごとに、委任承認が 1 つのバックエンド リソースに制限される。</span><span class="sxs-lookup"><span data-stu-id="8ac42-308">Delegated authorization is restricted to one backend resource per login session.</span></span>
- <span data-ttu-id="8ac42-309">複数の IDP を使用する場合、ホーム領域検出のための組み込みメカニズムがない。</span><span class="sxs-lookup"><span data-stu-id="8ac42-309">If you use more than one IDP, there is no built-in mechanism for home realm discovery.</span></span>
- <span data-ttu-id="8ac42-310">マルチテナント シナリオでは、アプリケーションがトークン発行者を検証するロジックを実装する必要がある。</span><span class="sxs-lookup"><span data-stu-id="8ac42-310">For multi-tenant scenarios, the application must implement the logic to validate the token issuer.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="8ac42-311">ソリューションのデプロイ方法</span><span class="sxs-lookup"><span data-stu-id="8ac42-311">Deploy the solution</span></span>

<span data-ttu-id="8ac42-312">このアーキテクチャの Resource Manager テンプレートの例については、[GitHub を参照してください][paas-basic-arm-template]。</span><span class="sxs-lookup"><span data-stu-id="8ac42-312">An example Resource Manager template for this architecture is [available on GitHub][paas-basic-arm-template].</span></span>

<span data-ttu-id="8ac42-313">PowerShell を使用してテンプレートをデプロイするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8ac42-313">To deploy the template using PowerShell, run the following commands:</span></span>

```powershell
New-AzureRmResourceGroup -Name <resource-group-name> -Location "West US"

$parameters = @{"appName"="<app-name>";"environment"="dev";"locationShort"="uw";"databaseName"="app-db";"administratorLogin"="<admin>";"administratorLoginPassword"="<password>"}

New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile .\PaaS-Basic.json -TemplateParameterObject  $parameters
```

<span data-ttu-id="8ac42-314">詳細については、[Azure Resource Manager テンプレートを使用したリソースのデプロイ][deploy-arm-template]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ac42-314">For more information, see [Deploy resources with Azure Resource Manager templates][deploy-arm-template].</span></span>

<!-- links -->

[aad-auth]: /azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication
[app-insights]: /azure/application-insights/app-insights-overview
[app-insights-data-rate]: /azure/application-insights/app-insights-pricing
[app-service]: /azure/app-service/
[app-service-auth]: /azure/app-service-api/app-service-api-authentication
[app-service-plans]: /azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
[app-service-plans-tiers]: https://azure.microsoft.com/pricing/details/app-service/
[app-service-security]: /azure/app-service-web/web-sites-security
[app-settings]: /azure/app-service-web/web-sites-configure
[arm-template]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-devops]: /azure/devops/
[azure-dns]: /azure/dns/dns-overview
[custom-domain-name]: /azure/app-service-web/web-sites-custom-domain-name
[deploy]: /azure/app-service-web/web-sites-deploy
[deploy-arm-template]: /azure/resource-group-template-deploy
[deployment-slots]: /azure/app-service-web/web-sites-staged-publishing
[diagnostic-logs]: /azure/app-service-web/web-sites-enable-diagnostic-log
[kudu]: https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/
[monitoring-guidance]: ../../best-practices/monitoring.md
[new-relic]: https://newrelic.com/
[paas-basic-arm-template]: https://github.com/mspnp/reference-architectures/tree/master/managed-web-app/basic-web-app/Paas-Basic/Templates
[perf-analysis]: https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md
[rbac]: /azure/active-directory/role-based-access-control-what-is
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[sla]: https://azure.microsoft.com/support/legal/sla/
[sql-audit]: /azure/sql-database/sql-database-auditing-get-started
[sql-backup]: /azure/sql-database/sql-database-business-continuity
[sql-db]: /azure/sql-database/
[sql-db-overview]: /azure/sql-database/sql-database-technical-overview
[sql-db-scale]: /azure/sql-database/sql-database-service-tiers#scaling-up-or-scaling-down-a-single-database
[sql-db-service-tiers]: /azure/sql-database/sql-database-service-tiers
[sql-db-v12]: /azure/sql-database/sql-database-features
[sql-dtu]: /azure/sql-database/sql-database-service-tiers
[sql-human-error]: /azure/sql-database/sql-database-business-continuity#recover-a-database-after-a-user-or-application-error
[sql-outage-recovery]: /azure/sql-database/sql-database-business-continuity#recover-a-database-to-another-region-from-an-azure-regional-data-center-outage
[ssl-redirect]: /azure/app-service-web/web-sites-configure-ssl-certificate#bkmk_enforce
[sql-resource-limits]: /azure/sql-database/sql-database-resource-limits
[ssl-cert]: /azure/app-service-web/web-sites-purchase-ssl-web-site
[troubleshoot-blade]: https://azure.microsoft.com/updates/self-service-troubleshooting-for-app-service-web-apps-customers/
[tfs]: /tfs/index
[troubleshoot-web-app]: /azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-autoscale]: /azure/app-service-web/web-sites-scale
[web-app-backup]: /azure/app-service-web/web-sites-backup
[web-app-log-stream]: /azure/app-service-web/web-sites-enable-diagnostic-log#streamlogs
[1]: ./images/paas-basic-web-app-staging-slots.png "運用環境およびステージング環境のデプロイ用スロットのスワップ"
