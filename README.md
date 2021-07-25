ansible-schedule-duplicity
=========

Set up scheduled (cronjob) backups using the [Duplicity](http://duplicity.nongnu.org/) backup tool, which supports
incremental encrypted backups to local filesystem mounts and various cloud storage locations, e.g. Dropbox.

Note that Duplicity and various Python3 modules for backend support will be installed from PyPI into a virtualenv at
`/opt/duplicity_venv`: see the `install_duplicity` option for ways to change that.

Requirements
------------
None.

Role Variables
--------------

**Required Variables**

```yaml
duplicity_target_paths:
  - path: /opt/my-awesome-config
    name: awesome-config
  - path: /etc/nginx
```
A list of paths to back up, and optional names for the backups. If no name is given, the full path is used.

```yaml
backup_url_stem: dpbx:///my_folder
```
Start of the backup path, to which the `name` or `path` above will be appended. See the
[Duplicity manpage](http://duplicity.nongnu.org/vers7/duplicity.1.html) for how to use various backends.

**Optional Variables**
```yaml
gpg_key: duplicity
```
Passphrase to use for backup encryption: you probably want to set this in a vault file!

```yaml
backup_env_vars:
  DPBX_ACCESS_TOKEN=ABC123ImNotARealDropboxToken
```
List of environment variables to set for the backup tool. Empty by default, but the example above is for Dropbox.

```yaml
backup_schedule: 0 3 * * *
full_backup_after: 1W
delete_backups_after: 1Y
```
Backup schedule controls. `backup_schedule` is a cron line defining how often to run Duplicity, which will run an
incremental backup if it can, unless the period in `full_backup_after` has passed, in which case it will run a full
backup. Finally, old backups are deleted after `delete_backups_after` if they aren't referenced by newer incremental
backups. See [the manpage](http://duplicity.nongnu.org/vers7/duplicity.1.html#sect8_) for valid time range values.

```yaml
run_backup_now: true
```
Should we run a full backup immediately as well as the cronjob?

```yaml
install_duplicity: true
duplicity_install_package: duplicity
duplicity_venv_path: /opt/duplicity_venv
duplicity_binary: "/usr/local/bin/duplicity"
```
Should we install Duplicity, and if so, where (a Python3 virtualenv will be created for it). We'll also write a wrapper
script to `duplicity_binary` to execute it.

```yaml
duplicity_install_name: duplicity
```
Customise the name of this backup installation in cronjobs and files, to allow the role to be used multiple times on
a host.

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- hosts: backup_targets
  roles:
      - role: ansible-schedule-duplicity
        duplicity_target_paths:
          - name: user-data
            path: /opt/user-data
        backup_url_stem: dpbx:///duplicity-backups
```

License
-------

MIT

Author Information
------------------

Sam Mesterton-Gibbons <sam@samn.co.uk>