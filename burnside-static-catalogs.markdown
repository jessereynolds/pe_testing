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


  
