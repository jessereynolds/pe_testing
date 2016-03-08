# Basic Testing

- Spun up a master and agent on vmpooler
- Used the file resource within profile::motd to set the motd file
- Puppet runs error out:


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
