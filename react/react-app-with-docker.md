# React development with docker

create app folder and change directory to the app folder

```
mkdir myapp
cd myapp
```

now start a node container with the app directory mounted as a volume

```
docker run -it -p 3000:3000 --rm --name app --volume `pwd`:/app node /bin/bash
```

now in the container shell do the following

```
cd /app/
npx create-react-app .
npm start
```
