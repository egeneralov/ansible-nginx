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
        pkg_state: latest
        
        worker_rlimit_nofile: 65535
        worker_processes: auto
        worker_connections: 4000
        resolver:
          servers:
            - 1.0.0.1
            - 8.8.4.4
          valid: 180s
        server_names_hash_bucket_size: 128
        client_max_body_size: 128m
        
        vhosts:
          - domain: "example.com"
            ssl: false
            rewrite_ssl: false
            rewrite_www: true
            root: /var/www/example.com
            index: "index.php index.html"
            raw: |
              
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
                raw: |
                  proxy_http_version 1.1;
                  proxy_set_header Connection $connection_upgrade;
                  proxy_set_header Upgrade $http_upgrade;
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

