	We can build the custom-image from the docker-compose.yml file

docker-compose.yml
```yml
version: '2'
services:
	proxy:
		build:
			context: .
			dockerfile: nginx.Dockerfile
		image: nginx-custom
		ports: 
			- '80:80'
	web:
		image: httpd
		volumes:
			- /html:/usr/local/apache2/htdocs/
```


context: . 
	That mean, its a path to the directory that contains `Dockerfile` file.


nginx.Dockerfile
```dockerfile
FROM nginx:1.23

COPY nginx.conf /etc/nginx/conf.d/default.conf
```

