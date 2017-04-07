## Upgrade

There are two problems that inhibit upgrading to a better policy:

1. Policies are super generic and elementary (i.e. `admin` roles only)
2. Lack of better policy out of the box has pushed deployer to roll their own

### Upgrade Constraints

- Must be backwards compatible if deployers have their own custom policy
  definitions
- Deployers need a way to know exactly what their policy is, including the
  operations they've overridden
- Users should only lose access in an upgrade if appropriate (i.e. a user is
  suppose to be a member and during the upgrade they lose the ability to live
  migrate instance)

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
