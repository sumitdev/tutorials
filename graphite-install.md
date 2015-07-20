

# Installation

Update and install some dependencies:

yum -y update 
yum install -y httpd net-snmp perl pycairo mod_wsgi python-devel git gcc-c++ --enablerepo=epel

Install Python Package manager:

yum install -y python-pip node npm 

Install graphite within pip

pip-2.6 install 'django<1.6' 
pip-2.6 install 'Twisted<12' 
pip-2.6 install django-tagging 
pip-2.6 install whisper 
pip-2.6 install graphite-web 
pip-2.6 install carbon

Configuration

cp /opt/graphite/examples/example-graphite-vhost.conf /etc/httpd/conf.d/graphite.conf 
cp /opt/graphite/conf/storage-schemas.conf.example /opt/graphite/conf/storage-schemas.conf 
cp /opt/graphite/conf/storage-aggregation.conf.example /opt/graphite/conf/storage-aggregation.conf 
cp /opt/graphite/conf/graphite.wsgi.example /opt/graphite/conf/graphite.wsgi 
cp /opt/graphite/conf/graphTemplates.conf.example /opt/graphite/conf/graphTemplates.conf 
cp /opt/graphite/conf/carbon.conf.example /opt/graphite/conf/carbon.conf

Apply the fix mentioned here - http://www.reemachugani.com/blog/2015/feb/14/no-module-named-fields-importing-GenericForeignKey/
which says to edit /usr/local/lib/python2.6/site-packages/tagging/models.py and replace
from django.contrib.contenttypes.fields import GenericForeignKey
with 
from django.contrib.contenttypes.generic import GenericForeignKey

chown -R apache:apache /opt/graphite/storage/ 

Add this to /opt/graphite/conf/storage-schemas.conf 

[default] 
pattern = .* 
retentions = 10s:4h, 1m:3d, 5m:8d, 15m:32d, 1h:1y

cd /opt/graphite/webapp/graphite 

sudo python manage.py syncdb

Start carbon

/opt/graphite/bin/carbon-cache.py start

Start graphite through either of these options

Built in python server which runs at port 8080
/opt/graphite/bin/run-graphite-devel-server.py /opt/graphite/

Apache at port 80
sudo service httpd start

apache configuration is here /etc/httpd/conf.d/graphite.conf logs - /opt/graphite/storage/log/webapp

Open http://ip:port
