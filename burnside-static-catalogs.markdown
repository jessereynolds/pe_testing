# Testing static catalogs in burnside

## Scenarios:

File Sync enabled and:

- Orchestration Services Disabled
  - With agent config `use_cached_catalog=true pluginsync=false`
  - With agent config `use_cached_catalog=false plugingsync=true`
- Orchestration Services Enabled
  - With agent config `use_cached_catalog=true pluginsync=false`
  - With agent config `use_cached_catalog=false plugingsync=true`

  
## Workflow:

- production environment coming from production branch of control-repo at https://github.com/jessereynolds/control-repo-burnside-testing.git
- '/etc/motd' being managed with a file resource using the source attribute
- Commit a change to the source file for /etc/motd
- Trigger puppet run from orchestrator client tools (if orchestration services are enabled), observe state of file under management
- Trigger a puppet run using the 'run puppet' button in the UI, observe state of file under management
- Trigger a puppet run using `puppet agent -t`, observe state of file under management

## Questions:

- Is it possible to reduce the catalog compilation load on the puppet master using static catalogs? If so, would it look like the below? Any other ways to achieve this?
  - Orchestration Services Enabled
  - Use Cached Catalogs Enabled
  - Agent continues to submit facts every 30 minutes
  - Agent doesn't cause a catalog to be compiled for it unless orchestration client triggers a puppet run
- Can Orchestration Services be enabled with you're running an HA environment? (Multiple monolithic installs with postgres replication). If so, how?
- Do Orchestration Services play nicely with proxies? (Eg between masters, between puppet client tools and masters, ...) 

## Environment Setup:

The following vms are created on vmpooler by beaker:
- master0 (Centos 7.0.1406)
- agent0 (Centos 7.0.1406)
  
Configure code-manager as per the [docs](https://docs.puppetlabs.com/pe/latest/code_mgr_config.html#configure-after-pe-installation) ie with the following parameters to the puppet_enterprise::profile::master class within the PE Master group:
- code_manager_auto_configure=true
- file_sync_enabled=true
- r10k_remote="https://github.com/jessereynolds/control-repo-burnside-testing.git"

and do a puppet run on the master. 

Create a user 'bofh' as a member of the Operators group. 

Generate password reset link for 'bofh' user. Paste it into a different browser session and proceed to assign a password to the bofh user.

Configure the puppet-access client on the puppet master for the root user:

```
mkdir -p ~/.puppetlabs
echo "{\"service-url\":\"https://`hostname -f`:4433/rbac-api\"}" > ~/.puppetlabs/puppet-access.conf
```

Login and get a ticket for the bofh user from the rbac service:

```
puppet-access login --lifetime 10000h
```

You can now trigger code deployments like this:

```
# puppet-code deploy production --wait
Deploying environment: production
[{"environment":"production","id":3,"status":"complete","file-sync":{"environment-commit":"8a1ce95471870cc8c73256a9242498b68076665f","code-commit":"2cca713241959161fb2564778dde18c43557ea3b"},"deploy-signature":"d8a205db6125e0faaabc65daf800352ae71a8b86"}]
```
