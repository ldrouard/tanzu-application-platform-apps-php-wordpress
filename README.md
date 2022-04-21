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
