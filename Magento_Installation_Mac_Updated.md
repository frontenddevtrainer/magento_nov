
# Installing Magento on Mac

This guide provides step-by-step instructions for installing Magento on a Mac.

## Prerequisites
1. **Homebrew**: Ensure Homebrew is installed. Run:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
2. **PHP**: Install PHP via Homebrew:
   ```bash
   brew install php
   ```
3. **Composer**: Install Composer globally:
   ```bash
   brew install composer
   ```
4. **MySQL**: Install MySQL:
   ```bash
   brew install mysql
   ```
   Start MySQL:
   ```bash
   brew services start mysql
   ```

## Steps to Install Magento
1. **Install Elasticsearch**:
   ```bash
   brew tap elastic/tap
   brew install elasticsearch
   ```
   Start Elasticsearch:
   ```bash
   brew services start elasticsearch
   ```

2. **Set Up a Local Web Server**:
   Install Apache:
   ```bash
   brew install httpd
   ```
   Start Apache:
   ```bash
   brew services start httpd
   ```

3. **Download Magento**:
   Navigate to the directory where you want to install Magento and run:
   ```bash
   composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition magento2
   ```

4. **Set Up Magento**:
   Navigate to the Magento directory:
   ```bash
   cd magento2
   ```
   Run the Magento installation command:
   ```bash
   php bin/magento setup:install    --base-url=http://localhost/magento2    --db-host=127.0.0.1    --db-name=magento    --db-user=root    --db-password=your_password    --admin-firstname=admin    --admin-lastname=admin    --admin-email=admin@example.com    --admin-user=admin    --admin-password=Admin123    --language=en_US    --currency=USD    --timezone=America/Los_Angeles    --use-rewrites=1
   ```

5. **Set Permissions**:
   ```bash
   sudo chmod -R 777 var/ generated/ pub/static/ pub/media/
   ```

6. **Access Magento**:
   - Frontend: `http://localhost/magento2`
   - Admin Panel: `http://localhost/magento2/admin`

## Troubleshooting
- Check Apache and MySQL services are running.
- Check PHP and MySQL configurations for compatibility with Magento.

## Additional Resources
- [Magento Official Documentation](https://magento.com/)
- [Homebrew](https://brew.sh/)
