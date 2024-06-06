
## Deployment

To deploy the project follow the following steps:

### Install the Requirements
unrar:
```bash
  sudo apt-get install unrar
```
pm2
```bash
  npm install pm2 -g
```

### Setting up the database
Login into the MySQL shell environment:
```bash
  mysql -u root -p
```
Create a new user:
```mysql
  CREATE USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

Create a new database:
```mysql
  CREATE DATABASE 'database_name';
```

Grant a User Permissions:
```mysql
  GRANT ALL PRIVILEGES ON 'database_name'.* TO 'username'@'localhost' WITH GRANT OPTION;
```

Exit the MySQL shell:
```mysql
  exit;
```

Restore a backup
```bash
mysql -u username -p database_name < /path/to/backup_file.sql
```

### Setup the application

Create the project root folder in the www:
```bash
  cd /var/www
  mkdir titanmates.com
```

Upload the source code and unrar it:
```bash
  unrar x 'compressed_file_name.rar'
  mv ./compressed_file_name ./app
```
Move to the project root directory:
```bash
  cd ./app
```

Install the application dependencies:
```bash
  pnpm install
```
Change the mysql database configs
```bash
  cd ./packages/prisma
  nano ./.env
```
Inside the file change the username, password and database_name:
```bash
  DATABASE_URL=mysql://username:password@0.0.0.0:3306/database
```
Setup the prisma ORM:
```bash
  npx prisma db pull
  mkdir ./prisma/migrations/0_init
  npx prisma migrate diff --from-empty --to-schema-datamodel ./prisma/schema.prisma --script > ./prisma/migrations/0_init/migration.sql
  npx prisma migrate resolve --applied 0_init
  npm install @prisma/client
  npx prisma generate
```

### Run the application
Run the application with the pm2 process manager:

api:
```bash
  cd /var/www/titanmates.com/app/apps/api
  pm2 start ./index.js --name titan-api
```

auth:
```bash
  cd /var/www/titanmates.com/app/apps/auth
  pm2 start ./index.js --name titan-auth
```

live:
```bash
  cd /var/www/titanmates.com/app/apps/live
  pm2 start ./index.js --name titan-live
```

lms:
```bash
  cd /var/www/titanmates.com/app/apps/lms
  pm2 start ./index.js --name titan-lms
```

## Documentation and resources

- [How To Create a New User and Grant Permissions in MySQL | Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql)

- [MySQL Backup & Restore | Medium](https://medium.com/@adnanitdev/mysql-backup-and-restore-ee146c31c696)

- [Introspection | Prisma Documentation](https://www.prisma.io/docs/getting-started/setup-prisma/add-to-existing-project/relational-databases/introspection-typescript-mysql)

- [Baseline the Database | Prisma Documentation](https://www.prisma.io/docs/getting-started/setup-prisma/add-to-existing-project/relational-databases/baseline-your-database-typescript-mysql)

- [Install Prisma Client | Prisma Documentation](https://www.prisma.io/docs/getting-started/setup-prisma/add-to-existing-project/relational-databases/install-prisma-client-typescript-mysql)

- [Start & Manage an app | PM2 Documentation](https://pm2.keymetrics.io/docs/usage/quick-start/)