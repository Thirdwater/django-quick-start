# Django Quick-Start

## Installation

### Django

Treat `django` as just another python library, e.g.
```shell
# After something like source venv/bin/activate
pip install django

# Or as part of requirements.txt from an existing repo
pip install -r requirements.txt
```

Includes a lightweight web server for testing purposes.  
Otherwise, we also need Apache + `mod_wsgi` for deployment in an actual web server.

Verify with:
```python
import django

print(django.get_version())
```

### Apache

See for example: [DigitalOcean's tutorial on how to install apache on Debian 10](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10)

E.g. not just `apt-get install apache2`, but also how to setup firewalls, basic workflows involved in working with an Apache process, etc.

```shell
# Apache itself
apt-get install apache2

# Allowing traffics through the firewall, e.g.
ufw allow WWW
# Check with
ufw status

# Check running apache service
systemctl status apache2
# or check the landing page on the server's IP (e.g. hostname -I)
```

#### Working with the Apache Process

```shell
systemctl [verb] apache2
# Verbs:
 - status
 - start
 - stop
 - restart
 - reload (for configuration changes without dropping connections)
 - enable (start up at boot) (default)
 - disable
```

See [section 5](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10#step-5-%E2%80%94-setting-up-virtual-hosts-(recommended)) and [6](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10#step-6-%E2%80%93-getting-familiar-with-important-apache-files-and-directories) for virtual hosts and important Apache files and directories respectively.

### `mod_wsgi`

See [documentations](https://modwsgi.readthedocs.io/en/develop/) for details.

Install into Python/virtual environment:
```shell
pip install mod_wsgi
# or
pip install mod_wsgi-standalone
```

This have the benefit of being separated from the system's Apache installation (suitable for docker-like settings, etc.).

Verify with:
```shell
mod_wsgi-express start-server
# Visit localhost:8000
```

