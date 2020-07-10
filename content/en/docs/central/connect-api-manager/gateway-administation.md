---
title: Network traffic - API Manager
linkTitle: Network traffic - API Manager
draft: false
weight: 130
description: Learn how to deploy your Discovery Agent and Traceability Agent so that you can manage your Axway API Gateway environment within AMPLIFY Central.
---
{{< alert title="Note" color="primary" >}}The Axway API Gateway connectivity to AMPLIFY Central is currently available in an alpha review mode; current functionality and configuration may change before release. Therefore, this connectivity is available for trial use only and is not supported for production API management or connectivity.{{< /alert >}}

## Before you start

* Read [AMPLIFY Central and Axway API Manager connected overview](/docs/central/connect-api-manager/)
* Be sure you have [Prepared AMPLIFY Central](/docs/central/connect-api-manager/prepare-amplify-central/)
* You will need a basic knowledge of Axway API Management solution
    * Where the solution is running (host / port / path to the logs / users)
    * How to create / publish an API
    * How to call an API

## Objectives

Learn how to install, customize and run your Discovery and Traceability agents.

## Discovery Agent

The Discovery Agent is used to discover new published APIs or any updated APIs. Once they are discovered, the related APIs are published to AMPLIFY Central, in one of the following publication modes, so that they become available for any consumer:

* Catalog item publication (disconnected mode): Customers expose their APIs globally for their consumers but keep the API management at the Gateway level.
* Environment / API Service publication (connected mode): Customers manage their APIs from the AMPLIFY platform.
The Discovery Agent only discovers APIs that have the tag(s) defined in the agent configuration file. See [Filtering apis to be discovered](/docs/central/connect-api-manager/filtering-apis-to-be-discovered/). By default, the filter is empty and thus the agent will discover all published API.

  As soon as an API is published, the identifier of the asset in AMPLIFY Central is kept in a custom field at the API level in API Manager to help the agent remember what is already published.

The agent can run in the following modes:

* With a yaml configuration file having the same name as the agent binary - discovery_agent.yaml:

    * Default: located in the same directory as the agent binary.
    * Optional: use a dedicated folder where the configuration file is located (use the –pathConfig flag in the agent command line to access the file path).
    * Advanced configuration: properties inside the configuration file can reference environment variables. This enables you to set up only one configuration file that addresses different behaviors (depending on the environment variables). See [Discovery Agent variables](/docs/central/connect-api-manager/discovery-agent-variables/).

* With command line arguments. See [Discovery Agent flags](/docs/central/connect-api-manager/discovery-agent-flags/).

### Installing the Discovery Agent

1. Download the latest version of the zip file from the Axway public repository using the following command:

   ```shell
   curl -L "https://axway.bintray.com/generic-repo/v7-agents/v7_discovery_agent/latest/discovery_agent-latest.zip" -o discovery_agent-latest.zip
   ```

2. Unzip the file `discovery_agent-latest.zip` to get the agent binary (`discovery_agent`) and the template configuration (`discovery_agent.yml`).

3. Copy both files (`discovery_agent`, `discovery_agent.yml`) into a folder (i.e., `/home/APIC-agents`) on the machine where the API Manager environment is located.

4. Customize apimanager section by setting configuration values to point to the API Gateway, API Manager, and AMPLIFY Central.  There are 2 options to set values:
   * `env_vars` file
   * `discovery_agent.yml` (example shown below)

The value for *teamID* can be found in [AMPLIFY Central > Access > Teams](https://apicentral.axway.com/access/teams/). 
The value for *tenantID* can be found in AMPLIFY Central Platform > Organization. 
The value for *clientId* can be found in Service Account. See [Create a Service in AMPLIFY Central](/docs/central/connect-api-manager/prepare-amplify-central/).

5. Run the Discovery Agent:
   * Open a shell and run the following commands to start up your agent.  Add necessary [Discovery Agent flags](/doc/central/connect-api-manager/discovery-agent-flags/).
     ```shell
     cd /home/APIC-agents
     ./discovery_agent
     ```

   * To verify that the agent is up and running, open another shell command and run:
      ```shell
      cd /home/APIC-agents
      ./discovery_agent --status
      ```

### Configuring APIManager

#### Tags

1. Configure filters in yaml file to determine which [APIs will be discovered](/docs/central/connect-api-manager/filtering-apis-to-be-discovered/) and published to AMPLIFY Central.

2. Discovery API tags.  Set filtering based on tag name, tag value, partial value and MatchRegEx to discover APIs that can be published to AMPLIFY Central:
   * In API Manager, select front-end API to edit.
   * The tags should match the values in your apimanager configuration file.
   * Save the API and publish.  Once published, the Discovery Agent attempts to match the polling criteria. If it matches, it publishes it to the AMPLIFY Central Catalog and Environments.

      * View from Topology – Environments

      * View from Catalog

   * Once the API is published to the catalog, a reference value is generated by AMPLIFY Central and sent back to the API Manager (`APIC_ID`).  If you want to see that field or customize it, refer to **Add a custom property to APIs** in [Customize API Manager](https://docs.axway.com/bundle/axway-open-docs/page/docs/apim_administration/apimgr_admin/api_mgmt_custom/index.html/index.html#customize-api-manager-data) documentation.

#### Subscriptions

##### Manage client applications on APIManager for subscription process

You can use the **Clients** tab to manage client applications (for example, create, update, or remove client applications that invoke specific APIs). When an application is created, API administrators can also set authentication, quota, and sharing settings on the appropriate tab.

The API administrator must first specify the APIs that an organization is allowed to access before any of its client applications can have access to them. In API Manager, you can only add APIs to an application when they have been added to the organization. For more details, see [Manage access to APIs](/docs/apim_administration/apimgr_admin/api_mgmt_admin/index.html).

##### Create / edit application

Once applications have been created, you can click an application name in the *Managing applications* screen to edit its existing settings on the **Application** tab.

1. Add API to be recognized as an application to be used for subscription.
2. Make sure **Enabled** is active.

##### Manage subscription in AMPLIFY Central

After configuring the application in APIManger, set up catalog subscriptions in AMPLIFY Central.

A consumer initiates the subscription in AMPLIFY Central:

1. Open an AMPLIFY Catalog Item.
2. Click **Subscribe**.
3. Enter the Team and API Manager Application name (created in Step 5 of Manage client applications on APIManager for subscription process).
4. Click **Subscribe**.

The Discovery Agent listens to the subscription event.

## Subscribing workflow

* Associate the API to the selected application
* Send back the subscription status

### Active

* Subscription ID is automatically added to the Custom field of the application.
* If failure, subscription status **Subscription failed** will appear. You can delete the subscription and start again from the **Save the API and publish** step.
* The subscriber consumes the API.

    * Workaround:

      1. In the UI, select the API.
      2. Expand **Manage selected**.
      3. Select **Grant access**.

## Unsubscription workflow

In API Manager, assume there is a FrontEnd API that is published, has been discovered by the Discovery Agent, and has an active subscription to it in AMPLIFY Central. To initiate an unsubscribe to AMPLIFY Central for that Catalog item:

* A user in API Manager unpublishes that API.
* The Discovery Agent discovers the change.
* The subscription status is set to **Unsubscribed**.
* The subscription ID is removed from the application’s **Custom** field.
* The subscription status is set to **Unsubscribed**.

## Managing Subscription notifications

**I don't know what story Shane just finished or what the topic is that explains email notification.**

```shell
subscriptions:
  smtp:
    host: mail.axway.int
    port: 25
    fromAddress: fromaddress@axway.com
    username: fromaddress@axway.com
    password:
    subscribe:
      subject: Subscription Notification
      body: |
        Subscription created for Catalog Item:  <a href= ${catalogItemUrl}> ${catalogItemName} </a> <br/>
        Subscription key: <b>${key}</b>
    unsubscribe:
      subject: Subscription Removal Notification
      body: |
        Subscription for Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a> has been unsubscribed
    subscribeFailed:
      subject: Subscription Failed Notification
      body: |
        Could not subscribe to Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a>
    unsubscribeFailed:
      subject: Subscription Removal Failed Notification
      body: |
        Could not unsubscribe to Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a>
 ```
