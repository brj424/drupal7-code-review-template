# review template


## Review Metadata

    Requested By: ?????
    Reviewed By: Tristan
    Completed On: 2017-03-1X
    Updates: Starting review
    Result: 
    

## Project Information

Modules in alpha, beta, dev or any other release other than production will not
be approved for use in production environments.

    Maintenance status: N/A (IRAD custom module)
    Development status: N/A (IRAD custom module)
    Module categories: N/A (IRAD custom module)
    Reported installs: N/A (IRAD custom module)


## Description

    Serves as an interface for accessing data from the LPS Course Production System


## Summary Results

    ///\\\


## Getting Started

The standard directory structure is as follows. Feel free to use your own
directory structure. If you do, be sure any config files and paths are updated
appropriately.

Location of contrib modules being Reviewed

    $HOME/projects/sas/drupal/drupal7/contrib

Location of local repos

    - $HOME/projects/sas/gitlab/infosec/drupal-static-review
    - $HOME/projects/sas/gitlab/infosec-docs/drupal7-code-review

If you don't already have the drupal-static-review repo, clone it.

    ```
    cd $HOME/projects/sas/gitlab/infosec-docs
    git clone https://github.com/clayball/drupal7-static-review.git
    ```

This example uses the xmlsitemap contrib module.

*. Add a new review directory on a new local branch

    ```
    cd $HOME/projects/sas/gitlab/infosec-docs/drupal7-code-review
    git checkout master
    git pull origin master
    git checkout -b xmlsitemap-$USER
    cd reviews
    mkdir xmlsitemap
    cd xmlsitemap
    cp ../../template/template.md xmlsitemap.md
    ```
    Update the template file with details for the contrib module under review.

*. Download and uncompress the contrib module

    ```
    cd $HOME/projects/sas/drupal/drupal7/contrib
    wget https://ftp.drupal.org/files/projects/xmlsitemap-7.x-2.3.tar.gz
    tar zxvf xmlsitemap-7.x-2.3.tar.gz
    ```

*. Run phploc on the contrib module directory

    ```
    cd $HOME/projects/sas/gitlab/infosec-docs/drupal7-code-review/reviews/xmlsitemap
    phploc --names=*.php,*.module,*.install,*.inc \
      ~/projects/sas/drupal/drupal7/contrib/xmlsitemap/ \
      --log-csv=xmlsitemap-loc.csv --log-xml=xmlsitemap-loc.xml > xmlsitemap-loc.txt
    ```
    The phploc command above will generate three files. We may use the various
    formats at a later date, e.g., for importing into HECTOR.

*. Run drupal-static-review and copy/move output to the review directory

    ```
    cd $HOME/projects/sas/gitlab/infosec/drupal-static-review
    ./drupal-static-review xmlsitemap
    mv reports/xmlsitemap-static-full.txt \
      ~/projects/sas/gitlab/infosec-docs/drupal7-code-review/reviews/xmlsitemap
    ```

## Review Items & Threat Model

Now it's time to install the module and try using it. I recommend doing this
locally and integrating the Drupal installation with an IDE, e.g. PhpStorm.
You should also install Xdebug and integrate it with PhpStorm.
Below is a list of items to look for when performing a code review. Keep in
mind that it's not only important to review and validate the correctness of
existing code, it's also important to consider if a security control or
mechanism is not being implemented when it should be.

Feel free to remove this when the review is complete. However, this should
remain as part of template.md.


### Install/Uninstall ###

- If a table is created is it removed when the module is uninstalled?

    Doesn't appear to create any tables.

- Are global variables used?

    Global variables are not used.

### Arbitrary Code Execution ###

The following items, if found, will prevent a module from being approved, full
stop. Depending on the module, use-case, and developer need, we may patch the
module and remove the dangerous functionality. Any patches will need to be
fully tested.

- Is eval() or php_eval() functions used?

    eval() / php_eval() not found.

- Is preg_replace being used?

    preg_replace appears to be absent.

    References:
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/using-php-with-eval-or-drupal_eval
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/do-not-use-e-in-preg_replace-use-preg_replace_callback-instead


### XSS ###

```
Note that all Drupal functions that return URLs (url(), request_uri(), etc.)
output plain URLs which have not been HTML escaped in any way (in other words,
they are plain-text).

Remember to use check_url() to escape them when outputting HTML (or XML). Don't
use check_url() in situations where a real URL is expected, e.g. in the HTTP
Location: ... header.

**The above note is why we search for url and uri in drupal-static-review. We
need to make sure check_url is used when outputting HTML or XML.**

Review sanitization functions listed on the following page.

https://api.drupal.org/api/drupal/includes!common.inc/group/sanitization/7.x

In order to identify most, if not all, of the following, run the drupal static
review script available on github at the link below

https://github.com/clayball/drupal7-static-review

Include the output from drupal-static-review.py.
```

- Is user input properly sanitized/Are validation functions used?

    Direct sanitization is not performed though I also don't think it's needed in this instance.
    
- Is output properly encoded?

    This module does not provide direct output to the user
    
- Is text handled in a secure way? 

   Text appears to be handled in a safe manner. 
    References:
    - https://www.drupal.org/node/28984
    - https://www.drupal.org/docs/7/security/writing-secure-code/handle-user-input-with-care
    - https://www.drupal.org/node/28984
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/avoid-using-data-from-form_stateinput


### SQLi ###

- Are all queries parameterized?

   Queries are not all parameterized though I don't believe all of them need to be. 
    References:
    - https://www.drupal.org/docs/7/security/writing-secure-code/overview
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/database-access


### Authorization ###

- Are permissions properly applied?
 
   Permissions are applied correctly.

### CSRF ###

- Does the module use the Form API for all requests that modify data?

   Form API is used.
 
- Does the module properly follow the Form API documentation?

   Yes.
 
    References:
    - https://www.drupal.org/docs/7/security/writing-secure-code/create-forms-in-a-safe-way-to-avoid-cross-site-request-forgeries


### Error Handling ###

- Ensure that all method/function calls that return a value have proper error
  handling and return value checking. 

    Robust error handling appears to be absent.

    References:
    - https://api.drupal.org/api/drupal/includes%21errors.inc/7.x
