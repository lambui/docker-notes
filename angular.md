## Create new Angular image with docker-compose and angular-cli

### Prerequisites:
+ Docker
+ Know how to use angular-cli to create new angular project

### Step:
**1. Create folder for your Docker project and your new angular project inside:**

terminal:
```
mkdir docker-project
cd docker-project
ng new angular-project
```
  
file explorer:
```
docker-project
|- angular-project
   |- ...
```

**2. Create Dockerfile inside angular-project:**

Dockerfile:
```
#create image based on node image on docker hub
FROM node:6

#create usr/src/app dir and point workdir to that so command run from that dir
ENV HOME=/usr/src/app
RUN mkdir $HOME
WORKDIR $HOME

#install angular-cli globally
RUN npm install -g angular-cli

#expose port 4200 and 49153 because angular-cli will use those
EXPOSE 4200
EXPOSE 49153
```

file explorer:
```
docker-project
|- angular-project
   |- Dockerfile
   |- ...
```

**3. Create docker-compose.yml inside docker-project:**

docker-compose.yml
```
version: '3'
services:
  angular: 
    build: ./angular-project
    volumes:
      - ./angular-project:/usr/src/app
    ports:
      - "4200:4200"
      - "49153:49153"
    command: npm start
```

file explorer:
```
docker-project
|- docker-compose.yml
|- angular-project
   |- Dockerfile
   |- ...
```

**4. Run ```docker-compose build``` to build the image with docker-compose:**

terminal (make sure it at docker-project, not angular-project):
```
docker-compose build
```

if success, we get new angular image. There are a few more steps to do before we can run it.

**5. Modify package.json in angular-project:**

In angular-project folder, there should be a file called package.json.  
file explorer:
```
docker-project
|- docker-compose.yml
|- angular-project
   |- package.json
   |- Dockerfile
   |- ...
```

Open it, and modify this line like so:
```
"scripts": {
  "start": "ng serve --host 0.0.0.0",
  ...
},
...
```
This change ensures that the app is served from the host created by docker image.

**6. Run ```docker-compose up``` to run your newly created angular image:**

terminal (at docker-project):
```
docker-compose up
```

if success, you can go to localhost:4200 on browser to see the angular image working

### Note:
+ **.dockerignore**  
In angular-project folder, you can create .dockerignore to make the build faster and lighter.  
Make sure .dockerignore is in the same folder as Dockerfile. Below is the one I used:  
  
.dockerignore
```
.git
.ipynb_checkpoints
notebooks
unused
Dockerfile
.DS_Store
.gitignore
README.md
env.*
devops
node_modules

# To prevent storing dev/temporary container data
*.csv
tmp
```

+ **docker-compose up error: Docker Compose: No such image:**  
Look [here](https://stackoverflow.com/questions/37454548/docker-compose-no-such-image/37976842).  
To resolve this, in terminal at docker-project I ran  
  
terminal:
```
docker-compose ps 
docker-compose rm [corrupted cached image]
```
Then try re-run ```docker-compose up``` again.



