# Static Catalog Testing - File

- Spun up a master and agent on vmpooler
- Set up code manager and file sync by setting the following parameters on the puppet_enterprise::profile::master class within the PE Master group:
  - code_manager_auto_configure = true
  - file_sync_enable = true
  - r10k_remote = "https://github.com/jessereynolds/control-repo-burnside-testing.git"
- Puppet run on the master
- Create a group 'Burn' that has the agent vm pinned, and add the class 'profile::motd' from the control-repo
- Puppet run on the agent
- Boom:

`puppet agent -t` output:

```
[root@umh45to93xipdps ~]# puppet agent -t
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts
Info: Caching catalog for umh45to93xipdps.delivery.puppetlabs.net
Info: Applying configuration version '02d6d08e61135e31cfb6b3a99c61f79f47a7b084'
Error: Error 403 on SERVER: Request Denied: A /static_file_content request must be a file within the files directory of a module.
Error: /Stage[main]/Profile::Motd/File[/etc/motd]/content: change from {md5}d41d8cd98f00b204e9800998ecf8427e to {md5}79e392a3a038099b5d0ba190d94d21eb failed: Error 403 on SERVER: Request Denied: A /static_file_content request must be a file within the files directory of a module.
Notice: Applied catalog in 0.72 seconds
```

puppetserver-access.log:

```
10.32.121.86 - - [07/Mar/2016:21:35:01 -0800] 
  "GET /puppet/v3/static_file_content/site/profile/files/motd?environment=production&code_id=urn%3Apuppet%3Acode-id%3A1%3A124b21726da4e203d3745d32c7e769cf40523c22%3Bproduction HTTP/1.1" 
  403 101 "-" "Ruby" 2
```

control-repo at [revision 02d6d08e](https://github.com/jessereynolds/control-repo-burnside-testing/blob/02d6d08e61135e31cfb6b3a99c61f79f47a7b084/site/profile/manifests/motd.pp)

puppetserver.log in debug mode on a later test (using motd.txt as the source this time):

```
2016-03-08 03:29:13,227 DEBUG [qtp1958992479-74 - /puppet/v3/static_file_content/site/profile/files/motd.txt?environment=production&code_id=urn%3Apuppet%3Acode-id%3A1%3A4c1517d451791d6ea8acd771d51f5c00c3cd3e8
a%3Bproduction] [o.e.j.i.WriteFlusher] write: WriteFlusher@1132a13a{IDLE} [HeapByteBuffer@66e23b65[p=0,l=140,c=8192,r=140]={<<<HTTP/1.1 403 Forb...z-SNAPSHOT)\r\n\r\n>>>g: chunked\r\nServe...\x00\x00\x00\x00\
x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00},HeapByteBuffer@1768c5bd[p=0,l=101,c=32768,r=101]={<<<Request Denied: A...ry of a module.>>>petlabs/puppet/mo...rver","server",}]
```
