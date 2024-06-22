
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
  unrar x 'build.rar'
  mv ./build/app ./app
  mv ./build/auth ./auth
```
Move to the project root directory:
```bash
  cd ./app
```

Install the application dependencies:
```bash
  pnpm install
```
create mysql database configs
```bash
  cd ./packages/prisma
  touch ./.env
  nano ./.env
```
Inside the file, copy the following line and change the username, password, and database_name:
```bash
  DATABASE_URL=mysql://username:password@0.0.0.0:3306/database_name
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

### Setup the environment variables
in the root of the project create a '.env' file:
```bash
  cd /var/www/titanmates.com/app
  touch ./.env
  nano ./env
```
add the following variables:

| Name                              | Value                                                                                       | Description                                                           |
|:----------------------------------|:--------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|
| `NODE_ENV`                        | `production`                                                                                | -                                                                     |
| `HOST_URL`                        | `127.0.0.1`                                                                                 | -                                                                     |
| `ORIGINS`                         | `'https://api.titanmates.com https://auth.titanmates.com https://dashboard.titanmates.com'` | -                                                                     |
| `LOG_FORMAT`                      | `dev`                                                                                       | -                                                                     |
| `DATABASE_URL`                    | `mysql://username:password@0.0.0.0:3306/database_name`                                      | -                                                                     |
| `REFRESH_TOKEN_SECRET`            | `your_secret`                                                                               | Generate a random base64 string and replace it with the 'your_secret' |
| `REFRESH_TOKEN_EXPIRATION_PERIOD` | `2592000`                                                                                   | Equals to 3 days                                                      |
| `REFRESH_TOKEN_SECRET`            | `your_secret`                                                                               | Generate a random base64 string and replace it with the 'your_secret' |
| `REFRESH_TOKEN_EXPIRATION_PERIOD` | `3600`                                                                                      | Equals to 1 hour                                                      |
| `SUBDOMAIN_AUTH`                  | `auth.titanmates.com`                                                                       | -                                                                     |
| `SUBDOMAIN_DASHBOARD`             | `dashboard.titanmates.com`                                                                  | -                                                                     |

now you need to setup the .env file for each app

#### API
```bash
  cd /var/www/titanmates.com/app/apps/api
  cp /var/www/titanmates.com/app/.env ./.env
  nano ./.env
```
inside the copied file add the following variable:
| Name | Value | Description  |
| :-------- | :------- | :-------------------------------- |
| `HOST_PORT` | `your_port` | The port that the 'API' application will run and listen to it (for example 8000). Make sure the chosen port is not taken by another process. |

#### Auth
```bash
  cd /var/www/titanmates.com/app/apps/auth
  cp /var/www/titanmates.com/app/.env ./.env
  nano ./.env
```
inside the copied file add the following variable:
| Name | Value | Description  |
| :-------- | :------- | :-------------------------------- |
| `HOST_PORT` | `your_port` | The port that the 'Auth' application will run and listen to it (for example 8005). Make sure the chosen port is not taken by another process. |

#### Live
```bash
  cd /var/www/titanmates.com/app/apps/live
  cp /var/www/titanmates.com/app/.env ./.env
  nano ./.env
```
inside the copied file add the following variable:
| Name | Value | Description  |
| :-------- | :------- | :-------------------------------- |
| `HOST_PORT` | `your_port` | The port that the 'Live' application will run and listen to it (for example 8010). Make sure the chosen port is not taken by another process. |

#### LMS
```bash
  cd /var/www/titanmates.com/app/apps/lms
  cp /var/www/titanmates.com/app/.env ./.env
  nano ./.env
```
inside the copied file add the following variable:
| Name | Value | Description  |
| :-------- | :------- | :-------------------------------- |
| `HOST_PORT` | `your_port` | The port that the 'LMS' application will run and listen to it (for example 8015). Make sure the chosen port is not taken by another process. |

#### Redis
For an unresolved bug, you need to set the Redis URL manually
```bash
  nano /var/www/titanmates.com/app/packages/redis/index.js
```
Inside the file, find and replace the 'redis_url' string with your Redis URL (It's on line 17). The URL should follow the following format:

`redis://:password@127.0.0.1:6379/0`

remove the created .env file in the root directory
```bash
  rm -rf /var/www/titanmates.com/app/.env
```

### Run the application
Run the application with the pm2 process manager:

#### API
```bash
  cd /var/www/titanmates.com/app/apps/api
  pm2 start ./index.js --name titan-api
```

#### Auth
```bash
  cd /var/www/titanmates.com/app/apps/auth
  pm2 start ./index.js --name titan-auth
```

#### Live
```bash
  cd /var/www/titanmates.com/app/apps/live
  pm2 start ./index.js --name titan-live
```

#### LMS
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
