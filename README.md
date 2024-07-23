# Ansible Role: autofs

Install and configure autofs. Creates and removes mountpoints as needed.

## Requirements

N/A

## Role Variables

### Defaults

| **Variable Name**       | **Type**| **Default Value**| **Description**|
| :-----------------------| :------:| :---------------:| :--------------|
| autofs_maps:            | list map| []               | List of mountpoints, see: [Autofs_maps](#autofs_maps).|
| autofs_options:         | string  | ""               | String of options, these options will be the default for all mountpoints.|
| autofs_state:           | string  | "present"        | When `"present"` autofs will be installed, configured and the mount point will be created. When `"absent"` autofs and its files and all specified mounts and mountpoints will be removed. |
| autofs_use_misc_device: | bool    | true             | If `true` `USE_MISC_DEVICE="yes"` in `default/autofs`.|
| autofs_slash_substitute:| string  | "~"              | Character to substitute with `/`. NOTE: don't change this if you already have mountpoints configured.|

### Autofs_maps

| **Variable Name**| **Required**| **Type**| **Default Value**| **Description**|
| :----------------| :----------:| :------:| :---------------:| :--------------|
| mountpoint:      | yes         | string  | ""               | path to the mountpoint, can also be `"/-"`.|
| options:         | no          | string  | ""               | Options for the mountpoint.|
| mounts:          | no          | list map| []               | List of mounts, see: [Mounts](#mounts). When mounts are added or marked as `absent` autofs will temporarily remove this mountpoint from the config, in order to update the filesystem.|
| state:           | no          | string  | "present"        | When `"present"` the mountpoint will be created, when `"absent"` the mountpoint and all mounts in it will be removed form the config and file system.|

Please note that when mounts is empty, the mountpoint will be removed regardless of state is "`present"` or `"absent"`.

#### Mounts

| **Variable Name**| **Required**| **Type**| **Default Value**| **Description**|
| :----------------| :----------:| :------:| :---------------:| :--------------|
| path:            | yes         | string  | ""               | Path to the local folder that will act as a mount. Please note that when this is changed the `mountpoint:` will temporarily be removed from the config. |
| dest:            | yes         | string  | ""               | Remote path in autofs syntax, take note that as in the autofs syntax some charters require escaping.|
| options:         | no          | string  | ""               | Options for how to connect to the `dest:`.|
| state:           | no          | string  | "present"        | When `"present"` the mount will be created in the file system. when `"absent"` the mount will be removed from the file system. please take note that the `mountpoint:` will temporarily be removed from the config.|

## Dependencies

N/A

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: tinyblargon.autofs
      vars:
        autofs_maps:
          - mountpoint: /mnt
            options: "--timeout 120"
            mounts:
              - path: backup
                options: "-fstype=nfs4,rw,soft,noexec"
                dest: "backup.example.com:/backup"
          - mountpoint: /-
            mounts:
              - path: "/var/www"
                dest: "data.example.com:/www"
                options: "-fstype=nfs4,r,soft,noexec"
              - path: "/root/opt"
                dest: "/opt/test"
                options: "-fstype=bind"
        autofs_options: "--timeout 60"
        autofs_state: "present"
        autofs_use_misc_device: true
        autofs_slash_substitute: "~"
```

## License

MIT
