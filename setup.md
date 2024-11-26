## **Prerequisites**
Ensure the following tools are installed on your Windows system:

1. **Composer**: Download and install [Composer](https://getcomposer.org/).
2. **PHP**: Install PHP (version 8.1 or higher for Magento 2.4.6+).
3. **Elasticsearch**: Install Elasticsearch (version 7.9.x - 7.17.x).
4. **Git**: Download and install [Git](https://git-scm.com/).
5. **Web Server**: Use XAMPP, WAMP, or an equivalent local server.

---

## **Step 1: Install Magento Prerequisites**

### **1.1 Install PHP**

1. Verify PHP installation:
   ```bash
   php -v
   ```

2. Configure `php.ini`:
   - Open `php.ini` in the PHP installation folder.
   - Enable the required extensions for Magento by uncommenting or adding:
     ```ini
     extension=gd
     extension=intl
     extension=mbstring
     extension=bcmath
     extension=xml
     extension=curl
     extension=zip
     extension=soap
     extension=pdo_mysql
     extension=openssl
     extension=soap
     ```

### **1.2 Install Composer**
1. Download and install [Composer](https://getcomposer.org/).
2. Verify installation:
   ```bash
   composer -v
   ```

### **1.3 Install Elasticsearch**
1. Download Elasticsearch (version 7.9.x - 7.17.x) from the [Elasticsearch Archives](https://www.elastic.co/downloads/past-releases).
2. Extract the files to a directory (e.g., `C:\elasticsearch`).
3. Start Elasticsearch:
   ```bash
   C:\elasticsearch\bin\elasticsearch.bat
   ```

---

## **Step 2: Magento Installation**

### **2.1 Download Magento**
1. Open a terminal and navigate to your web server’s root directory (e.g., `C:\xampp\htdocs`).
2. Use Composer to download Magento:
   ```bash
   composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition magento
   ```
   - Enter your Magento authentication keys when prompted. These can be obtained from the [Magento Marketplace](https://marketplace.magento.com/).

   - Add keys to composer 
   ```bash 
   composer config --global http-basic.repo.magento.com <public_key> <private_key>
   ```

### **2.2 Install Magento**
1. Navigate to the Magento directory:
   ```bash
   cd magento
   ```
2. Run the installation command:
   ```bash
   php bin/magento setup:install \
       --base-url=http://localhost/magento \
       --db-host=127.0.0.1 \
       --db-name=magento \
       --db-user=root \
       --db-password=yourpassword \
       --admin-firstname=admin \
       --admin-lastname=user \
       --admin-email=admin@example.com \
       --admin-user=admin \
       --admin-password=Admin123! \
       --search-engine=elasticsearch7 \
       --elasticsearch-host=127.0.0.1 \
       --elasticsearch-port=9200 \
       --backend-frontname=admin
   ```

---

## **Step 3: Troubleshooting Common Issues**

### **3.1 PHP Update**
- Follow the steps in **Step 1.1** to update PHP to a compatible version.

### **3.2 Elasticsearch Issues**
- Ensure Elasticsearch is running by testing it with:
  ```bash
  curl -X GET "http://127.0.0.1:9200"
  ```
- If Magento cannot connect to Elasticsearch, ensure the host and port are correct.

## **Step 4: Final Steps**
1. Set Magento file permissions:
   ```bash
   chmod -R 775 var/ pub/ generated/
   ```
2. Deploy static content:
   ```bash
   php bin/magento setup:static-content:deploy
   ```
3. Test your Magento installation by accessing `http://localhost/magento`.

---

## **Resources**
- Official Magento Documentation: [Adobe Commerce DevDocs](https://developer.adobe.com/commerce/)
- Magento Forums: [Magento Community](https://community.magento.com/)
- Elasticsearch Documentation: [Elasticsearch Guide](https://www.elastic.co/guide/)
