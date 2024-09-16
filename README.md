
# PIN 1 - Instalando Docker, Custom Jenkins, Pipelines, Registry propio y scan



### Instalando Docker en Ubuntu Server 24.04.1

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```


### File Structure

```bash
├── authentik
│   ├── certs
│   ├── custom-templates
│   ├── database
│   ├── media
│   │   └── public
│   │       ├── favicon.svg
│   │       ├── flow-backgrounds
│   │       │   └── star-wars-poster-4k-af-1920x1080.jpg
│   │       └── logo.png
│   └── redis
├── docker-compose.yml
├── ohif
│   ├── config
│   │   ├── logo.png
│   │   └── ohif.js
│   └── nginx
│       ├── default-ohif-nginx.conf
│       └── ohif-nginx.conf
├── orthanc
│   ├── config
│   │   ├── orthanc.json
│   │   └── postgresql.json
│   ├── database
│   └── dicomImages
├── proxy
└── README.md
```

### Set `.env` variable for Authentik

```bash
echo "PG_PASS=$(openssl rand 36 | base64)" >> .env
echo "AUTHENTIK_SECRET_KEY=$(openssl rand 60 | base64)" >> .env
```

### Set Orthanc UserName & Password
> Use you favourite text editor
```bash
nvim ./orthanc/config/orthanc.json
```
```json
   "AuthenticationEnabled": true,
  "RegisteredUsers": {
    "hyper": "enterlab"
  },
```

### Create generate the base64-encoded string
```bash
echo -n 'hyper:enterlab' | base64.
aHlwZXI6bWFwZHI=
```

### Update Nginx config for Ohif to pass http auth for accessing Orthanc
Edit Nginx reverse proxy
```bash
nvim ./ohif/nginx/ohif.conf
```
and paste base64-encoded user name and password

```conf
proxy_set_header Authorization "Basic aHlwZXI6bWFwZHI=";  # Replace with base64-encoded credentials
```
> Whenever you access OHIF it won't ask for Orthanc auth and password. 

### Spin up containers
```bash
docker-compose up -d
```
