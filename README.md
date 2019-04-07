egeneralov.nginx
=========

Provide nginx installation.

Requirements
------------

Debian-based system.


Example Playbook
----------------

    - hosts: servers
      vars:
        manage_iptables: false
        vhosts:
          - domain: "example.com"
            ssl: false
            rewrite_ssl: true
            rewrite_www: false
            root: /var/www/example.com
            index: "index.php index.html"
            locations:
        
              - path: /
                type: static
                index: "index.php index.html"
                try_files: "$uri $uri/ @fallback"
        
              - path: "@fallback"
                type: static
                rewrite: "^(.*)$ /index.php?$args last"
        
              - path: '~ \.php$'
                type: fastcgi
                try_files: "$uri = 404"
                fastcgi_param: "SCRIPT_FILENAME $document_root$fastcgi_script_name"
                fastcgi_pass:
                  - unix:/var/run/php/php7.0-fpm.sock
        
              - path: /proxy/
                type: proxy
                schema: "http://"
                proxy_to: 
                  - 127.0.0.1:8080
                  - 127.0.0.1:8081
                  - 127.0.0.1:8082
      roles:
         - egeneralov.nginx

License
-------

MIT

Author Information
------------------

Eduard Generalov <eduard@generalov.net>

