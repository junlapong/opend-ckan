### CKAN ER-Diagram
https://www.researchgate.net/profile/Cristina_Ribeiro8/publication/295106208/figure/fig2/AS:487074768920579@1493139082654/Tables-for-metadata-in-CKANs-relational-schema.png

### Install pgAdmin4 dependencies, create a virtual environment, download, install & configure
```sh
#sudo apt-get install virtualenv python-pip libpq-dev python-dev

cd

virtualenv pgadmin4

cd pgadmin4

source bin/activate

pip install https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v4.21/pip/pgadmin4-4.21-py2.py3-none-any.whl

vi lib/python2.7/site-packages/pgadmin4/config_local.py
```
```sh
import os
DATA_DIR = os.path.realpath(os.path.expanduser(u'~/.pgadmin/'))
LOG_FILE = os.path.join(DATA_DIR, 'pgadmin4.log')
SQLITE_PATH = os.path.join(DATA_DIR, 'pgadmin4.db')
SESSION_DB_PATH = os.path.join(DATA_DIR, 'sessions')
STORAGE_DIR = os.path.join(DATA_DIR, 'storage')
SERVER_MODE = False
```
```sh
#Run
python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py
```

#### Access http://localhost:5050 (password: pgadmin)

```sh
#Run again
cd ~/pgadmin4

source bin/activate

python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py
```
