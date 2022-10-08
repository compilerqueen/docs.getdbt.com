---
title: "Quickstart"
id: quickstart-semantic-layer
description: "Define metrics and set up the dbt Semantic Layer"
sidebar_label: "Quickstart"
---

# dbt Semantic Layer quickstart

:::info 📌

The dbt Semantic Layer is currently available for public preview! With the dbt Semantic Layer, you'll be able to centrally define business metrics, remove code duplication and inconsistency, create self-service in downstream tools, and more! Review the [public preview section below](/docs/integrate/quickstart-semantic-layer#public-preview) to understand what public preview means for you.

:::

Try out the features of the dbt Semantic Layer and follow this guide to learn more.

## Introduction

To use the dbt Semantic Layer, you first need to have a dbt project set up. This quickstart guide will lay out the following steps, and recommends a workflow that demonstrates some of its essential features:

- Install dbt metrics package
- Define metrics
- Query, and run metrics
- Configure the dbt Semantic Layer

## Prerequisites
To use the dbt Semantic Layer, you’ll need to meet the following:

- Have a multi-tenant [dbt Cloud](https://cloud.getdbt.com/) Teams or Enterprise account. 
   * Developer accounts will be able to query the Proxy Server using SQL, but will not be able to browse pre-populated dbt metrics in external tools, which requires access to the Metadata API.
- Have both your production and development environments running dbt version 1.2 (latest) or higher
- Use Snowflake data platfrom 
- Install the [dbt metrics package](https://hub.getdbt.com/dbt-labs/metrics/latest/) version 0.3.2 or higher in your dbt project
- Set up the [Metadata API](/docs/dbt-cloud/dbt-cloud-api/metadata/metadata-overview) in the integrated tool to import metric definitions
- Recommended - Review the [dbt metrics page](/docs/building-a-dbt-project/metrics) and [Getting started with the dbt Semantic Layer](https://docs.getdbt.com/blog/getting-started-with-the-dbt-semantic-layer) blog

## Considerations

Here are some important considerations to know about during public preview:

- Support for Snowflake data platform only (_additional data platforms coming soon_)
- Support for the deployment environment only (_development experience coming soon_)
- Do not use environment variables for the job/environment (_coming soon_)

:::info 📌 

New to dbt or metrics? Check out our [Getting Started guide](/guides/getting-started) to build your first dbt project! If you'd like to define your first metrics, try our [Jaffle Shop](https://github.com/dbt-labs/jaffle_shop_metrics) example project.

:::

## Installing dbt metrics package

1. You can install the [dbt metrics package](https://hub.getdbt.com/dbt-labs/metrics/latest/) into your dbt project by copying the below code blocks. Make sure you use a dbt metrics package that’s compatible with your dbt environment version. 

<!---
<File name='models/<filename>.yml'>

<VersionBlock firstVersion="1.3">

```yaml
packages:
- package: dbt-labs/metrics
  version: [">=1.3.0", "<1.4.0"]
```
    
</VersionBlock> 

<VersionBlock lastVersion="1.2">

```yaml
packages:
  - package: dbt-labs/metrics
    version: [">=0.3.0", "<0.4.0"]
```
    
</VersionBlock> 

</File>   
--->


2. Paste the dbt metrics package code in your `packages.yml` file.
3. Run the [`dbt deps` command](/reference/commands/deps) to install the package.
4. If you see successful result, you have now installed the dbt metrics package successfully! 
5. If you have any errors during the `dbt deps` command run, review the system logs for more information on how to resolve them.

<Lightbox src="/img/docs/dbt-cloud/semantic-layer/metrics_package.png" title="Running dbt deps in the dbt Cloud IDE" />

## Design and define metrics in your dbt project

Before you define metrics in your dbt project, you'll need to understand how to structure and organize your metrics. For more info on that and to understand the best practices, review our [Structuring and designing your metrics](url) blog post first.

Now that's you've organized your metrics folder and files, you can define your metrics in `.yml` files nested under a `metrics` key.  

1. Add the metric definitions found in the [Jaffle Shop](https://github.com/dbt-labs/jaffle_shop_metrics) example to your dbt project. For example, to add an Expenses metric, see the below metrics you can define directly in your metrics folder: 

```sql
version: 2

metrics:
  - name: expenses
    label: Expenses
    model: ref('orders')
    description: "The total expenses of our jaffle business"

    calculation_method: sum
    expression: amount / 4

    timestamp: order_date
    time_grains: [day, week, month, year]

    dimensions:
      - customer_status
      - had_credit_card_payment
      - had_coupon_payment
      - had_bank_transfer_payment
      - had_gift_card_payment

    filters:
      - field: status
        operator: '='
        value: "'completed'"
```

1. Click **Save** and **Compile** the code.
2. Commit and merge the code changes that contain the metric definitions.
3. If you'd like to further design and design your own metrics, review the following documentation:

    - [dbt metrics](/docs/building-a-dbt-project/metrics) will povide you in-depth detail on attributes, properties, filters, and how to define and query metrics.
   
    - [`FUTURE BLOG POST`](URL) to understand best practices for designing and structuring metrics in your dbt project.

## Develop and query metrics

You can dynamically develop and query metrics directly in dbt and verify their accuracy *before* running a job in the deployment environment by using the `metrics.calculate` macro and `metrics.develop` macro.

To understand when and how to use the macros above, review [dbt metrics](/docs/building-a-dbt-project/metrics) and make sure you install the [dbt_metrics package](https://github.com/dbt-labs/dbt_metrics) first before using the above macros.

:::info 📌 

Note, you will need access to dbt Cloud and the dbt Semantic Layer from your integrated tool of choice. 

:::

## Run your production job

Once you’ve defined metrics in your dbt project, you can perform a job run in your deployment environment to materialize your metrics. The deployment environment is only supported for the dbt Semantic Layer at this moment. 

1. Go to **Deploy** in the navigation and select **Jobs** to re-run the job with the most recent code in the deployment environment.
2. Your metric should appear as red nodes in the dbt Cloud IDE and dbt directed acyclic graphs (DAG).

<Lightbox src="/img/docs/dbt-cloud/semantic-layer/metrics_red_nodes.png" title="DAG with metrics appearing as red nodes" />

:::info📌 

What’s happening internally?

- Merging the code into your main branch allows dbt Cloud to pull those changes and builds the definition in the manifest produced by the run.
- Re-running the job in the deployment environment helps materialize the models, which the metrics depend on, in the data platform. It also makes sure that the manifest is up to date.
- Your dbt Metadata API pulls in the most recent manifest and allows your integration information to extract metadata from it.

:::

## Set up dbt Semantic Layer

To query your universally-defined metrics in your integration tool, you need to [set up the dbt Semantic Layer](/docs/integrate/setup-dbt-semantic-layer#set-up-dbt-semantic-layer) in dbt Cloud to connect with your integration tool:

1. In your dbt Cloud account, go to **Account Settings** and then **Service Tokens** to create a new [service account API token](/docs/dbt-cloud/dbt-cloud-api/service-tokens). 
2. You won't be able to see your token again so we recommend you copy it somewhere safe.
3. Go to **Deploy** and then **Environment**, and select your **Deployment** environment.
4. Io=n the upper right side of the page, click **Settings** and then **Edit**.
5. Select dbt Version 1.2 (latest) or higher.
6. Toggle the Semantic Layer **On.**
7. Copy the Proxy Server URL to connect to your [integrated tool](https://www.getdbt.com/product/semantic-layer-integrations). 
8. If supported by your tool, provide an API service token with metadata access. 
9. You can now run precise and consistent queries with the dbt Semantic Layer.

**Configure dbt Semantic Layer** 

:::info 

Before you continue with the below steps, you must have a multi tenant dbt Cloud Team or Enterprise plan. Developer accounts will be able to query the Proxy Server using SQL, but will not be able to browse dbt metrics in external tools, which requires access to the [Metadata API](/docs/dbt-cloud/dbt-cloud-api/metadata/metadata-overview).

:::

In order to query your precise and universally-defined metrics in your integration tool, you need to [`configure the dbt Semantic Layer`](URL) in dbt Cloud to connect with your integration tool:

1. In your dbt Cloud account, go to **Account Settings** and then **Service Tokens** to create a new [service account API token](https://docs.getdbt.com/docs/dbt-cloud/dbt-cloud-api/service-tokens). Save your token somewhere safe.
2. Go to **Deploy** and then **Environment.** Select your **Deployment** environment.
3. Click **Settings** and then **Edit** on the upper right side of the page.
4. Select dbt Version 1.2 (latest) or higher.
5. Toggle the Semantic Layer **On.**
6. Copy the Proxy Server URL to connect to your [integrated tool](https://www.getdbt.com/product/semantic-layer-integrations). 
7. If supported by your tool, provide an API service token with metadata access. 
8. You can now run precise and consistent queries with the dbt Semantic Layer.
   
## Public preview
    
We're excited to announce the dbt Semantic Layer is currently available for public preview, which means:
    
&mdash; **Who?** The dbt Semantic Layer is open to all dbt Cloud tiers (Developer, Team, and Enterprise) during public preview. Developer accounts will be able to query the Proxy Server using SQL, but will not be able to browse dbt metrics in external tools, which requires access to the Metadata API. Review our [Product architecture](/docs/integrate/dbt-semantic-layer.md#product-architecture) page for more information on plan availability.

&mdash; **What?** Public preview provides early access to new features, is supported and production ready, but not priced yet. Pricing for the dbt Semantic Layer will be introduced alongside the General Available (GA) release. There may also still be additions or modifications to product behavior. 

&mdash; **When?** Public preview will end once the dbt Semantic Layer is available for GA. After GA, the dbt Semantic Layer will only be available to dbt Cloud **Team** and **Enterprise** plans.

&mdash; **Where?** Public preview is enabled at the account level so you don’t need to worry about enabling it per user.

&mdash; **Why?** Public preview is designed to test the functionality and collect feedback from the community on performance, usability, and documentation. 
    
    
## Next steps

Are you ready to define your own metrics and bring consistency to data consumers? Review the following documents to understand how to structure, define, and query metrics, and set up the dbt Semantic Layer: 

- [dbt metrics](/docs/building-a-dbt-project/metrics) for in-depth detail on attributes, properties, filters, and how to define and query metrics
- [`FUTURE BLOG POST`](URL) to understand best practices for designing and structuring metrics in your dbt project
- [Integrate with dbt](/docs/integrate/setup-dbt-semantic-layer.md#set-up-dbt-semantic-layer) to learn about the dbt Semantic Layer
- [Getting started with the Semantic Layer](https://docs.getdbt.com/blog/getting-started-with-the-dbt-semantic-layer) blog post to see further examples