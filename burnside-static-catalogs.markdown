# Testing static catalogs in burnside

## Scenarios:

- Orchestration Services Disabled
  - Cached Catalogs Enabled
  - Cached Catalogs Disabled
- Orchestration Services Enabled
  - Cached Catalogs Enabled
  - Cached Catalogs Disabled
  - 
  
## Workflow:

- production environment coming from production branch of control-repo at https://github.com/jessereynolds/control-repo.git
- '/etc/motd' being managed with a file resource using the source attribute
- Commit a change to the source file for /etc/motd
- Trigger puppet run from orchestrator client tools (if orchestration services are enabled), observe state of file under management
- Trigger a puppet run using the 'run puppet' button in the UI, observe state of file under management
- Trigger a puppet run using `puppet agent -t`, observe state of file under management
- 
