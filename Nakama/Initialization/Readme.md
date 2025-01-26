## Setting up local Nakama Server: ##
First thing we can do with Nakama is to authenticate users, but first we need to initialize the Nakama Server in our project. To integrate Nakama into the project, setting up the local nakama server with ability to create leaderboard and authenticate users, follow these steps:

**Set Up the Nakama Server:**
  Here we'll show how to set up and run a local Nakama server with a createleaderboard RPC function to create leaderboards.
  **note that creating leaderboards is not supported automatically from the Nakama SDK within the project. you have to set it up with typescript during server runtime.**

- **Download Dependencies:** 
first we need to set up a nakama server, in this case, we'll do it using typescript and docker.
Ensure that you have node and Docker Descktop installed on your machine. you can find the installation files here:

  [Download Docker Descktop from here](https://www.docker.com/products/docker-desktop/)

  [Download Node from here](https://nodejs.org/en/download)

  Download and install them and **make sure the docker engine is up and running.**

- **Create the project:**
  open up a terminal (BASH is recommended) in a desiered directory and run the following command:
  ~~~bash
  npm init -y
  ~~~
  there is a couple of dependencies we need to install

  **First:** Install Typescript as dev dependency:
  ~~~BASH
  npm i -D typescript
  ~~~
  **Second:** we will install the nakama runtime package directly from github:
  ~~~BASH
  npm i https://github.com/heroiclabs/nakama-commn
  ~~~


- **Configuration:** 

  open up your code editor and create a file called **tsconfig.json** in the root of the project directory
  ~~~json
  {
    "files": [
        "./main.ts",
        "createleaderboard.ts",
    ],
    "compilerOptions": {
    "target": "es5",
    "typeRoots": [
        "./node_modules"
    ],
    "outFile": "./build/index.js",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
    }
  }
  ~~~

  Create a new file in the root directory, called **main.ts** with the following content:
  ~~~ts
  function InitModule (ctx: nkruntime.Context, logger: nkruntime.Logger, nk: nkruntime.Nakama, initializer: nkruntime.Initializer) {
    initializer.registerRpc('createleaderboard', createLeaderboard);
    // initializer.registerRpc('create-leaderboard', createLeaderboard);
    logger.info("Hello World!");
    logger.info("rpc registered successfully");
  }
  ~~~
  This file will serve as the main entry point for our server runtime code. It also registers an RPC method to handle the creation of a Leaderbord.

  Now we're gonna create another file called **createleaderboard.ts** in the root of our directory. It will contain the needed code to create a Leaderboard:
  ~~~ts
  function createLeaderboard(ctx: nkruntime.Context, logger: nkruntime.Logger, nk: nkruntime.Nakama): string {
    let id = 'newLeaderboard';
    let authoritative = false;
    let sort = nkruntime.SortOrder.DESCENDING;
    let operator = nkruntime.Operator.BEST;
    let reset = '0 0 * * 1';
    let metadata = {
    weatherConditions: 'rain',
    };
    try {
        nk.leaderboardCreate(id, authoritative, sort, operator, reset, metadata, false); // 86400 seconds for DAILY
        logger.info(`Leaderboard ${id} created successfully.`);
        return JSON.stringify({ success: true });
    } catch (error) {
        logger.error(`Failed to create leaderboard ${id}: ${error}`);
        return JSON.stringify({ success: false });
        
    }
  }
  ~~~

- **Setting up the server with Docker:**

  now that we have all the dependencies installed, it's time to run the server using Docker which is the easiest way of deploying the local Nakama server. To do so:

  **First** Create a file in the root directory of the project called **"local.yml"**:
  ~~~yml
  logger:
    level: "DEBUG"
  runtime:
    js_entrypoint: "build/index.js"
  ~~~

  **Next** Let's create our **Dockerfile**. create a file called **"Dockerfile"** in the same root directory:
  ~~~Dockerfile
    FROM node:alpine AS node-builder

    WORKDIR /backend

    COPY package*.json .
    RUN npm install

    COPY tsconfig.json .
    COPY *.ts .
    RUN npx tsc

    FROM heroiclabs/nakama:3.22.0

    COPY --from=node-builder /backend/build/*.js /nakama/data/modules/build/
    COPY local.yml /nakama/data/
  ~~~

  Nowe let's create a file called **"docker-compose.yml"** in the root directory:
  ~~~yml
  version: '3'
  services:
    postgres:
      command: postgres -c shared_preload_libraries=pg_stat_statements -c pg_stat_statements.track=all
      container_name: template_nk_postgres
      environment:
        - POSTGRES_DB=nakama
        - POSTGRES_PASSWORD=localdb
      expose:
        - "8080"
        - "5432"
      image: postgres:12.2-alpine
      ports:
        - "5432:5432"
        - "8080:8080"
      healthcheck:
        test: ["CMD", "pg_isready", "-U", "postgres", "-d", "nakama"]
        interval: 3s
        timeout: 3s
        retries: 5
      volumes:
        - data:/var/lib/postgresql/data
  
    tf:
      image: tensorflow/serving
      container_name: template_tf
      environment:
        - MODEL_NAME=ttt
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:8501/"]
        interval: 10s
        timeout: 5s
        retries: 5
      ports:
        - "8501"
      volumes:
        - ./model:/models/ttt
      restart: unless-stopped
  
    nakama:
      build: .
      container_name: template_nk_backend
      depends_on:
        - postgres
        - tf
      entrypoint:
        - "/bin/sh"
        - "-ecx"
        - >
          /nakama/nakama migrate up --database.address postgres:localdb@postgres:5432/nakama?sslmode=disable &&
          exec /nakama/nakama --config /nakama/data/local.yml --database.address postgres:localdb@postgres:5432/nakama?sslmode=disable
      expose:
        - "7349"
        - "7350"
        - "7351"
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:7350/"]
        interval: 10s
        timeout: 5s
        retries: 5
      links:
        - "postgres:db"
      ports:
        - "7349:7349"
        - "7350:7350"
        - "7351:7351"
      restart: unless-stopped
  
  volumes:
    data:
  ~~~

- **Run the server:**
  In the terminal, run the following command:
  ~~~BASH
  docker compose up
  ~~~
  This will begin downloading and building the Docker images and run the server

- **Test the server:**

  Open a browser and head up to [localhost](http://localhost:7351/).

  This will open the Nakama Console which we can log in with default credintials which are **admin** and **password**.

  From the **API EXPLOREER** section, you can have access to the create leaderboard RPC function and create a leaderboard, alongside with many other built-in functions and APIs.

## More Info ##
  for more information see the official docs **[Here](https://heroiclabs.com/docs/nakama/server-framework/typescript-runtime/)**

## Next Step: ##
  [integrating Nakama into your project](https://github.com/Karen-Najafzadeh/to-learn-list/blob/main/Nakama/Integration/README.md)
