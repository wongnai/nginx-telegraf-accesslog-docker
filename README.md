# NGINX with telegraf-ready access log
This docker image is based on the official nginx. All requests are logged to /var/log/nginx/rt.log in influx line protocol format. Therefore it can be read by telegraf using tail input and put to influxdb in order to monitor web performance.

Logrotate is also setup to rotate log file daily so that it does not grow too large.

## Extending image
The access log configuration is placed on /etc/nginx/nginx.conf. If you already have your nginx.conf file, you may need to configure log yourselve.
You can include /etc/nginx/response_time in your nginx.conf file to get the telgraf-ready log format. For example:-

    include /etc/nginx/response_time
    access_log  /var/log/nginx/rt.log   response_time;

## telegraf
To configure telegraf to read the log file and store them in influxdb, running it in separate docker container and set telegraf.conf like below:-

    [[inputs.tail]]
        files = ["/var/log/nginx/rt.log"] 
        from_beginning = false
        pipe = false
        data_format = "influx"

    [[outputs.influxdb]]
        urls = ["http://influxdb:8086"]
        database = "telegraf"

You should see nginxrt measurement in your influxdb.