cloudsync
=========

Sync a local filesystem incremental and encrypted with google drive simliar to rsync or restore the encrypted data back to a local filesystem. It works as a complete backup solution for your private data.

Other compareable backup solution like [duplicity](http://duplicity.nongnu.org) are uploading one big encrypted 'base' archive with additional delta archive files. After some month you must upload a new fresh 'base' archive to avoid hundred of delta files. This is problematically for private async dsl connections. To solve this issues each file is encrypted and uploaded separately.

To get a first impression you should take a look at [these screenshot](https://github.com/HolgerHees/cloudsync/wiki/Home).

The encryption is based on OpenPGP with AES 256 and a passphrase. It is possible to decrypt uploaded files with a normal OpenPGP compatible tool like 'gpg' or 'gpg2'.

Encrypted data includes:
- filecontent
- filename
- filesize
- createtime 
- modifytime
- accesstime
- gid
- uid
- permissions
- md5 checksum

The filecompare is done by comparing the archived metadata. It uses a local cachefile to speedup the incremental update. The local cachefile is completly restoreable by analysing the serverside archived metadata.

To provide additional cloud targets like Dropbox or Microsoft OneDrive just implement 6 functions from the interface [Connector.java](https://github.com/HolgerHees/cloudsync/blob/master/src/cloudsync/connector/RemoteConnector.java). 

**java >= 7 and Java Cryptography Extension (JCE) is required**

It is tested and works fine on linux but with a few fixes it should also work on windows or osx. Feel free to submit patches for improvements and bugfixes!

To use it, copy ```'config/cloudsync.config.default'``` to ```'config/cloudsync.config'``` and set your PASSPHRASE, REMOTE_CLIENT_ID and REMOTE_CLIENT_SECRET. To get the last two parameter follow https://github.com/HolgerHees/cloudsync/wiki/Google-Client-Credentials.

to create a backup of '/data', call:

```./cloudsync --backup /data --name dataBackup```

to restore a backup into '/restore', call:

```./cloudsync --restore /restore --name dataBackup```

for a complete list of options, see below:

```
usage: cloudsync <options>
 -b,--backup <path>                    Create or refresh backup of <path>
 -r,--restore <path>                   Restore a backup into <path>
 -c,--clean <path>                     Repair 'cloudsync*.cache' file and put leftover file into <path>
 -l,--list                             List the contents of an backup
 -n,--name <name>                      Backup name of --backup, --restore, --clean or --list
    --config <path>                    Config file path. Default is './config/cloudsync.config'
    --followlinks <extern|all|none>    How to handle symbolic links
                                       <extern> - follow symbolic links if the target is outside from the current
                                       directory hierarchy - (default)
                                       <all> - follow all symbolic links
                                       <none> - don't follow any symbolic links
    --duplicate <stop|update|rename>   Behavior on existing files
                                       <stop> - stop immediately - (default for --backup and --restore)
                                       <update> - replace file
                                       <rename> - extend the name with an autoincrement number (default for --clean)
    --history <count>                  Before remove or update a file or folder move it to a history folder.
                                       Use a maximum of <count> history folders
    --include <pattern>                Include content of --backup, --restore and --list if the path matches the regex
                                       based ^<pattern>$. Multiple patterns can be separated with an '|' character.
    --exclude <pattern>                Exclude content of --backup, --restore and --list if the path matches the regex
                                       based ^<pattern>$. Multiple patterns can be separated with an '|' character.
    --nopermissions                    Don't restore permission, group and owner attributes
    --nocache                          Don't use 'cloudsync*.cache' file for --backup or --list (much slower)
    --forcestart                       Ignore a existing pid file. Should only be used after a previous crashed job.
    --logfile <path>                   Log message to <path>
    --cachefile <path>                 Cache data to <path>
    --test                             Start a 'test' run of --backup or --restore.
 -h,--help                             Show this help
```
