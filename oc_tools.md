'oC_Utilities are several "shortcuts" that helps me to administrate my oC
 

### List:

- oc_go-data
- oc_go-root
- oc_go-apache
- oc_email-list
- oc_last-seen
- oc_log-access
- oc_log-error
- oc_log-show
- oc_log-trim
- oc_occ
- oc_scan
- oc_use
- oc_use-files
- oc_use-cache
- oc_use-version
- oc_use-trashbin
- oc_use-thumbnails
- oc_use-external

## Codes:

### oc_go-data, oc_go-home, oc_go-apache

in .bash rc:
```
#alias oc_go
alias oc_go-data="cd /mnt/owncloud/data"
alias oc_go-home="cd /var/www/html/owncloud"
alias oc_go-apache="cd /var/log/apache2"
```

### oc_email-list
```
#!/bin/bash
FILE="/mnt/owncloud/data/carlos/files/Documentos/email_list.txt"
mysql -u root --password=XXXX -e "SELECT configvalue FROM ocdamken.oc_preferences WHERE configkey = 'email'" > "$FILE"
sed -i '/configvalue/d' -i ':a;N;$!ba;s/\n/; /g' "$FILE"
chown www-data:www-data "$FILE"
```

### oc_last-seen
```
#!/bin/bash
USER=$(mysql -u root --password=XXXX -e "select uid from ocdamken.oc_users")
[[ $# == 1 ]] && USER=$1
  OLDIFS=$IFS
  IFS=$'\n'
    for CHUSER in $USER ; do
      if [ "$CHUSER" != "uid" ] ; then
        printf "Testing %s  --> " "$CHUSER"
        SEEN=$(sudo -u www-data php /var/www/html/owncloud/occ user:lastseen "$CHUSER")
        if [[ "$SEEN" == *"never"* ]] ; then
          tput setaf 1
        fi
        printf " %s \n" "$SEEN"
        tput sgr0
      fi
    done
  IFS=$OLDIFS
```

### oc_log-access
```
#!/bin/bash
tail -f -n 1000 /var/log/apache2/access.log
```

### oc_log-error
```
#!/bin/bash
grep -Ev '(level":0|level":1|level":2)' /mnt/owncloud/data/owncloud.log |more
```

### oc_log-show
```
#!/bin/bash
tail -f -n 1000 /mnt/owncloud/data/owncloud.log
```

### oc_log-trim
```
#!/bin/bash
VERSION="/mnt/owncloud/data/owncloud.log"
[[ $# == 1 ]] && VERSION=$VERSION"."$1
sed -i '/level":0/d' "$VERSION"
sed -i '/level":1/d' "$VERSION"
sed -i '/level":2/d' "$VERSION"
echo "Trimmed $VERSION"
```
### oc_occ

```
#! /bin/bash
sudo -u www-data php /var/www/html/owncloud/occ $#
```
### oc_scan
```
#!/bin/bash
USER="--all"
[[ $# == 1 ]] && USER=$1
sudo -u www-data php /var/www/html/owncloud/occ files:scan $USER
```

### oc_use
```
#!/bin/bash
echo Use in the storage:
echo
oc_use-files
echo
oc_use-cache
echo
oc_use-version
echo
oc_use-trashbin
echo
oc_use-thumbnails
echo
oc_use-external

```

### oc_use-files
```
#!/bin/bash
echo files:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/files' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}' | numfmt --to=iec
```

### oc_use-cache
```
#!/bin/bash
echo cache:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/cache' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}'| numfmt --to=iec
```

### oc_use-version
```
#!/bin/bash
echo files_versions:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/files_versions' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}' | numfmt --to=iec
```

### oc_use-trashbin
```
#!/bin/bash
echo files_trashbin:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/files_trashbin' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}' | numfmt --to=iec
```

### oc_use-thumbnails
```
#!/bin/bash
echo thumbnails:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/thumbnails' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}' | numfmt --to=iec
```

### oc_use-external
```
#!/bin/bash
echo files_external:
find /mnt/owncloud/data -maxdepth 2 -wholename '*/data/*/files_external' -print0 |xargs -r0 du --max-depth=0 -a| awk '{sum+=$1} END {print sum}' | numfmt --to=iec
```

