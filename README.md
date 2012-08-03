# Akrabat ZF library

See [Akrabat_Db_Schema_Manager: Zend Framework database migrations](http://akrabat.com/zend-framework/akrabat_db_schema_manager-zend-framework-database-migrations/) for full details

## ZF1:

To get the Zend_Tool provider working:

1. Create a composer.json with the following

        {
            "require": {
                "akrabat/zf1-db-migrate": "dev-master",
                "breerly/zf1": "1.11.11"
            },
            "config": {
                "bin-dir": "bin/"
            }
        }

2. You need to have composer installed http://getcomposer.org, and run

        php composer.phar install

3. Update your `~/.bash_profile` to set up an alias to the zf.php script

        alias zf='export ZF_CONFIG_FILE=/path/to/my/project/bin/.zf.ini; /path/to/my/project/bin/zf.php'
    
   Restart your terminal or `source ~/.bash_profile`
4. If you haven't already done so, setup the storage directory and config file:
    
        zf --setup storage-directory
        zf --setup config-file
        
5. Edit the created `.zf.ini`. Change path so that it includes ZF1 and Akrabat/zf1, allow for auotoloading Akrabat and set up the provider:
    
        php.include_path = "/path/to/zf1/library:/path/to/Akrabat/zf1/"
        autoloadernamespaces.0 = "Akrabat_"
        basicloader.classes.0 = "Akrabat_Tool_DatabaseSchemaProvider"

6. `zf` should provide a help screen with the `DatabaseSchema` provider at the bottom.

### Akrabat_Db_Schema_Manager

1. Create scripts/migrations folder in your ZF application
2. Create migration files within migrations with the file name format of nnn-Xxxx.php. e.g. 001-Users.php
    where:  
       nnn => any number. The lower numbered files are executed first  
       Xxx => any name. This is the class name within the file.

3. Create a class in your migrations file. Example for 001-Users.php:
    
        class Users extends Akrabat_Db_Schema_AbstractChange 
        {
            function up()
            {
                $tableName = $this->_tablePrefix . 'users';
                $sql = "
                    CREATE TABLE IF NOT EXISTS $tableName (
                      id int(11) NOT NULL AUTO_INCREMENT,
                      username varchar(50) NOT NULL,
                      password varchar(75) NOT NULL,
                      roles varchar(200) NOT NULL DEFAULT 'user',
                      PRIMARY KEY (id)
                    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;";
                $this->_db->query($sql);
        
                $data = array();
                $data['username'] = 'admin';
                $data['password'] = sha1('password');
                $data['roles'] = 'user,admin';
                $this->_db->insert($tableName, $data);
            }
            
            function down()
            {
                $tableName = $this->_tablePrefix . 'users';
                $sql= "DROP TABLE IF EXISTS $tableName";
                $this->_db->query($sql);
            }
        
        }
        
4. If you want a table prefix, add this to your `application.ini`:

        resources.db.table_prefix = "prefix"

### Akrabat\Db\Schema\Manager

1. Create scripts/migrations folder in your ZF application
2. Create migration files within migrations with the file name format of nnn-Xxxx.php. e.g. 001-Users.php
    where:  
       nnn => any number. The lower numbered files are executed first  
       Xxx => any name. This is the class name within the file.

3. Create a class in your migrations file. Example for 001-Users.php:
    
        class Users extends \Akrabat\Db\Schema\AbstractChange 
        {
            function up()
            {
                $tableName = $this->_tablePrefix . 'users';
                $sql = "
                    CREATE TABLE IF NOT EXISTS $tableName (
                      id int(11) NOT NULL AUTO_INCREMENT,
                      username varchar(50) NOT NULL,
                      password varchar(75) NOT NULL,
                      roles varchar(200) NOT NULL DEFAULT 'user',
                      PRIMARY KEY (id)
                    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;";
                $this->_db->query($sql);
        
                $data = array();
                $data['username'] = 'admin';
                $data['password'] = sha1('password');
                $data['roles'] = 'user,admin';
                $this->_db->insert($tableName, $data);
            }
            
            function down()
            {
                $tableName = $this->_tablePrefix . 'users';
                $sql= "DROP TABLE IF EXISTS $tableName";
                $this->_db->query($sql);
            }
        
        }
        
4. If you want a table prefix, add this to your `application.ini`:

        resources.db.table_prefix = "prefix"
        
        
## Phing Task

1. Define the phing task in the `build.xml`

        <taskdef name="dbmigration" classname="phing.tasks.PhingAkrabatDbSchemaManager" />
        
2. Setup a phing target with the database options in the `build.xml`
 
        <target name="database-migration">
        	<dbmigration adapter="mysqli" host="${db.host}" dbname="${db.name}" username="${db.user}" password="${db.pass}" />
		</target>

3. Run phing using the command line, by `phing database-migration`
