# Document explains how to create debian package with `.deb` extension

## Structure of the DEB package must be like as the following

- `node_exporter` - top level folder of the package name
- `DEBIAN` - Folder with builtin name and must be created with the same name
- `control` - It is information file to installation which defines type of the package, maintainer etc.
- `postinst` - File (with executable permission `+x`) which will be executed at the end (Bash code)
- `etc/systemd/system` - Means file `node_exporter.service` inside of this folder will be copied to the same `/etc/systemd/system/` path in the OS file system.

```bash
➜ ~ tree
.
├── node_exporter
│   ├── DEBIAN
│   │   ├── control
│   │   └── postinst
│   └── etc
│       └── systemd
│           └── system
│               └── node_exporter.service
└── README.md

5 directories, 4 files
```

#### Install needed package to prepare debian based `.deb` files

```bash
$ apt-get install build-essential
```

#### Create deb package from `node_exporter` folder(command creates `node_exporter.deb` in the output)

```bash
$ dpkg-deb --build node_exporter
```

#### Install created package (This command firsly create `/etc/systemd/system` directory and copies `node_exporter.service` file inside of this folder. Then it will execute `postinst` script to download `node_exporter` binary file and place into `/usr/sbin` folder. At the end script start service and delete unnneded files.)

```bash
$ dpkg -i node_exporter.deb
```

#### Look at the service status at the end:

```bash
$ ~ systemctl status node_exporter.service
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2021-09-05 16:09:14 +04; 16min ago
   Main PID: 26810 (node_exporter)
      Tasks: 6 (limit: 3543)
     Memory: 3.6M
        CPU: 7ms
     CGroup: /system.slice/node_exporter.service
             └─26810 /usr/sbin/node_exporter --web.listen-address=:9108
```
