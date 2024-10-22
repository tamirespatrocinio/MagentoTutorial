# Magento Installation Tutorial

After spending several hours researching and exploring various tutorials, I finally found an effective solution. Now, I am sharing this step-by-step tutorial to help you install Magento in your development environment, considering all the necessary prerequisites. Follow the instructions carefully and enjoy a successful Magento setup.

## Step 1: Environment Setup

- 1.1. Make sure you have Xampp installed on your system. Download the version compatible with PHP 7.3 or higher.
- 1.2. Install Xampp by following the instructions in the installer.
- 1.3. After installing Xampp, open the Xampp control panel and start Apache and MySQL.

## Step 2: Installing Composer

- 2.1. Download and install Composer by following the instructions in the official Composer documentation.

## Step 3: Installing ElasticSearch

- 3.1. Download version 7.0 of Elasticsearch, as Magento does not yet support version 8.
- 3.2. Navigate to the "htdocs" folder inside the Xampp directory on your system.
- 3.3. Extract the Elasticsearch folder inside the "htdocs" folder.
- 3.4. Access the "elasticsearch > bin" folder within the Elasticsearch folder.
- 3.5. Run the "elasticsearch.bat" file to start the Elasticsearch service.
- 3.6. Confirm that the service has started by visiting http://localhost:9200 in your browser. You should see a JSON with some service data.

## Step 4: Creating the Database

- 4.1. Access http://localhost/phpmyadmin/ in your browser.
- 4.2. Create a new database with a name of your choice. For example, you can use "magento_db".

## Step 5: Enabling Extensions

- 5.1. In Xampp, the php.ini file is usually located in the php folder of the installation directory. You can follow the path: C:\xampp\php\php.ini.
- 5.2. Open the php.ini file in a text editor and look for the [Extensions] section in the file to uncomment. If it doesn't exist, you can add it at the end of the file.
```py
extension=intl
...
extension=soap
extension=sockets
extension=sodium
...
extension=xsl
```
- 5.3. Save the changes and restart the Apache server in the Xampp control panel for the changes to take effect.

## Step 6: Installing Magento

- 6.1. Open a terminal or command prompt and navigate to the root folder of Xampp (usually something like "C:\xampp\htdocs" on Windows).
- 6.2. Run the following command to download Magento using Composer:
```py
composer create-project --repository=https://repo.magento.com/ magento/project-community-edition
```
with version
```py
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.5
```
- 6.3. During the installation process, you will be prompted to provide your Magento Marketplace account credentials. Make sure you have an account created and the credentials ready.

## Step 7: Modifying Files

> [!NOTE]
> Always carefully check single and double quotes, as they can be copied incorrectly due to the platform used.

7.1. Open the Magento folder in your editor of choice, navigate to the `vendor/magento/framework/Image/Adapter` folder, and open the `Gd2.php` file.

7.2. Locate the `validateURLScheme` function in the file and replace all the content with the following code:

```py
private function validateURLScheme(string $filename) : bool
{
   $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
   $url = parse_url($filename);
   if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) 
    {
       return false;
     }
   return true; 
}
```    
- 7.3. Save the Gd2.php file.
- 7.4. Navigate to the `vendor/magento/framework/View/Element/Template/File`folder and open the `Validator.php` file.
- 7.5. Modify the line that contains `$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));` to:
```py
$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));
```
- 7.6. Finally, navigate to the Magento folder and go to `vendor\magento\framework\Interception`.
- 7.7. Open the `PluginListGenerator.php` file and look for $cacheId to replace it with:
```py
$cacheId = implode('-', $this->scopePriorityScheme) . '-' . $this->cacheId;
```
## Step 8: Running Magento Commands in CMD

- 8.1. Open CMD in the Magento folder.
- 8.2. This command performs the installation of Magento and configures various options, including the base URL, database, administrator information, language, currency, timezone, and Elasticsearch settings. Make sure the options in the command are correct for your environment:
```py
php bin/magento setup:install --base-url=http://127.0.0.1:8082 --db-host=localhost --db-name=magento2 --db-user=root --db-password="" --admin-firstname=Magento --admin-lastname=User --admin-email=user@example.com --admin-user=admin --admin-password=admin123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=http://localhost --elasticsearch-port=9200
 ```
- 8.3. After executing the command, wait for the installation process to complete.
- 8.4. During the installation, you may see an automatically generated admin panel access name, like "admin_wrr7yw." Note this access name, as it will be needed to access the Magento admin panel later.
- 8.5. After completing the installation, continue by executing the following commands separately:

```py
php bin/magento setup:di:compile
```
* This command compiles the Magento code and generates dependency files. It is important to run it whenever you make changes to the Magento code.
```py
php bin/magento indexer:reindex
```
* This command reindexes Magento's indices. You need to run it whenever there are changes in Magento's data, such as products, categories, or attributes.
```py
php bin/magento setup:upgrade
```
* This command updates the Magento database schema to reflect any changes made to the modules. It is important to run it whenever you update or install new modules.
```py
php bin/magento setup:static-content:deploy -f en_US en_GB
```
* This command deploys Magento's static files for the languages en_US (U.S. English) and en_GB (British English). Make sure to replace these languages with those you are using in your project.
```py
php bin/magento deploy:mode:set developer
```
* This command sets Magento's deployment mode to "developer." In developer mode, Magento displays detailed error messages and automatically regenerates static files. It is recommended for development environments.
```py
php bin/magento cache:clean
```
* This command clears Magento's cache, removing cached files and cached data. It is useful to run it after making changes to the code or Magento configuration.
```py
php bin/magento cache:flush
```
* This command completely flushes Magento's cache, removing all cached files and data. Use with caution, as this can take some time to regenerate the cache when Magento is accessed again.
```py
php bin/magento module:disable Magento_Csp
```
* This command disables the Magento_Csp module. You can replace "Magento_Csp" with the name of the module you want to disable. Remember that disabling a module can affect Magento's functionality, so do this only if necessary.
```py
php bin/magento module:disable Magento_TwoFactorAuth
```
* This command disables the Magento_TwoFactorAuth module, which is responsible for two-factor authentication in Magento. If you do not need this additional security feature, you can disable it using this command.

## Step 9: Finalization

To proceed, return to phpMyAdmin and follow these steps:

- 9.1. Open phpMyAdmin and select the database.
- 9.2. Go to the "SQL" section at the top of the page.
- 9.3. In the provided text area, paste the following query:
```py
INSERT INTO `core_config_data`(`path`, `value`) VALUES ('dev/static/sign', 0) ON DUPLICATE KEY UPDATE `value`=0
```
- 9.4. To execute the query and apply the changes, click the "Execute" button in phpMyAdmin.
- 9.5. Then, run the following command to clean the cache exclusively for the configurations:
```py
php bin/magento cache:clean config
```
Isso garantirá que as alterações de configuração sejam atualizadas corretamente.

Por fim, para iniciar a aplicação Magento localmente, use o seguinte comando:
```py
php -S 127.0.0.1:8082 -t ./pub/ ./phpserver/router.php
```
After completing the command, you should see the Magento dashboard. 

![magento](https://github.com/tamirespatrocinio/MagentoTutorial/assets/73259410/a89e62ae-3968-467f-b5e6-a41c6a525d7e)

To access the admin panel, simply open the URL in your browser:
`http://127.0.0.1:8082/admin_wrr7yw/`.
Make sure to replace "admin_wrr7yw" with the correct URL for your admin panel.


> [!CAUTION]
If you encounter a bug similar to the one shown in the image, follow the commands below to resolve it and run the application again:

![error](https://github.com/tamirespatrocinio/MagentoTutorial/assets/73259410/8a3be36f-127d-46d1-97ab-3ed909519c2d)
```py
php bin/magento setup:upgrade
```
```py
php bin/magento setup:static-content:deploy -f 
```
* This command is used to deploy Magento's static files. The -f option indicates that you want to force the deployment, replacing existing static files if there are any. By executing this command, Magento will compile and copy the relevant static files for the configured languages in your project. This includes CSS, JavaScript, images, and other static resources necessary for the proper functioning of the online store.

