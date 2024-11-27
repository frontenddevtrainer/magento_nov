
# Installing Magento 2.4.6 on Windows 10/11 with XAMPP

## Steps to Install Magento 2.4.6:

### 1. Install XAMPP
Download and install XAMPP from [Apache Friends](https://www.apachefriends.org/download.html).

### 2. Enable PHP Extensions
In the `php.ini` file, enable necessary PHP extensions required by Magento.

### 3. Install Composer
Download and install Composer from [getcomposer.org](https://getcomposer.org/download/).

### 4. Install Elasticsearch
Download Elasticsearch and run it.

### 5. Download Magento
Open a command prompt and run:
```bash
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition ecom
```
Use your Magento Marketplace public and private keys when prompted.

### 6. Configure Virtual Host
Edit `C:\xampp\apache\conf\extra\httpd-vhosts.conf` to add:
```apache
<VirtualHost *:80>
    DocumentRoot "C:/xampp/htdocs/ecom/pub"
    ServerName ecom.local
</VirtualHost>
```
Also, add `127.0.0.1 ecom.local` to your `hosts` file located at `C:\Windows\System32\drivers\etc\hosts`.

### 7. Restart XAMPP
Restart the Apache server in XAMPP to apply changes.

### 8. Create Database
Access phpMyAdmin at `http://localhost/phpmyadmin/` and create a new database for Magento.

### 9. Install Magento
Navigate to the Magento root directory in the command prompt and run:
```bash
php bin/magento setup:install --base-url="http://ecom.local/" --db-host="localhost" --db-name="your_db_name" --db-user="your_db_user" --db-password="your_db_password" --admin-firstname="admin" --admin-lastname="admin" --admin-email="admin@example.com" --admin-user="admin" --admin-password="Admin@12345" --language="en_US" --currency="USD" --timezone="America/Chicago" --use-rewrites="1" --backend-frontname="admin" --search-engine=elasticsearch7 --elasticsearch-host="localhost" --elasticsearch-port=9200
```
Replace placeholders with your actual database credentials and desired admin details.

### 10. Modify Files
- In `vendor\magento\framework\Image\Adapter\Gd2.php`, replace the `validateURLScheme` function with:
```php
private function validateURLScheme(string $filename) : bool
{
    $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
    $url = parse_url($filename);
    if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) {
        return false;
    }
    return true;
}
```
- In `vendor\magento\framework\View\Element\Template\File\Validator.php`, replace `strpos($realPath, $directory)` with `strpos($path, $directory)`.
- In `app/etc/di.xml`, replace `Magento\Framework\App\View\Asset\MaterializationStrategy\Symlink` with `Magento\Framework\App\View\Asset\MaterializationStrategy\Copy`.

### 11. Run Magento Commands
Execute the following commands:
```bash
php bin/magento indexer:reindex
php bin/magento setup:upgrade
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush
php bin/magento module:disable Magento_AdminAdobeImsTwoFactorAuth Magento_TwoFactorAuth
```

These steps should help you set up Magento 2.4.6 on your local Windows environment using XAMPP.
