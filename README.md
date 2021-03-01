# Snowpack Cache Bug

Reproducing an error somewhere between Snowpack, Node, and Docker.

Running Snowpack in a container via Docker on a Linux host I get this error:

```
[snowpack] Cache Error: Error: EACCES: permission denied, mkdir '/root/.cache/snowpack/build-cache-2.7/tmp'
```

To reproduce:

1. Clone this repo
2. Install Snowpack
3. Run the dev server
4. Load the page

The error is triggered by the page load.

For more detailed instructions:

## 1. Clone this repo

```
git clone git@github.com:Jwashton/turbo-octo-robot.git
```

## 2. Install Snowpack

Snowpack is already setup as a dependency, so all you need to do is run
`npm install`.  This Docker command should do the trick:

```
docker run -it --rm -v "${PWD}":/app:Z -w="/app" node:15 npm i
```

Since that's a lot of typing that I want to get consistently right, I provided
a helper script that calls Docker with the appropriate flags.  This should do
the same as above:

```
bin/run npm i
```

## 3. Run the Dev Server

There is an HTML file in the project. Let's serve it. I've added port-mapping
flags to the Docker command so we'll be able to do step 4 in a minute.

```
docker run -it --rm -p=8080:8080 -v "${PWD}":/app:Z -w="/app" node:15-alpine npx snowpack dev
```

Again for convenience, and to reduce typing errors:

```
bin/run npx snowpack dev
```

## 4. Load the page

Go to `localhost:8080` in your browser, and you should see the simple page
served just fine. Looking back at the dev-server log I now see our unfriendly
error on the last line:

```
snowpack

  http://localhost:8080 • http://172.17.0.3:8080
  Server started in 106ms.

▼ Console

[snowpack] Hint: run "snowpack init" to create a project config file. Using defaults...
[snowpack] Nothing to install.
[snowpack] Cache Error: Error: EACCES: permission denied, mkdir '/root/.cache/snowpack/build-cache-2.7/tmp'
```
