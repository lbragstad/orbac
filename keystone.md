### unprotected policies

Authentication is in itself a protective operation, but it isn't protected by
policy in this sence because the policy is essentially in code.

```
- authenticate_for_token
```

### protected with a valid token

Allow users that hold a valid token the ability to ask for domains and projects
they have access to, list regions, validate their own tokens, and list group
memberships.

```
# catalog
- identity:get_auth_catalog

# projects and domains
- identity:get_project
- identity:get_auth_projects
- identity:get_auth_domains
- identity:list_projects_for_user
- identity:list_domains_for_user

# token validation
- identity:check_token
- identity:validate_token

# passwords and security requirements
- identity:get_security_compliance_domain_config
- identity:change_password

# regions
- identity:get_region
- identity:list_regions

# group memberships
- identity:list_groups_for_user
```

### role: keystone_observer

Users with this role on project A should be able to list the credentials they
have on a project. As a user with the observer role on project A, I should be
able to see credentials given to my by an administrator.

```
# credentials (including ec2)
- identity:list_credentials
- identity:get_credential
- identity:ec2_get_credential
- identity:ec2_list_credentials
```

### role: keystone_member

Users with this role should be able to manage their own credentials and create
trusts.

```
# credentials (including ec2)
- identity:create_credential
- identity:update_credential
- identity:delete_credential
- identity:ec2_create_credential
- identity:ec2_delete_credential

# trusts
- identity:create_trust
- identity:list_trusts
- identity:list_roles_for_trust
- identity:get_role_for_trust
- identity:delete_trust
```

### role: keystone_domain_admin

Users with this role on a domain should have ability to control resources
*within* that domain.

```
# domains and domain configuration
- identity:get_domain
- identity:create_domain_config
- identity:get_domain_config
- identity:update_domain_config
- identity:delete_domain_config
- identity:get_domain_config_default

# domains specific roles
- identity:get_domain_role
- identity:list_domain_roles
- identity:create_domain_role
- identity:update_domain_role
- identity:delete_domain_role

# projects
- identity:list_projects
- identity:list_user_projects
- identity:create_project
- identity:update_project
- identity:delete_project

# users
- identity:get_user
- identity:list_users
- identity:create_user
- identity:update_user
- identity:delete_user

# groups
- identity:get_group
- identity:list_groups
- identity:create_group
- identity:update_group
- identity:delete_group

# user/group membership
- identity:list_users_in_group
- identity:remove_user_from_group
- identity:check_user_in_group
- identity:add_user_to_group

# identity providers
- identity:create_identity_provider
- identity:list_identity_providers
- identity:get_identity_providers
- identity:update_identity_provider
- identity:delete_identity_provider

# mappings
- identity:create_mapping
- identity:get_mapping
- identity:list_mappings
- identity:delete_mapping
- identity:update_mapping

# protocols
- identity:create_protocol
- identity:update_protocol
- identity:get_protocol
- identity:list_protocols
- identity:delete_protocol

# service providers
- identity:create_service_provider
- identity:list_service_providers
- identity:get_service_provider
- identity:update_service_provider
- identity:delete_service_provider
```

### role: keystone_global_observer

Users with this role assignment on the "admin" project should have the ability
to do non-destructive operations on cloud-level resources.

```
# services
- identity:get_service
- identity:list_services

# endpoints and endpoint groups
- identity:get_endpoint
- identity:list_endpoints
- identity:list_endpoint_groups
- identity:get_endpoint_group
- identity:list_projects_associated_with_endpoint_group
- identity:list_endpoints_associated_with_endpoint_group
- identity:get_endpoint_group_in_project
- identity:list_endpoint_groups_for_project
- identity:list_projects_for_endpoint

# policies
- identity:list_policies
- identity:get_policy

# grants
- identity:check_grant
- identity:list_grants

# roles and implied roles
- identity:get_implied_role
- identity:list_implied_roles
- identity:check_implied_role
- identity:list_role_inference_rules
- identity:get_role
- identity:list_roles
- identity:list_role_assignments
- identity:list_role_assignments_for_tree

# endpoint/service policy association
- identity:check_policy_association_for_endpoint
- identity:check_policy_association_for_service
- identity:check_policy_association_for_region_and_service
- identity:get_policy_for_endpoint
- identity:check_endpoint_in_project
- identity:list_endpoints_for_project
- identity:list_endpoints_for_policy
```

### role: keystone_admin

```
# regions
- identity:create_region
- identity:update_region
- identity:delete_region

# services
- identity:create_service
- identity:update_service
- identity:delete_service

# domains
- identity:list_domains
- identity:create_domain
- identity:update_domain
- identity:delete_domain

# endpoints and endpoint groups
- identity:create_endpoint
- identity:update_endpoint
- identity:delete_endpoint
- identity:create_endpoint_group
- identity:update_endpoint_group
- identity:delete_endpoint_group
- identity:add_endpoint_group_to_project
- identity:remove_endpoint_group_from_project

# policies
- identity:create_policy
- identity:update_policy
- identity:delete_policy

# grants
- identity:create_grant
- identity:revoke_grant

# roles and implied roles
- identity:create_implied_role
- identity:delete_implied_role
- identity:create_role
- identity:update_role
- identity:delete_role

# endpoint/service policy association
- identity:create_policy_association_for_endpoint
- identity:delete_policy_association_for_endpoint
- identity:create_policy_association_for_service
- identity:delete_policy_association_for_service
- identity:create_policy_association_for_region_and_service
- identity:delete_policy_association_for_region_and_service
- identity:add_endpoint_to_project
- identity:remove_endpoint_from_project
```

------------------------------------------------------------------------------

```
#"identity:list_revoke_events": "rule:service_or_admin"
#"identity:validate_token_head": "rule:service_or_admin"
#"identity:revocation_list": "rule:service_or_admin"
#"identity:revoke_token": "rule:admin_or_token_subject"
```
