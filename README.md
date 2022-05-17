# tanzu-application-platform-apps-php-wordpress
Tanzu Application Platform - PHP / Legacy language - Tests Apps with WordPress

## Update WordPress code
Choose a WordPress version
```bash
WORDPRESS_VERSION='5.9.3'
```

Download and extract WordPress archive
```bash
wget https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz
tar -zxvf wordpress-${WORDPRESS_VERSION}.tar.gz
rm -vf wordpress-${WORDPRESS_VERSION}.tar.gz
```

Restore previous configuration
```bash
cp -vp src/wp-config.php wordpress/
```

Add debug file (uncomment entries with extreme precaution!)
```bash
cat > wordpress/666-debug.php << "EOF"
<?php
  // /!\ Security Issues
  //phpinfo(INFO_GENERAL);
  //phpinfo(INFO_CONFIGURATION);
  //phpinfo(INFO_MODULES);
  
  // /!\/!\ High Security Issues
  //phpinfo(INFO_VARIABLES);
  //phpinfo(INFO_ENVIRONMENT);
?>
EOF
```

Replace old version
```bash
git rm -r src/*
mv -v wordpress src
```
 
Update Git repository
```bash
git add .
git commit -m "Upgrade WordPress to ${WORDPRESS_VERSION}"
git tag -a ${WORDPRESS_VERSION} -m "Upgrade WordPress to ${WORDPRESS_VERSION}"
git push && git push --tags
```

## Recreate config file from scratch
If you prefer to recreate a `wp-config.php` from scratch instead of restoring old configuration, it's possible
```bash
# Use config sample
cp -vp wordpress/wp-config-sample.php wordpress/wp-config.php

# Clear '^M' characters from Wordpress conf
sed -i "s/\r//g" wordpress/wp-config.php

# Define MariaDB parameters
sed -i "s/^define( 'DB_NAME'.*/define( 'DB_NAME', \$_SERVER['RDS_NAME'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'DB_USER'.*/define( 'DB_USER', \$_SERVER['RDS_USER'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'DB_PASSWORD'.*/define( 'DB_PASSWORD', \$_SERVER['RDS_PASS'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'DB_HOST'.*/define( 'DB_HOST', \$_SERVER['RDS_HOST'] . ':' . \$_SERVER['RDS_PORT'] );/1" wordpress/wp-config.php

# Define WordPress Secrets parameters
sed -i "s/^define( 'AUTH_KEY'.*/define( 'AUTH_KEY', \$_SERVER['WP_AUTH_KEY'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'SECURE_AUTH_KEY'.*/define( 'SECURE_AUTH_KEY', \$_SERVER['WP_SECURE_AUTH_KEY'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'LOGGED_IN_KEY'.*/define( 'LOGGED_IN_KEY', \$_SERVER['WP_LOGGED_IN_KEY'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'NONCE_KEY'.*/define( 'NONCE_KEY', \$_SERVER['WP_NONCE_KEY'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'AUTH_SALT'.*/define( 'AUTH_SALT', \$_SERVER['WP_AUTH_SALT'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'SECURE_AUTH_SALT'.*/define( 'SECURE_AUTH_SALT', \$_SERVER['WP_SECURE_AUTH_SALT'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'LOGGED_IN_SALT'.*/define( 'LOGGED_IN_SALT', \$_SERVER['WP_LOGGED_IN_SALT'] );/1" wordpress/wp-config.php
sed -i "s/^define( 'NONCE_SALT'.*/define( 'NONCE_SALT', \$_SERVER['WP_NONCE_SALT'] );/1" wordpress/wp-config.php
```

Manually add some custom parameters...
