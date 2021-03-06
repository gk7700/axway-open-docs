---
title: Agent security - AWS Gateway
linkTitle: Agent security - AWS Gateway
draft: false
weight: 80
description: This section describes the main security features of the AWS API Gateway agents.
---
{{< alert title="Note" color="primary" >}}The Axway API Gateway connectivity to AMPLIFY Central is currently available in an alpha review mode; current functionality and configuration may change before release. Therefore, this connectivity is available for trial use only and is not supported for production API management or connectivity.{{< /alert >}}

## Information security

The agents communicate to AWS API Gateway Manager, API Manager, APIGateway Manager, and AMPLIFY CENTRAL servers over TLS-encrypted HTTP connections by default. Except for AWS API Gateway Manager, the agents can be configured to enforce various TLS protocol versions. You can specify both a minimum and a maximum version. The default minimum version is TLS1.2, and the default maximum version is TLS1.3. Either of these values can be changed to one of the following: TLS1.0, TLS1.1, TLS1.2, TLS1.3. For additional information, see [Discovery Agent flags](/docs/central/connect-api-manager/discovery-agent-flags/).

Agents can also be configured to use one or more specific SSL. The ciphers that are configured within the agents to be used by default are:

TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305

TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305

TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

For additional information, see [Discovery Agent flags](/docs/central/connect-api-manager/discovery-agent-flags/).

The full list of cipher suites is extensive, and many may not be supported by the various servers. The cipher suites listed above are generally considered to be the most secure (at the time of this writing). Here is a full list of cipher suites that the agents allow for configuration:

TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA

TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA

TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305

TLS_ECDHE_ECDSA_WITH_RC4_128_SHA

TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305

TLS_ECDHE_RSA_WITH_RC4_128_SHA

TLS_RSA_WITH_RC4_128_SHA

TLS_RSA_WITH_3DES_EDE_CBC_SHA

TLS_RSA_WITH_AES_128_CBC_SHA

TLS_RSA_WITH_AES_128_CBC_SHA256

TLS_RSA_WITH_AES_128_GCM_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA

TLS_RSA_WITH_AES_256_GCM_SHA384

TLS_AES_128_GCM_SHA256

TLS_AES_256_GCM_SHA384

TLS_CHACHA20_POLY1305_SHA256

The Traceability Agents also communicate to the AMPLIFY search logs service using the Lumberjack protocol over a TCP connection. The connection URL and port can be configured by changing the LOGSTASH_URL in your configuration file. See [Traceability Agent variables](/docs/central/connect-api-manager/traceability-agent-variables/).

## Proxying

The agents are configured to use a TLS-encrypted HTTP connection by default. Customers can also configure the agents to use a proxy. HTTP, HTTPS, and SOCKS5 proxies are supported. For general information on proxying see [Security concerns](/docs/central/connect-api-manager/security-concerns/). For specific proxy configuration, consult the documentation for your proxy.

## Agent client/server

The agents use HTTPS connections to its API servers for communication between a running agent, the API Manager, AWS API Gateway, or AMPLIFY Central. The Traceability Agents also use the Lumberjack protocol over TCP to communicate to the AMPLIFY search logs service.

Authentication to the servers is enforced through tokens, username/password, or public/private keys, depending upon what is required by the particular server. All JWT tokens and private keys and secrets stay within the environment of the agent. Public keys are registered in AMPLIFY Central. Configuration details for these settings can be found at [Discovery Agent variables](/docs/central/connect-api-manager/discovery-agent-variables/) and [Traceability Agent variables](/docs/central/connect-api-manager/traceability-agent-variables/). For the AWS API Gateway Discovery and Traceability agents, the configuration details are at [Deploy your agents](/docs/central/connect-aws-gateway/deploy-your-agents-1).

## Agent security scans

Axway’s Product Security Office dictates that the agents undergo regular security assessments of supporting infrastructure and application components, including active scans of the core code. Each build of the code runs various static code analysis and software vulnerability tools. We track remediation of findings according to Axway’s Security policy.

All code in the agents is written in Golang. Axway performs regular vulnerability static analysis using the following tools:

* Golint - scans the code for possible coding errors or inconsistencies

* Sonar cube - looks for code reliability issues, security vulnerabilities, and unit test coverage

* Gosec - specific to Golang, and checks the code for security vulnerabilities

* DependencyCheck - scans all library dependencies for security vulnerabilities

* Twistlock - for the agents that run in docker containers, scans the container for security protection within the infrastructure

* IriusRisk - this is the manual creation of threat models of the system by the system architects to identify and mitigate architectural security risks
