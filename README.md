 WordPress Develop for ARM

## Purpose of this fork?

This fork of [wordpress-develop](https://github.com/WordPress/wordpress-develop) is compatible with ARM processors, specifically Apple Silicon chips like M1 and M2.

Usage is nearly identical to the original project ([README.md](https://github.com/WordPress/wordpress-develop/blob/trunk/README.md)). 

Main differences as compared with the original project:
- Docker services' images in this build use `arm` platform where possible. 
- WPCLI is bundled into the PHP image, as opposed to running in seperate container.
- Nginx and PHP are pre-configured for lengthy xdebugging sessions.
- Easier SSL config supporting multiple server names( no more annoying browser security roadblocks).

Whether you want to contribute to WordPress core or test your own projects against bleeding edge of WordPress, the mods in this fork should speed up your time to code.

## Notes and config options

### Nginx

#### ServerNames 
You can create multiple test servers to run simultaneosly on the nginx container.
To create 2 seperate servers that use the same configuration, for example, you may set the env variable in `docker-compose.override.yml`. 
```
  wordpress-develop:
    environment:
      SERVER_NAMES: 'wpdev1.local wpdev2.local'
```

#### Certificates
To enable HTTPS, create public and private certificate key pair at the following locations, using `mkcert`:  
- Create `tools/local-env/devunstuck-key.pem` private Key.
- Create `tools/local-env/devunstuck.pub` public Key.

If you choose to rename the keys with your own custom key pair name, remember to also:
- Update the relevant `volumes:` entries in `wordpress-develop` service.
- Update`ssl_certificate_key` and `ssl_certificate` in the nginx conf file `tools/local-env/default.conf/default.template` to match.
    ```
    ...
    ssl_certificate /root/custom-key-pair-name;
    ssl_certificate_key /root/custom-key-pair-name;
    ...
    ```

### PHP

The custom PHP image([devunstuck / php-xdebug-wpcli-arm](https://hub.docker.com/repository/docker/devunstuck/php-xdebug-wpcli-arm)) may be the biggest difference between this fork and the original project. 
The image is based on [php - Official Image](https://hub.docker.com/_/php), and can be used in similar fashion.

Includes:
- [WP-CLI](https://wp-cli.org/) 
    - The official [wordpressdevelop/cli](https://registry.hub.docker.com/r/wordpressdevelop/cli#!) image doesn't have a ARM compatible version.
    Rather than using a seperate container, wpcli is installed to avoid setup struggles on devices like Apple Silicon powered M1, M2.
- [Xdebug](https://xdebug.org/).
- strace

### WPCLI
To run `wp` commands, use the `wp_php` user.
E.g. if the php container is named `du-wordpress-develop-php-1`, do:
`docker exec -u wp_php du-wordpress-develop-php-1 wp help`

### Xdebug
Xdebug is enabled by default.