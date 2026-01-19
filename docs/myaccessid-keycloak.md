# Federated IAM Using Keycloak and MyAccessID

This guide describes how to connect [Keycloak](https://github.com/keycloak/keycloak), an open-source Identity and Access Management (IAM) solution, with MyAccessID to implement a federated IAM architecture. The examples in this guide are based on implementations from DRI sites that use these tools to provide federated authentication and authorisation.

## Implementing Compliant Authentication and Authorisation with MyAccessID and Keycloak

### Authentication vs Authorisation

Authentication and authorisation serve distinct but complementary purposes:

- **Authentication** verifies *who* a user is.
- **Authorisation** determines *what* an authenticated user is allowed to access.

MyAccessID is used to authenticate a user’s identity, ensuring that the person attempting to log in is who they claim to be.

However, successful authentication alone does not imply that a user is permitted to access a specific service. Keycloak is responsible for authorisation, verifying that the authenticated user has the appropriate permissions to access the requested application or resource.

---

### Authentication with MyAccessID

![requesting authenticated identity](../images/bristol-authn-req.png)

The diagram above illustrates authentication using MyAccessID as implemented in the [BriCS](https://www.bristol.ac.uk/research/centres/bristol-supercomputing/) Isambard-AI service. In this example, a user attempts to log in to the project management application Waldur.

Keycloak acts as an identity broker between the application and the identity provider, i.e between Waldur and MyAccessID. When the user selects the “University Login (MyAccessID)” option on the Keycloak *Choose your identity provider* page, they are redirected to MyAccessID. MyAccessID then prompts the user to authenticate using their institutional credentials.

![receiving authenticated identity](../images/bristol-authn-recv.png)

In this example, the user’s home institution is the University of Bristol and they login using their institutional account. After successful authentication, MyAccessID sends the user’s identity attributes—such as email address, first name, and last name—back to Keycloak.

---

### Authorisation with Keycloak

At this point, the user has successfully authenticated with Keycloak. The next step is to ensure that the user is authorised to access the requested service.

In this architecture, Waldur is the source of truth for project membership and access rights. Keycloak acts as an authorisation broker, determining whether the authenticated user is permitted to access a given service.

To make this decision, Keycloak queries Waldur using a custom Keycloak plugin, which retrieves the user’s project and facility memberships.

- If the user is authorised, Keycloak allows the login flow to continue and issues the appropriate tokens.
- If the user is not authorised, access is denied and the login attempt fails.

This clear separation of authentication and authorisation using MyAccessID and Keycloak enables a secure, flexible, and policy-driven access control model.

To better illustrate this, consider a DRI operating multiple facilities and services. For example, a user may belong to projects on the BriCS facilities Isambard-AI and Isambard3. BriCS may operate additional facilities, each hosting multiple projects.

When the user logs in, they should be able to access only the projects they are a member of across the facilities they are authorised for. They must not be able to access projects or facilities for which they have no authorisation.

The diagram below illustrates the workflow of a user, `cwoods`, accessing projects on the Isambard-AI and Isambard3 clusters via SSH.

At BriCS, access to facilities is performed using signed SSH certificates. This workflow involves the following components:

- **Keycloak** for authentication and authorisation brokering
- **Waldur** for authoritative project and membership data
- **Conch** as the SSH Certificate Authority (SSH CA)
- **Clifton** as the client-side tool used to request SSH certificates

In short, Clifton manages the lifecycle of SSH certificates issued by Conch. With this context, the typical SSH access flow is as follows. In this example, the user has access to:

- `brics` project on Isambard-AI  
- `demo` project on Isambard3  

#### SSH Access Flow

The SSH authorisation process consists of two distinct phases: an **authorisation request** and an **authorisation response**. The diagrams below illustrate the end-to-end flow.

##### Authorisation Request Path (User → Keycloak → Waldur)

![authorisation request flow](../images/bristol-authz-req.png)

This diagram shows how an SSH authorisation request propagates from the user, through Keycloak, to Waldur.

1. The user requests an SSH certificate from **Clifton** in order to access BriCS facilities.
2. Clifton, which is registered as an OIDC client in Keycloak, initiates an authorisation request via **Conch**, the SSH Certificate Authority.
3. Conch redirects the user to **Keycloak** to determine which facilities and projects the user is authorised to access.
4. The user authenticates using **MyAccessID** using their institutional identity.
5. Keycloak queries **Waldur**, via a [custom plugin](https://github.com/isambard-sc/keycloakplugins/tree/main/keycloak-isambard-auth-plugin), to retrieve the user’s project memberships.

---

##### Authorisation Response Path (Waldur → Keycloak → User)

![authorisation response flow](../images/bristol-authz-recv.png)

This diagram shows how the authorisation decision is returned from Waldur, via Keycloak, back to the user.

1. **Waldur** responds to Keycloak's request with the list of projects and facilities the user is authorised to access.
2. **Keycloak** presents an authorisation prompt to the user. Upon approval, the authorisation context is returned to Conch.
3. **Conch** signs the user’s SSH public key and returns the signed certificate.
4. **Clifton** receives the signed certificate, and the user can access the authorised projects via SSH.

### Connecting Keycloak to MyAccessID

Follow the guidance provided in the official
[MyAccessID documentation for connecting Keycloak](https://wiki.geant.org/spaces/MyAccessID/pages/867205135/Connecting+a+Keycloak+RP)
to configure Keycloak for integration with MyAccessID.

The section below highlights configuration settings that are not explicitly covered in the official documentation, but are included here as a reference based on operational experience.

![keycloak-advanced-settings](../images/bristol-keycloak-advanced.png)

The **Trust Email** option is enabled because the email address provided by MyAccessID is already verified. Users authenticate to MyAccessID using their institutional credentials, and the email attribute returned is associated with that verified institutional identity.

Keycloak is configured to automatically create user accounts the first time a user logs in via MyAccessID. To enable this behaviour, set the **First Login Flow Override** to `Auto Create and Link Account`.

To support authorisation based on project membership in Waldur, the **Post Login Flow** is configured to use the `Waldur Check Access` plugin. This allows Keycloak to evaluate whether an authenticated user is authorised to access services based on information retrieved from Waldur.

---

### Attributes, Claims, and Scopes

The configuration of attributes, claims, and scopes may vary between DRI sites depending on local requirements.

For example, at BriCS, the attributes of interest are:

- Email address  
- First name  
- Last name  
- Community User Identifier  

To request these attributes from MyAccessID, the **Scopes** field under the advanced settings is configured as:

`openid profile email`

![keycloak-mappers](../images/bristol-keycloak-mappers.png)

The corresponding **mappers** are configured in Keycloak to consume and map these attributes into the user model, as shown above.

Additionally, users authenticating via MyAccessID are automatically assigned to a dedicated Keycloak group using the **ForceMyAccessIDGroup Mapper**. This grouping simplifies management and policy enforcement for users who log in through MyAccessID.
