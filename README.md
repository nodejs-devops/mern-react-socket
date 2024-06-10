# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

```sh
npm create vite@latest .
```

```
React
Javascript
```

```sh
npm install
npm run dev

npm install --save-dev prettier eslint eslint-plugin-react eslint-config-prettier eslint-plugin-jsx-a11y
npx eslint src
npx eslint src --fix
npx eslint src
npm pkg set scripts.lint="eslint src"
npm run lint
npm install --save-dev husky lint-staged

git init -b master
git add package.json
git commit -am "chore: Initial commit"

npm pkg set scripts.prepare="husky install"
npm run prepare
npx husky init
echo "npx lint-staged" > .husky/pre-commit

git add .
git commit -m "chore: Basic project setup"

npm install --save-dev @commitlint/cli @commitlint/config-conventional
echo "npx commitlint --edit ${1}" > .husky/commit-msg
npx husky add .husky/commit-msg 'npx commitlint --edit ${1}'

git add .
git commit -m "no type"
git commit -m "wrong: type"
git commit -m "chore: Configure commitlint"
```

```sh
docker run -it ubuntu:24.04 /bin/bash
uname -a
exit

docker run -d --name dbserver -p 27017:27017 --restart unless-stopped mongo:6.0.4
mongosh mongodb://localhost:27017/ch2
console.log("test")

db.users.insertOne({ username: 'dan', fullName: 'Daniel Bugl', age: 26 })
db.users.find()

db.users.insertMany([
  { username: 'jane', fullName: 'Jane Doe', age: 32 },
  { username: 'john', fullName: 'John Doe', age: 30 }
])

db.users.findOne({ username: 'jane' })
db.users.find({ fullName: 'Daniel Bugl' })
db.users.findOne({ _id: ObjectId('6405f062b0d06adeaeefc3bc') })
db.users.find({ age: { $gt: 30 } })
db.users.find().sort({ age: 1 })

# Updating documents
db.users.updateOne({ username: 'dan' }, { $set: { age: 27 } })
db.users.updateOne({ username: 'new' }, { $set: { fullName: 'New User' } })
db.users.updateOne({ username: 'new' }, { $set: { fullName: 'New User' } }, { upsert: true })

db.users.deleteOne({ username: 'new' })
```

**Accessing the database via VS Code**

1. Click on the MongoDB icon on the left sidebar
2. A new **MongoDB** tab will open up. In this tab, click on `Connect` in the `Connect with Connection String` box:
3. A popup should open at the top. In this popup, enter the following connection string to connect to your local database:

   ```
   mongodb://localhost:27017/
   ```

**Accessing the MongoDB database via Node.js**

```sh
npm install mongodb
node backend\mongodbweb.js
```

```sh
npm uninstall --save react react-dom
npm uninstall --save-dev vite @types/react @types/react-dom @vitejs/plugin-react eslint-plugin-jsx-a11y eslint-plugin-react

npm install mongoose
```

```sh
npm install --save-dev jest mongodb-memory-server
npm install express
npm install dotenv
npm install --save-dev nodemon
npm install body-parser
npm install cors
```

```sh
docker image build -t blog-backend backend/
docker run -it -e PORT=3001 -e DATABASE_URL=mongodb://host.docker.internal:27017/blog -p 3001:3001 blog-backend
docker build -t blog-frontend .
docker run -it -p 3000:80 blog-frontend
docker stop dbserver
docker compose up
```

### Creating an account on Google Cloud

---

1. Go to https://cloud.google.com in your browser.
2. Press **Get started for free** if you do not have an account yet or press **Sign in** if you already have an account.
3. Log in with your Google account and follow the instructions until you have access to the Google Cloud console.

**Deploying our Docker images to a Docker registry**

1. Go to https://hub.docker.com and log in or register an account there.
2. Press the **Create repository** button to create a new repository. The repository will contain our image.
3. Enter `blog-frontend` as the repository name and leave the description empty and visibility `public`. Then press the Create button.
4. Repeat Steps 2 and 3, but this time, enter `blog-backend` as the repository name.
5. Open a new terminal and enter the following command to log in to your Docker Hub account.

   ```sh
   docker login
   ```

   Enter your username and password from Docker Hub and press the _Return_ key or _Enter_.

6. Rebuild your image for Linux (to be able to deploy it to Google Cloud later), tag your image with your repository name (replace [USERNAME] with your Docker Hub username), and push it to the repository

   ```bash
   docker build --platform linux/amd64 -t blog-frontend .
   docker tag blog-frontend [USERNAME]/blog-frontend
   docker push [USERNAME]/blog-frontend
   ```

7. Navigate to `backend/` in the terminal and repeat Step 6 for the `blog-backend` image

   ```sh
   cd backend/
   docker build --platform linux/amd64 -t blog-backend .
   docker tag blog-backend [USERNAME]/blog-backend
   docker push [USERNAME]/blog-backend
   ```

**Deploying the backend Docker image to Cloud Run**

1. Go to https://console.cloud.google.com/.
2. In the search bar at the top, enter `Cloud Run` and select the **Cloud Run – Serverless for containerized applications** product.
3. Press the **Create Service** button to create a new service.

   You may need to first create a project before you can create a service. In that case, just follow the instructions on the website to create a new project with a name of your choice. Afterward, press the Create Service button to create a new service.

4. Enter [USERNAME]/blog-backend in the **Container** image URL box.
5. Enter `blog-backend` in the **Service name** box, select a region of your choice, leave **CPU is only allocated during request processing** selected, and select **All – Allow direct access to your service from the Internet and Authentication – Allow unauthenticated invocations**.
6. Expand the **Container, Networking, Security** section, scroll down to **Environment variables**, and click on **Add Variable**.
7. Name the new environment variable `DATABASE_URL` and, as the value, enter the connection string from MongoDB Atlas, which you saved earlier.

   For simplicity, we are using a regular environment variable here. To make variables that contain credentials more secure, it should instead be added as a secret, which requires enabling the Secrets API, adding the secret to the secret manager, and then referencing the secret and choosing it to be exposed as an environment variable.

8. Leave the rest of the options as the default options and press **Create**.
9. You will get redirected to the newly created service, where the container is currently being deployed. Wait until it finishes deploying, which can take up to a couple of minutes.
10. When the service finishes deploying, you should see a checkmark and a URL. Click the URL to open the backend and you will see our **Hello World from Express!** message, which means that our backend was successfully deployed in the cloud!

**Deploying the frontend Docker image to Cloud Run**

For the frontend, we first need to rebuild the container to change the `VITE_BACKEND_URL` environment variable, which is statically built into our project.

1. Open a terminal and run the following command to rebuild the frontend with the environment variable set

   ```sh
   docker build --platform linux/amd64 --build-arg "VITE_BACKEND_URL=[URL]/api/v1" -t blog-frontend .
   ```

   Make sure to replace [URL] with the URL to the backend service deployed on Google Cloud Run.

2. Tag it with your Docker Hub username and deploy the new version of the image to Docker Hub

   ```sh
   docker tag blog-frontend [USERNAME]/blog-frontend
   docker push [USERNAME]/blog-frontend
   ```

Now, we can repeat similar steps as we did to deploy the backend to deploy our frontend as well

1. Create a new Cloud Run service, enter `[USERNAME]/blog-frontend` in the Container image URL box and `blog-frontend` in the Service name box.
2. Pick a region of your choice and enable **Allow unauthenticated invocations**.
3. Expand **Container, Networking, Security** and change the container port from `8080` to `80`.
4. Press **Create** to create the service and wait for it to be deployed.
5. Open the URL in your browser and you should see the deployed frontend. Adding and listing blog posts also works now by sending a request to the deployed backend, which then stores the posts in our MongoDB Atlas cluster.

We have successfully manually deployed our first full-stack React and Node.js application with a MongoDB database in the cloud! In the next sections, we are going to focus on automating testing and deployment using CI/CD.

### Configuring CD to automate the deployment

---

**Getting Docker Hub credentials**

1. Go to https://hub.docker.com/.
2. Click on your profile and go to your **Account settings**.
3. Click on the **Security** tab and press the **New Access Token** button.
4. As a description, write `GitHub Actions` and press the **Generate** button. Give **Read, Write, Delete** permissions.
5. Copy the access token and store it in a safe place.
6. Go to your GitHub repository and then go to **Settings | Secrets and variables | Actions**.
7. Press the **New repository secret** button to add a new secret. As a name, write `DOCKERHUB_USERNAME`, and as a secret value, use your username on Docker Hub.
8. Add another secret with the name `DOCKERHUB_TOKEN` and paste your previously created access token as the secret value.

**Getting Google Cloud credentials**

1. Go to https://console.cloud.google.com/.
2. In the search box on the top, enter **Service accounts** and go to the **IAM and admin – Service accounts** page.
3. Press the **Create Service Account** button.
4. In the **Service account name** box, enter `GitHub Actions`. The ID should automatically be generated as `github-actions`. Press **Create and Continue**.
5. Grant the service access to the **Cloud Run Admin** role and press **Continue**.
6. Press **Done** to finish creating the service account.
7. On the overview list, copy the email of your newly created service account and save it for later use.
8. Go to the default compute service account by clicking on its email address. Go to the **Permissions** tab and press **Grant Access**.
9. Paste the email of your newly created service account into the **New principals** field and assign the **Cloud Run Service Agent** role. Press **Save** to confirm.
10. On the overview list, press the three dots icon to open actions on your `github-actions` service account and select **Manage keys**.
11. On the new page, press **Add Key | Create New Key**, and press **Create** in the popup. A JSON file should be downloaded.
12. Go to your GitHub repository, and go to **Settings | Secrets and variables | Actions**. Press the **New repository** secret button to add a new secret.
13. Add a new secret on your GitHub repository called `GOOGLECLOUD_SERVICE_ACCOUNT` and paste the previously copied email of your newly created service account as a secret value.
14. Add a new secret on your GitHub repository called `GOOGLECLOUD_CREDENTIALS` and as the secret, paste in the contents of the downloaded JSON file.
15. Add a new secret on your GitHub repository called `GOOGLECLOUD_REGION` and set the secret value to the region you selected when creating the Cloud Run services.

    For better security, Google recommends using workload identity federation instead of exporting service account key JSON credentials. However, setting up workload identity federation is a bit more complicated. More information on how to set it up can be found here: https://github.com/google-github-actions/auth#setup.
