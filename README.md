# pyvision v0.1

This is an open source python/django project for image recognition using caffe framework. The current 0.1.0 version is coded using Python 3.7.1 and Django 2.1.2. The example demo is on https://www.mutualact.top. The sina dockers have only 512 MB + 2 GB memory and it takes 8 to 15 seconds for recognizing one single image if no sequene ahead of this image. Upon deploying this app on Microsoft Azure Virtual Machine, it took only 100 ms per recognition. However Azure charges about USD $10 per day for renting that Virtual Machine (16GB memory, 8-core CPU, without GPU, SSD disk). The current trained model was the Champion winner in 2012 ImageNet Competition. 

For database migrations, you can use the MySQL statements provided (/sae/readme/database.sql) or use Django commands (python ./manage.py makemigrations and then python ./manage.py migrate). If you use Django commands, do not forget to insert 1000 records into Table basic_synsetwords, check the /sae/readme/database.sql file.

For secret key files, please check the /sae/readme/\*.example files. However, if you are not using Sina SAE and SCS Cloud Servers, you may need to change all of these files according to your server environment. The sae/settings.py file should also be changed in serveral places, such as os.environ.get('MYSQL_PORT'). These are environment variables provided by Sina SAE dockers.

For internationalization, you can use Django commands (python ./manage.py makemessages -l zh_Hans and then python ./manage.py compilemessages). If you added any new words for translation, before running the compilemessages command, you may want to check the path-to-your-project/sae/common_files/locale/zh_Hans/LC_MESSAGES/django.po file.

The /sae/readme/recognition/daemon.py file contains two classes, the parent process for conducting the image recognition while the children processes will use requests module to submit recognized results to a uri of Django to update database tables. The parent process has a caffe trained model loaded in memory (about 700 MB). When conducting the image recognition, about 1.5 GB memory is needed. It is recommended NOT to run two or more image recognition processes (parent processes) simultaneously, otherwise your server memory will be dried out. These daemon files need to be deployed like this: if ~/recognition/daemon.py sits in ~/recognition folder, then ~/recognition/daemon/basic.py and ~/recognition/daemon/\_\_init\_\_.py are supposed to be in ~/recognition/daemon folder. Other folders/files like ~/recognition/images_done, ~/recognition/images_waiting, ~/recognition/tmp, ~/recognition/child.py are also needed. And ~/log/recognition folder is needed for logging of the daemon processes. 

After all are deployed, start the parent process by running a command like this:
python2 ~/recognition/daemon.py start
It is recommended to use python2.7 to run parent process, same as this trained model. Other trained models could be deployed using Python3 though. Of course, before running the command above-mentioned, do not forget to start your web server such as Nginx or Apache and then uwsgi server, also the MySQL server. 

If Baidu translation API is needed, you need to visit http://api.fanyi.baidu.com/api/trans/product/index and register an account and change /sae/extend/baidu.py file.

In /sae/home/views.py file, we have one line like this: url = sinaResponse.url.replace('http://','https://cdn.'). But this project read images and then output base64 string to the front end. Therefore cdn is useless. But if your client's images are NOT sensitive and your clients do not care privacy you may expose the uri such as https://cdn.xxx.com/bucketname/pathtoyourimage.jpg in frontend templates, by doing so, image loading should be faster.
