
```
docker run -it ubuntu /bin/bash
```
now execute the following in side the container shell

```
apt update
```

```
apt install -y wget curl xz-utils vim git inetutils-ping
```

```
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
```

```
apt-get install -y nodejs
```

```
node -v
```

```
npx create-react-app /tmp/app
```

add the following aliases

```
alias gs='git status'
alias gl='git log'
alias gaa='git add -A'
alias gcm='git commit -m'
alias gi='git init'
alias gcgu='git config --global user.name'
alias gcge='git config --global user.email'

alias cra='npx create-react-app'
```
now commit the container

```
docker commit c0561d2d4998 react:18
```

---


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
cd /
npx create-react-app /app
npm start
```
