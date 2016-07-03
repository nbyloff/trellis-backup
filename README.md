# trellis-backup
Role to set up [Stouts.backup](https://github.com/Stouts/Stouts.backup) jobs for [Roots/Trellis](https://roots.io/trellis/) Wordpress sites. Archives the database and uploads folder. to Amazon S3 storage. 

## Amazon S3 Account setup

 * Create an account with [Amazon AWS](http://aws.amazon.com/)
 * Create an S3 bucket to store backups
 * Enable IAM policy for user and new configured backup bucket
 * Get Access ID and Secret Key
 
### Test Amazon S3 Connection

1) Install `s3cmd`
````
sudo apt-get install s3cmd
````

2) Configure `s3cmd` to use API keys (only required first time)
````
s3cmd --configure
````

3) Check for bucket access

This command should return all buckets configured on this account, which should include the one created for this project

````
s3cmd ls
````

## Usage
From your project directory

1) Clone this repository
````
git clone --depth=1 git@github.com:nbyloff/trellis-backup.git trellis/roles/trellis-backup && rm -rf trellis/roles/trellis-backup/.git
````

2) Clone Stouts.backup
````
git clone --depth=1 git@github.com:Stouts/Stouts.backup.git trellis/roles/Stouts.backup && rm -rf trellis/roles/Stouts.backup/.git
````

3) Add `trellis-backup` and `Stouts.backup` to server.yml:
 ````yaml
- name: Set up backups
  hosts: web:&{{ env }}
  roles:
    - { role: backup, tags: [backup] }
    - { role: Stouts.backup, tags: [backup] }
````

4) Add backup configuration to `trellis/group_vars/production/vault.yml` :

````
vault_backup:
  backup_user: "{{ admin_user }}"
  target_user: < YOUR_AWS_ACCESS_KEY_ID >
  target_password: < YOUR_AWS_SECRET_ACCESS_KEY >
````

5) Reprovision your server to add backup tasks:
````{r, engine='bash', count_lines}
ansible-playbook server.yml -e env=production
````
