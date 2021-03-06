# XMLsitemap Review

## Review Metadata

    Requested By: Jeff Lyons
    Reviewed By: Clay, Tristan, Hunter
    Completed On:
    Updates:
    Result: 
    

## Project Information

Modules in alpha, beta, dev or any other release other than production will not
be approved for use in production environments.

    Maintenance status: Seeking co-maintainer(s)
    Development status: Maintenance fixes only
    Module categories: Drush, Search, Third-party Integration, Utility
    Reported installs: 300,805

## Description

The XML sitemap module creates a sitemap that conforms to the sitemaps.org specification.
This helps search engines to more intelligently crawl a website and keep their results up
to date. The sitemap created by the module can be automatically submitted to Ask,
Google, Bing (formerly Windows Live Search), and Yahoo! search engines. The module also
comes with several submodules that can add sitemap links for content, menu items,
taxonomy terms, and user profiles.


## Summary Results

Check issues on website/code vulns, put them here

## Getting Started

The standard directory structure is as follows. Feel free to use your own
directory structure. If you do, be sure any config files and paths are updated
appropriately.

Location of contrib modules being Reviewed

    - $HOME/tristan/Documents/drupal-contrib

Location of local repos

    - $HOME/tristan/Documents/drupal-static-review
    - $HOME/tristan/Documents/drupal7-code-review

*. Review the module on drupal.org (metadata, issues)

    2 Critical - little to no impact 
    18 Major - Potential permissions issue - https://www.drupal.org/node/1817124
    User's without 'administer xmlsitemap' can edit XML sitemap fieldset page, however a patch has
    been provided.
    
    - Is there anything we should know about?
    
      - Nothing of security related note. Just feature requests and normal
      bugs. All issues have been visited within the last 2 weeks.

    Narrow the search Priority from 'Major' to 'Critical'

    - Is there anything we should know about?
    - Note the number of issues and whether or not the issues would prevent the
      module from passing review.
      
      - Nothing of note. All issues have been visited within the last 2
      weeks.

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

	- Yes, module implements hook_schema() to manage tables. They are
	automatically uninstalled when module is removed.
    
- Are global variables used?

    TODO: answer

### Arbitrary Code Execution ###

The following items, if found, will prevent a module from being approved, full
stop. Depending on the module, use-case, and developer need, we may patch the
module and remove the dangerous functionality. Any patches will need to be
fully tested.

- Is eval() or php_eval() functions used?

    No

- Is preg_replace being used?

    Yes, but /e flag is not being used. Should be safe.

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

    No sanitization functions are used, due to the nature of the module
    they are unnecessary.
    
- Is output properly encoded?

    TODO: this one will take a while
    
- Is text handled in a secure way? 

    Proper validation is used in admin forms, no arbitrary code possible.
    
    References:
    - https://www.drupal.org/node/28984
    - https://www.drupal.org/docs/7/security/writing-secure-code/handle-user-input-with-care
    - https://www.drupal.org/node/28984
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/avoid-using-data-from-form_stateinput


### SQLi ###

- Are all queries parameterized?

    TODO: answer
    
    References:
    - https://www.drupal.org/docs/7/security/writing-secure-code/overview
    - https://www.drupal.org/docs/7/security/writing-secure-code-0/database-access


### Authorization ###

- Are permissions properly applied?
 
    Yes
    

### CSRF ###

- Does the module use the Form API for all requests that modify data?

    TODO: answer
    
- Does the module properly follow the Form API documentation?

    TODO: answer
    
    References:
    - https://www.drupal.org/docs/7/security/writing-secure-code/create-forms-in-a-safe-way-to-avoid-cross-site-request-forgeries


### Error Handling ###

- Ensure that all method/function calls that return a value have proper error
  handling and return value checking. 

    TODO: answer

    References:
    - https://api.drupal.org/api/drupal/includes%21errors.inc/7.x


### TODO: to properly answer the rest of the questions, submodules xmlsitemap_il8n,
xmlsitemap_node, xmlsitemap_taxonomy, xmlsitemap_user need to be looked at.

- At present module should be approved. Hunter Kippen 12/11/16
