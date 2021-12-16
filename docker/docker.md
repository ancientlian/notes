# nginx配置

```bash
docker run -d -p 80:80 --name nginx02 -v F:\WorkSpace\nginx\nginx.conf:/etc/nginx/conf.d/default.conf nginx
```



> [Error - nginx: [emerg\] "server" directive is not allowed here in /etc/nginx/nginx.conf:1](https://stackoverflow.com/questions/59848507/error-nginx-emerg-server-directive-is-not-allowed-here-in-etc-nginx-ngin)
>
> In your context you need to wrap server in http block. Or move you config file not by replacing the default config file, but rather replacing a default server config located in /etc/nginx/conf.d/ So you can use something like
>
> ```
>         - ./nginx.conf:/etc/nginx/conf.d/default.conf
> ```
>
> for volumes for nginx. That will rewrite your default server config. You can also copy it to sites-enabled folder if you planning to use multiple config files.
>
> Or you can modify your nginx config, by starting with wrapping it with http block

