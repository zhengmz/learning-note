# 配置方式一：
  虚拟主机的配置

    <VirtualHost *:443>
        ServerName tracsite.coderanger.net
        ServerAdmin webmaster@coderanger.net
        
        # Note: This folder should exist, but will generally be empty
        DocumentRoot /srv/tracsite/htdocs
        <Directory /srv/tracsite/htdocs>
            Order allow,deny
            Allow from all
        </Directory>
        
        # ---- SSL ----
        # Note: If you are not using SSL, make sure to change the port above
        SSLEngine On
        SSLCipherSuite HIGH:MEDIUM    
        SSLProtocol all -SSLv2
        SSLCertificateFile /etc/ssl/...
        SSLCertificateKeyFile /etc/ssl/...
            
        # ---- TRAC ----
        # Note: See below for the contents of this file.
                The trailing slash is important, don't leave it off.
        ScriptAlias / /srv/tracsite/cgi-bin/trac.fcgi/
        
        # ---- AUTHENTICATION ----
        # Note: Do not add this section if you are planning to use AccountManager for logins.
        #       Use the normal htpasswd[2] tool to create/update this file.
        <Location /login>
            AuthType Basic
            AuthName "Trac Login"
            AuthUserFile /srv/tracsite/htpasswd
            Require valid-user
        </Location>
    </VirtualHost>

# 配置方式二：
  直接增加在conf.d增加trac.conf，内容如下：

    <IfModule mod_fcgid.c>
        AddHandler fcgid-script .fcgi
        FcgidIPCDir /tmp/fcgid_sock/
        FcgidProcessTableFile /tmp/fcgid_shm
    </IfModule>
     
    ScriptAlias /trac /var/www/fcgi-bin/trac.fcgi/
     
    FcgidInitialEnv TRAC_ENV_PARENT_DIR /opt/trac
     
    <LocationMatch "/trac/[^/]+/login">
       AuthType Basic
       AuthName "ARCH web Auth"
       AuthUserFile /opt/web_auth_file
       Require valid-user
    </LocationMatch>

# 可执行程序trac.fcgi
  注:内容如下（第二行的空行是必须的）

    #!/usr/bin/env python
     
    # ---- TRAC.FCGI ----
    # Note: This will work on >=0.9
     
    import os
     
    from trac.web.main import dispatch_request
    try:
        from flup.server.fcgi import WSGIServer
    except ImportError:
        from trac.web._fcgi import WSGIServer
     
    if __name__ == '__main__':
        if 'TRAC_ENV' not in os.environ and \
           'TRAC_ENV_PARENT_DIR' not in os.environ:
            os.environ['TRAC_ENV'] = '/opt/trac/svnproj'
        if 'PYTHON_EGG_CACHE' not in os.environ:
            if 'TRAC_ENV' in os.environ:
                egg_cache = os.path.join(os.environ['TRAC_ENV'], '.egg-cache')
            elif 'TRAC_ENV_PARENT_DIR' in os.environ:
                egg_cache = os.path.join(os.environ['TRAC_ENV_PARENT_DIR'], \
                                         '.egg-cache')
            os.environ['PYTHON_EGG_CACHE'] = egg_cache
        WSGIServer(dispatch_request).run()
