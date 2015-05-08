> ## In Magento:
> * **Code pools**:
  * **core**
    * Holds modules that are distributed with the base Magento and make up the core functionality
  * **community**
    * Holds modules that are developed by third-parties and installed by the users
  * **local**
    * Holds custom modules developed by your company, including Mage code overrides.
  * The priority to load the files from these pools is:
    * Local
    * Comunity
    * Core
* **Module Structure:**
  ```
    Namespace/
      └── Modulename
          ├── Block
          ├── Helper
          ├── Model
          ├── controllers
          ├── etc
          └── sql
  ```
  * **Bloks**
    * Cordinate models with the template files. The files in the folder load the data from the database
      and transfer it to the templates in your theme(.phtml files).
  * **Controllers**
    * Represent all business logic actions for the given request( dispatch(), preDispatch(), postDispatch() methods) and delegate commands to other parts of the system.
  * **etc**
    * It contains all xml files that declare and configure behavior of all modules. Each module must have at least config.xml and it's a right place to declare all models, routes, blocks, helpers etc.
    * Optionally this folder could have adminhtml.xml( grant permisions for your tabs/sections in backend menus) and system.xml( creates this new section or specifies where it should be diplayed in existinh one).
  * **Helper**
    * Contain utility methods, which are commonly used in the whole system. Methods, declared in helpers, can be called from any template file or block, model, controller, class by:
      ```
        Mage::Helper('modulename/helpername')->methodName();
      ```
  * **Model**
    * In clasical MVC, Models are used to connect to the database and process the data from and to it. Magento has a different approach, which can be triky at first look.
      Magento models can be categorized in one of two ways.
        * ActiveRecord-like/one-object-one-table Model
        * Entity Attribute Value(EAV) Model.
    ***
      Each of Model also gets a **Model Collection**, Collections are PHP objects used to hold a number of invidual magento Model
      Instances. The magento team has implemented the PHP Standard Lyrbrary Interfaces of Magento Instances of IteratorAggregate and Countable
      to allow each Model type to have it's own collection type
      Each model uses two ModelResource classes:
      ```
          * one to read
          * one to write
      ```
    ***


* **Magento Templates and layout files location**
  * A theme consists of the following elements
    * **Layout:** Folder includes XML files that define the layout of the theme. Layout files work as a "glue" between the modules
    (wich are found under the app/code directory), and the template files

    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── <b>layout</b>
      │   │   │       └── template
    ```
  Because of magent modularity, all XML data of the default theme are stored in separate files with the name of the module to which they belong to.
A non-default theme, contrariwise, should have a sigle layout file, named local.xml, where all layout updates are placed.

* **Template**
  * This folder cointains .phtml files that have HTML and PHP code for reach Magento Blocks which will be displayed in the frontend
    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── <b>template</b>
    ```
* **Locale**
  * This folder contains all .CSV files organized by language that provide translations in format of languagecode_COUNTRYCODE eg(es_MX, en_US)/translate.csv
    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── template
      │   │   │       └── <b>locale</b>
      │   │   │           └── es_MX
      │   │   │               └── translate.csv
    ```

* **Skin and javascript files location**
  * Skin folder includes javascript, CSS and image files tha  are used in .phtml files in template folder. e.g All the files in this folder vary from theme to theme.
    ```
      skin/
      ├── frontend
      │   ├── package_name
      │   │   └── theme_name
      │   │   |   ├── <b>js</b>
      │   │   |   ├── <b>css</b>
      │   │   |   ├── <b>images</b>
    ```
  * **JS** folder embodies **js** files, libraries and frameworks that are used via frontend and backend, I f you need to add a new Javascript/AJAX library on your module from local code pool
    requires special scripts, they should be placed here.


## Design Areas:
```
  All the frontend files are stored on these areas:
```

* **adminhtml**
  * Everithing that you will observe while working in the admin panel cabe be found here. In other words
  , If a module performs anything related to admin panel, all templates and layouts will be here.
* **frontend**
  * Everything that can be observed on your webstore by any customer is stored here.
* **install**
  * Files from this area will be displayed during the installation process.
* **sql**
  * Handles any custom database tables wich will be used by the module and process all upgrades to the extension.


* **Class naming conventions and their relationship with the autoloader**
  >Magento was developed based on the Zend Framework, so the rules of class naming in Magento were taken from the Zend Framework

  Magento standardizes class names depending on their location in the file system. Such standadization enables atomatic class loading(**autoloader**)
  instead of using require_once and include_once functions. Rather than the directory separator('/' - invalid character for class names), developers use the underscor
  character('_')

  For example, the Mage_Catalog_Model_Product class is located in the /app/code/core/Mage/Catalog/Model/Product.php category. Magento Autoloader replaces all underscore characters
('_') with the category separator('/') and looks for the file in one of the categories(/app/code/local/ can be disables in the app/etc/local.xml in the disable_local_modules tag).

  - /app/code/core
  - /app/code/community
  - /app/code/local
  - /lib/
> File search categories in Magento are defined in app/Mage.php
```
  Mage::register('original_include_path', get_include_path());

  if (defined('COMPILER_INCLUDE_PATH')) {
      $appPath = COMPILER_INCLUDE_PATH;
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage_Core_functions.php";
      include_once "Varien_Autoload.php";
  } else {
      /**
       * Set include path
       */
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'local';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'community';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'core';
      $paths[] = BP . DS . 'lib';

      $appPath = implode(PS, $paths);
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage/Core/functions.php";
      include_once "Varien/Autoload.php";
  }

  Varien_Autoload::register();
  ```
  After defining all include paths you see, at the very last line of above snippet, that
  Magento's autoloader ins initialized. This line triggers the underneath snippet, which registers
  the autoload() method of the Varient_Autoloader object as it's autoloader weapon of choice.
  So behind the scenes Magento will now run each classname through this method to get to it's related file.

* **Describe methods for resolving module conflicts.**
  * There are **3 levels** of module compatibility conflicts
    * Conflicts with configuration files
      * When thow modules use the same class dependence, future conflicts may appear in the program parts of modules(improver use of the class constructor). This group of problems
        is closel connected to our second group 'conflicts with software part'.
        eg:

        Namespace_OtherModulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_OtherModulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_OtherModulename>
              </modules>
          </config>
        ```
        Namespace_Modulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_Modulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_Modulename>
              </modules>
          </config>
        ```

        We can fix them by replacing with the next code:

        Namespace_OtherModulename.xml

          ```xml
            <config>
                <modules>
                    <Namespace_OtherModulename>
                        <active>true</active>
                        <codePool>community</codePool>
                <depends><Mage_Something/></depends>
                    </Namespace_OtherModulename>
                </modules>
            </config>
          ```
          Namespace_Modulename.xml

          ```xml
              <config>
                  <modules>
                      <Namespace_Modulename>
                          <active>true</active>
                          <codePool>community</codePool>
                  <depends><Namespace_OtherModulename/></depends>
                      </Namespace_Modulename>
                  </modules>
              </config>
          ```

    * Conflicts with the software part
      * The main reason for module conflicts could be in the use of dependencies and class rewriting. You should regard your module
        configuration file(/Namespace_Modulename/etc/config.xml), in which class rewriting with `<rewrite></rewrite>` tags can be used. Eg

        ```xml
            <customer>
              <rewrite>
                   <form_edit>Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
               </rewrite>
            </customer>
        ```
        Incorrect use of class methods is the main reason of conflicts origin. There are several ways to solve this problem, but I would like to highlight the very best
        and most correct way in my opinion
        - The elimination of the classes overriding. The idea of this method is similar to the previous method of module conflict resilving and offers setting
          Dependence of one class from another
    * Conflicts in a module display

