# RBAC in OpenStack

Notepad for OpenStack RBAC thoughts and discussion


[orbac](https://www.draw.io/?lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=orbac.xml#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Flbragstad%2Forbac%2Fmaster%2Forbac.xml)

[orbac with
domains](https://www.draw.io/?lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=orbac-domain.xml#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Flbragstad%2Forbac%2Fmaster%2Forbac-domain.xml)


## Overview

The objective of this exercise it to try and figure out we want out of
OpenStack and Role Based Access Control (RBAC). There are for sure going to be
implementation details the inhibit being able to immediately adopt the
*perfect* model, but let's define what we want the idea model to look like.

## Constraints

- Must adhere the principal of least privilege, users should have only the
  privileges they need to do their job and nothing more
- A user should be able to discover what they can do based on the role(s)
  he/she has
- Must be able to establish role hierarchies where privileges of a child role
  are accessible to a user given the parent role
- Must provide the ability for operators to override policy if they have their
  own definitions
- Operators need a way to know exactly what their policy is, including the
  operations they've overridden

## Roles Definitions

What would it look like for each project to group privileges in ways that fit
the following:

#### protected

A user with a valid token should be able to do these things. These are
operations that don't require a project scope. For example, so long as Morgan
is a user in the system, he should be able to validate tokens, change his
password, or list projects he has the ability to operate within.

#### `*_observer`

Includes `protected` operations in addition to per-service non-destructive
operations. For example, giving Rick the `cinder_observer` role on `Project A`
allows him the ability to list volumes within `Project A`.

Each project will have their own definition of this role.

#### `observer`

This role is a combination of all `*_observer` roles for each project.
Assigning this role to Machonne on `Project B` gives her the ability to list
all volumes, instances, networks within `Project B`.

#### `*_member`

This role inherits all privileges of the service-specific observer in addition
to permissions for a standard or regular user. For example, assigning Glenn the
`nova_member` role on `Project C` would all him the ability to list and create
instances in `Project C`.

#### `member`

This role is a combination of all service-specific member roles, in addition to
all service-specific observer roles. For example, assigning Eugene the `member`
role on `Project D` allows him the ability to create and view instances,
networks, volumes, etc.

#### `*_domain_admin`

This role inherits all privileges of the service-specific member role in
addition to all service-specific actions a domain owner should be able to do.
For example `Domain Foo` has two projects, `development` and `staging`. The
`development` project contains two instances called `instance1` and
`instance2` and `staging` contains an instance called `deploy-node`. Giving
Maggie the `nova_domain_admin` role on `Domain Foo` should give her the ability
to manage `instance1`, `instance2`, and `deploy-node` since all three are
within `Domain Foo`.

#### `domain_admin`

This role inherits all service-specific domain administrator roles. Assigning
Abraham the `domain_admin` role on `Domain E` will give him the ability to add
new federated identity providers so that users within his domain can
authenticate with their corporate credentials. At the same time, Abraham will
be able to manage all volumes within `Domain E`.

#### `*_admin`

This role inherits all privileges of the service-specific domain administrator
role. This should be reserved for privileges that only administrators should be
able to do. For example, assigning Daryl the `nova_admin` role will allow him
to live migrate instances from a host that is about to undergo maintenance.

#### `admin`

This role inherits from all service-specific administrator roles and allows
users the ability to administer the entire deployment. For example, granting
Carol the `admin` role will allow her to add new services and endpoints as well
as live migrate instances from hosts that are about to undergo maintenance.

## Upgrade

There are two problems that inhibit upgrading to a better policy:

1. Policies are super generic and elementary (i.e. `admin` roles only)
2. Lack of better policy out of the box has pushed deployer to roll their own

### Prerequisites

- Move policy into code so that operators can use it to generate exact policy
  files
- Defining the various roles for each project (i.e. `*_observer`, `*_member`,
  `*_domain_admin`, `*_global_observer`, `*_admin`)

### Upgrade Process

This is going to need work, but ideally we need to be able to provide a series
of steps that allows a deployment to get to better policy in with as little
pain as possible. All steps would be taken as an operator or administrator
performing the upgrade (note this is only taking into consideration the policy
operations required, not the entire upgrade flow):

1. Generate an updated policy file that includes overrides for each service
2. Ensure all policy files are in place and being used
3. Upgrade all the services, backwards-compat should be maintained because the
   policy files from step one are in place
4. Create roles required for the new model, but don't assign them to any users
   or groups
5. Assign users and groups new roles, so they should have all pre-existing
   roles from the last release and new roles for the new model (this is going
   to be a deployment wide assessment of permissions)
6. Update each service and either remove or prune the various policy files,
   letting the new model take over where applicable
7. Remove unused (old) role assignments from users and groups
8. Delete old roles so long as they are not used in any policy files


# POLICIES

Who can do What on a Thing?

```
{
    'policies': [
        {
            'who': [*, user, group],
            'what': [operation, role],
            'thing': [*, domain, project, object]
        }
    ]
}

```

```
{
    'policies': [
        {
            'actor': [
                {'user': 'eefd423f4f494c258dd4276dc07aaab7'}
                {'group': '0644b4c0b0e8432a93e9295f493c40b6'}
            ],
            'action': [
                'os_compute_api:servers:create',
                'os_compute_api:servers:delete',
                'volume:create'
            ],
            'resource': [
                'project:b9f8b78e10fb4fe9b0a87bb6d60f0a15'
                'domain:9de5d33310eb4d03a6269cf88ac81c70'
            ]
        }
    ]
}
```
