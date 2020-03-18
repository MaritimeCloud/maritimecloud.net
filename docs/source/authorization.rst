Authorization
===============
Another central aspect of Identity Management is the concept of authorization which is the process of determining a set of permissions that is granted to a specific trusted identity. In all practical senses, authorization follows authentication. Once a system knows who you are, the system can determine what is appropriate for you to be able to see or do. authorization can be determined based on the user identity alone, but in most cases requires additional attributes about the user, such as role, title, flag state, etc.

Authorization can typically be handled in two ways.

Locally by the application or service that is being accessed.

Centralizing the authorization policy decisions regardless of the location of the user or the application/service

Since authorization can always be done locally by the application that is being accessed, for example by storing user rights in a local database, we have decided that MCP will not prioritize central support for authorization for the next milestones. Instead focusing on getting authentication right.

Before implementation of centralized support for authorization can begin there are some obstacles that lie ahead. Mainly because there, unlike authentication, are no good standards for doing authorization.

Even though there are no real standards there are a number of approaches that are commonly used. The most commonly used probably being role-based access control (RBAC). RBAC is an access control mechanism defined around roles and privileges. The components of RBAC such as role-permissions, user-role and role-role relationships make it simple to do user assignments of entitlements. However, adopting RBAC for MCP opens some questions. For example, who defines the roles and are they global roles. Or are they local to a particular service or a particular organization. For example, an "administrator" role might entail a list of certain privileges in one organization and another list of privileges in another organization.

Given these issues and many other we have to decide not to implement central authorization in MCP. Instead MCP will provide the information that can be used for authorization in the services.

As described in the sections about [OpenID Connect] and [Certificate Attributes], both authentication methods provides information about the authenticated user that can be used for authorization:

* The organization the entity belong to.

* The permissions/roles/groups the entity has been assigned by the organization.

When an organization wishes to use a service, the organization will then make an agreement with that about how access levels shall be defined in that service, based on the permissions. This will have to be done for each service the organization wishes to use.

Authorization in MIR
^^^^^^^^^^^^^^^^^^^^

As an example of how authorization can be done, let us have a look at how it is handled inside the MCP Identity Registry. When it comes to authorization, the Identity Registry will have the same information about its users as any other service in MCP.

The Identity Registry currently has these roles:

A few things should be noted:

* "Maintain" (as mentioned in the table above) means to be able to create, update and delete, as well as issuing and revoking certificates.

* Excluding entities with the role ROLE_SITE_ADMIN, it is not possible for entities to see entities from other organizations.

* A ROLE_SITE_ADMIN can maintain entities and organizations beyond his own organization.

* Any entity, regardless of roles, can see all entities from its own organization, though some sensitive information from services is filtered for non-admins.

* Only a ROLE_SITE_ADMIN can assign ROLE_SITE_ADMIN and ROLE_APPROVE_ORG roles.

* A ROLE_APPROVE_ORG can create a user for an organization if and only if there is no users for the organization (this is used for creating the first administrative user for an organization).

In this example we will focus on ROLE_USER and ROLE_ORG_ADMIN. Let us assume that an Organization (DMA) wants to grant members of the internal "E-navigation" department administrative rights in the MCP Identity Registry. In DMAs Identity Provider setup the department name is automatically added to the "permissions" attribute. So to make this mapping the current DMA administrator sets up a role mapping between the permission "E-navigation" and the role ROLE_ORG_ADMIN. Once this is done, all members of the DMA E-navigation department will have administrative rights for the DMA organization inside the Identity Registry. As noted earlier, these rights only apply inside the Identity Registry. Other services must create a similar setup with mapping of roles and permissions.
