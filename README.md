[![logo](https://raw.githubusercontent.com/bomura/samba/master/logo.jpg)](https://www.samba.org)

# Samba

Samba docker container

# What is Samba?

Since 1992, Samba has provided secure, stable and fast file and print services
for all clients using the SMB/CIFS protocol, such as all versions of DOS and
Windows, OS/2, Linux and many others.

# How to use this image

By default there are no shares configured, additional ones can be added.

## Hosting a Samba instance

    sudo docker run -it -p 139:139 -p 445:445 -d bomura/samba

OR set local storage:

    sudo docker run -it --name samba -p 139:139 -p 445:445 \
                -v /path/to/directory:/mount \
                -d bomura/samba

## Configuration

    sudo docker run -it --rm bomura/samba -h
    Usage: samba.sh [-opt] [command]
    Options (fields in '[]' are optional, '<>' are required):
        -h          This help
        -i "<path>" Import smbpassword
                    required arg: "<path>" - full file path in container
        -n          Start the 'nmbd' daemon to advertise the shares
        -o "<opt1>[;opt2;opt3;...;optN]" Add global options
        -p          Set ownership and permissions on the shares
        -s "<name;/path>[;browsable;readonly;guest;users;admins;others]" Configure a share
                    required arg: "<name>;<comment>;</path>"
                    <name> is how it's called for clients
                    <path> path to share
                    NOTE: for the default values, just leave blank
                    [browsable] default:'yes' or 'no'
                    [readonly] default:'yes' or 'no'
                    [guest] allowed default:'yes' or 'no'
                    [users] allowed default:'all' or list of allowed users
                    [admins] allowed default:'none' or list of admin users
                    [others] other options
        -t ""       Configure timezone
                    possible arg: "[timezone]" - zoneinfo timezone for container
        -u "<username;password>"       Add a user
                    required arg: "<username>;<passwd>"
                    <username> for user
                    <password> for user
                    [ID] for user
        -w "<workgroup>"       Configure the workgroup (domain) samba should use
                    required arg: "<workgroup>"
                    <workgroup> for samba

    The 'command' (if provided and valid) will be run instead of samba

ENVIRONMENT VARIABLES (only available with `docker run`)

 * `NMBD` - As above, enable nmbd
 * `TZ` - As above, set a zoneinfo timezone, IE `EST5EDT`
 * `WORKGROUP` - As above, set workgroup

**NOTE**: if you enable nmbd (via `-n` or the `NMBD` environment variable), you
will also want to expose port 137 and 138 with `-p 137:137/udp -p 138:138/udp`.

## Examples

Any of the commands can be run at creation with `docker run` or later with
`docker exec -it samba.sh` (as of version 1.3 of docker).

### Setting the Timezone

    sudo docker run -it -p 139:139 -p 445:445 -d bomura/samba -t EST5EDT

OR using `environment variables`

    sudo docker run -it -e TZ=EST5EDT -p 139:139 -p 445:445 -d bomura/samba

Will get you the same settings as

    sudo docker run -it --name samba -p 139:139 -p 445:445 -d bomura/samba
    sudo docker exec -it samba samba.sh -t EST5EDT ls -AlF /etc/localtime
    sudo docker restart samba

### Start an instance creating users and shares:

    sudo docker run -it -p 139:139 -p 445:445 -d bomura/samba \
                -u "example1;badpass" \
                -u "example2;badpass" \
                -s "public;/share" \
                -s "users;/srv;no;no;no;example1,example2" \
                -s "example1 private;/example1;no;no;no;example1" \
                -s "example2 private;/example2;no;no;no;example2;writable = yes;vfs objects = recycle"
                -o "wide links = yes;unix extensions = no"	

# User Feedback

## Issues

If you have any problems with or questions about this image, please contact me
through a [GitHub issue](https://github.com/bomura/samba/issues).
