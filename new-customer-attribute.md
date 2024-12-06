
# Creating a Custom Extension in Magento 2 

This guide walks through the creation of a new Magento 2 extension.

---

## Part 1: Creating a New Magento 2 Extension

### Step 1: Define the Module Structure
Create the directory structure for the new module:
```
app/code/ABCInc/CustomerAttributes
```

### Step 2: Create the Required Module Files
1. **`registration.php`**
   - Path: `app/code/ABCInc/CustomerAttributes/registration.php`
   ```php
   <?php

   use Magento\Framework\Component\ComponentRegistrar;

   ComponentRegistrar::register(
       ComponentRegistrar::MODULE,
       'ABCInc_CustomerAttributes',
       __DIR__
   );
   ```

2. **`module.xml`**
   - Path: `app/code/ABCInc/CustomerAttributes/etc/module.xml`
   ```xml
   <?xml version="1.0"?>
   <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
       <module name="ABCInc_CustomerAttributes" setup_version="1.0.0">
           <sequence>
               <module name="Magento_Customer"/>
           </sequence>
       </module>
   </config>
   ```

3. **`composer.json`**
   - Path: `app/code/ABCInc/CustomerAttributes/composer.json`
   ```json
   {
       "name": "abcinc/module-customer-attributes",
       "description": "A Magento 2 module to add custom customer attributes.",
       "type": "magento2-module",
       "version": "1.0.0",
       "license": "OSL-3.0",
       "autoload": {
           "files": [
               "registration.php"
           ],
           "psr-4": {
               "ABCInc\CustomerAttributes\": ""
           }
       }
   }
   ```

4. **Data Patch for Adding Attributes**
   - Path: `app/code/ABCInc/CustomerAttributes/Setup/Patch/Data/AddCustomerAttributes.php`
   ```php
   <?php

   namespace ABCInc\CustomerAttributes\Setup\Patch\Data;

   use Magento\Framework\Setup\Patch\DataPatchInterface;
   use Magento\Customer\Setup\CustomerSetupFactory;
   use Magento\Eav\Model\Config;

   class AddCustomerAttributes implements DataPatchInterface
   {
       private $customerSetupFactory;
       private $eavConfig;

       public function __construct(
           CustomerSetupFactory $customerSetupFactory,
           Config $eavConfig
       ) {
           $this->customerSetupFactory = $customerSetupFactory;
           $this->eavConfig = $eavConfig;
       }

       public function apply()
       {
           $customerSetup = $this->customerSetupFactory->create();

           // Add "Hobby" Attribute
           $customerSetup->addAttribute(
               \Magento\Customer\Model\Customer::ENTITY,
               'hobby',
               [
                   'type' => 'varchar',
                   'label' => 'Hobby',
                   'input' => 'text',
                   'required' => false,
                   'visible' => true,
                   'system' => false,
                   'user_defined' => true,
                   'position' => 100,
               ]
           );
           $hobby = $this->eavConfig->getAttribute(\Magento\Customer\Model\Customer::ENTITY, 'hobby');
           $hobby->setData('used_in_forms', ['adminhtml_customer', 'customer_account_create', 'customer_account_edit']);
           $hobby->save();

           // Add "Anniversary" Attribute
           $customerSetup->addAttribute(
               \Magento\Customer\Model\Customer::ENTITY,
               'anniversary',
               [
                   'type' => 'datetime',
                   'label' => 'Anniversary',
                   'input' => 'date',
                   'required' => false,
                   'visible' => true,
                   'system' => false,
                   'user_defined' => true,
                   'position' => 110,
               ]
           );
           $anniversary = $this->eavConfig->getAttribute(\Magento\Customer\Model\Customer::ENTITY, 'anniversary');
           $anniversary->setData('used_in_forms', ['adminhtml_customer', 'customer_account_create', 'customer_account_edit']);
           $anniversary->save();
       }

       public static function getDependencies()
       {
           return [];
       }

       public function getAliases()
       {
           return [];
       }
   }
   ```

---

### Step 3: Enable and Test the Module
1. Enable the module:
   ```bash
   php bin/magento module:enable ABCInc_CustomerAttributes
   php bin/magento setup:upgrade
   php bin/magento setup:di:compile
   php bin/magento cache:flush
   ```

2. Verify:
   - Go to the **Admin Panel** > **Customers** > Edit a customer.
   - Check that "Hobby" and "Anniversary" attributes are added.

---

## Part 2: Adding Frontend Templates

### Step 1: Create the Template File
1. Create the directory:
   ```
   app/code/ABCInc/CustomerAttributes/view/frontend/templates/customer
   ```

2. Add the `attributes.phtml` file:
   - **Path**: `app/code/ABCInc/CustomerAttributes/view/frontend/templates/customer/attributes.phtml`
   ```phtml

   <div class="field">
    <label for="hobby" class="label"><span>Hobby</span></label>
    <div class="control">
        <input type="text" id="hobby" name="hobby" value="<?= $block->escapeHtml($block->getCustomer()->getHobby()) ?>" />
    </div>
    </div>

    <div class="field">
        <label for="anniversary" class="label"><span>Anniversary</span></label>
        <div class="control">
            <input type="date" id="anniversary" name="anniversary" value="<?= $block->escapeHtml($block->getCustomer()->getAnniversary()) ?>" />
        </div>
    </div>
   ```
---

## Part 3: Fixing the `Failed to Open Stream` Error

### Issue
While running `bin/magento setup:di:compile`, you may encounter:
```text
Warning: file_put_contents(/magento2/generated/metadata/primary|global|plugin-list.php): 
Failed to open stream: No such file or directory in \magento2endor\magentoramework\Interception\PluginListGenerator.php on line 414
```

### Cause
The issue arises because Magento uses the `|` character in file paths, which is not valid on some operating systems (e.g., Windows).

---

### Solution
1. Locate the file `PluginListGenerator.php`:
   ```
   vendor/magento/framework/Interception/PluginListGenerator.php
   ```

2. Replace this line:
   ```php
   $cacheId = implode('|', $this->scopePriorityScheme) . "|" . $this->cacheId;
   ```

   with:

   ```php
   $cacheId = implode('-', $this->scopePriorityScheme) . "-" . $this->cacheId;
   ```

3. Save the `PluginListGenerator.php` file.

---

### Step 4: Clear Cache and Compile
Run the following commands to clear the cache and recompile Magento:
```bash
php bin/magento setup:upgrade
php bin/magento setup:di:compile
php bin/magento cache:flush
```

---

## Additional Information
If you encounter a similar error while creating directories or writing files, ensure the `generated` directory exists and is writable:
1. Navigate to the Magento root directory.
2. Verify that the `generated/metadata` and `generated/code` directories exist.
3. If missing, create them manually:
   ```
   mkdir -p generated/metadata
   mkdir -p generated/code
   ```
4. Set the correct permissions:
   ```bash
   chmod -R 777 generated/
   ```

---

## Explanation
- The `|` character is invalid for file paths on some operating systems, such as Windows.
- Replacing it with `-` ensures compatibility across all environments.
