# HAPI Fhir Server


To (re-)build an image you need to run `docker build` and provide a tag, fhir version and data directory (unless you are building the empty servers)
See **build** examples below.

To run an image you should do `docker run -p {PORT}:8080 smartonfhir/{TAG}` replacing the {PORT} and {TAG} with whatever you need.

To update an image use `docker push smartonfhir/{TAG}`.

To add data to an image:
1. Start an existing image with external folder mounted. That external folder will contain the data that the running image will use.
   For example, if we start with the `hapi:r3-smart` image we could do something like:
   ```sh
   docker run -it -p 8080:8080 -v /absolute/path/to/my/local/data/folder:/data smartonfhir/hapi:r3-empty
   ```
   In this case the image will start a STU3 server but ignore it's pre-built data and use the external folder. If the external data folder is empty the server will have 0 patients...

2. Insert some data using the FHIR HTTP API. The new data will appear in the mounted external folder and persist there even after the container is stopped.
3. Stop the container (Ctrl+C or docker ps and docker stop {id})
4. Re-build the image with the new data:
   ```sh
   docker build -t smartonfhir/hapi:my-tag --build-arg FHIR=dstu3 --build-arg DATA=/absolute/path/to/my/local/data/folder .
   ```
5. Test the image:
   ```sh
   docker run -it -p 8080:8080 smartonfhir/hapi:my-tag
   ```
6. Upload (update) the image:
   ```sh
   docker push smartonfhir/hapi:my-tag
   ```

### Examples
The build command uses `--build-arg` to pass build-time arguments and this way
perform different builds. The supported build-args are:
- `DATA` - the path to the database data. If not provided the server will use an empty database.
- `FHIR` - the fhir version. One of `dstu3`, `dstu2`.
- `CLI_OPTS` - CLI args passed to java. Defaults to `-Xmx900m` but the memory might have to be increased for bigger databases.

- R5 EMPTY
    - **Not working yet.** Produces errors: Despite documentation, r5 option is unsupported by `run-server`
    - **`build:`** `docker build -t hapi-r5:empty --build-arg FHIR=r5 .` // docker build -t hapi-r5:empty -f Dockerfile.r5 .
    - **`run  :`** `docker run -it -p 8080:8080 hapi-r5:empty`
    - **`push :`** `docker push smartonfhir/hapi-r5:empty`

- R4 EMPTY
    - **`build:`** `docker build -t smartonfhir/hapi:r4-empty --build-arg FHIR=r4 --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r4-empty`
    - **`push :`** `docker push smartonfhir/hapi:r4-empty`

- R4 SAMPLE


- R4 SYNTHEA
    - **`build:`** `docker build -t smartonfhir/hapi:r4-synthea --build-arg FHIR=r4 --build-arg DATA=./databases/r4/synthea --build-arg CLI_OPTS=-Xmx1024m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r4-synthea`
    - **`push :`** `docker push smartonfhir/hapi:r4-synthea`

- R3 EMPTY
    - **`build:`** `docker build -t smartonfhir/hapi:r3-empty --build-arg FHIR=dstu3 --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r3-empty`
    - **`push :`** `docker push smartonfhir/hapi:r3-empty`

- R3 SMART
    - **`build:`** `docker build -t smartonfhir/hapi:r3-smart --build-arg FHIR=dstu3 --build-arg DATA=./databases/r3/smart --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r3-smart`
    - **`push :`** `docker push smartonfhir/hapi:r3-smart`

- R3 SYNTHEA
    - **`build:`** `docker build -t smartonfhir/hapi:r3-synthea --build-arg FHIR=dstu3 --build-arg DATA=./databases/r3/synthea --build-arg CLI_OPTS=-Xmx1024m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r3-synthea`
    - **`push :`** `docker push smartonfhir/hapi:r3-synthea`

- R3 SAMPLE
    - **`build:`** `docker build -t smartonfhir/hapi:r3-sample --build-arg FHIR=dstu3 --build-arg DATA=./databases/r3/sample --build-arg CLI_OPTS=-Xmx1024m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r3-sample`
    - **`push :`** `docker push smartonfhir/hapi:r3-sample`

- R3 FULL
    - **`build:`** `docker build -t smartonfhir/hapi:r3 --build-arg FHIR=dstu3 --build-arg DATA=./databases/r3/full --build-arg CLI_OPTS=-Xmx2048m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r3`
    - **`push :`** `docker push smartonfhir/hapi:r3`

- R2 EMPTY
    - **`build:`** `docker build -t smartonfhir/hapi:r2-empty --build-arg FHIR=dstu2 --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r2-empty`
    - **`push :`** `docker push smartonfhir/hapi:r2-empty`

- R2 SMART
    - **`build:`** `docker build -t smartonfhir/hapi:r2-smart --build-arg FHIR=dstu2 --build-arg DATA=./databases/r2/smart --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r2-smart`
    - **`push :`** `docker push smartonfhir/hapi:r2-smart`

- R2 SYNTHEA
    - **`build:`** `docker build -t smartonfhir/hapi:r2-synthea --build-arg FHIR=dstu2 --build-arg DATA=./databases/r2/synthea --build-arg CLI_OPTS=-Xmx1024m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r2-synthea`
    - **`push :`** `docker push smartonfhir/hapi:r2-synthea`

- R2 SAMPLE
    - **`build:`** `docker build -t smartonfhir/hapi:r2-sample --build-arg FHIR=dstu2 --build-arg DATA=./databases/r2/sample --build-arg CLI_OPTS=-Xmx1024m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r2-sample`
    - **`push :`** `docker push smartonfhir/hapi:r2-sample`

- R2 FULL
    - **`build:`** `docker build -t smartonfhir/hapi:r2 --build-arg FHIR=dstu2 --build-arg DATA=./databases/r2/full --build-arg CLI_OPTS=-Xmx2048m --squash .`
    - **`run  :`** `docker run -it -p 8080:8080 smartonfhir/hapi:r2`
    - **`push :`** `docker push smartonfhir/hapi:r2`



### WARNING!
The folder /databases/r2/full/target contains some files that are bigger then the GitHub restrictions. These files cannot not be uploaded and you will not get anything when you clone or pull the project. Instead you can do the following:
1. The biggest chunk of the data is already available so start with it by copying /databases/r2/synthea/target to /databases/r2/full/target
2. Start the server - `docker run -it -p 8080:8080 -v /absolute/path/to/project/databases/r2/full/target:/data smartonfhir/hapi:r2-empty`
3. Insert the dstu2-smart patients
4. Insert the dstu2-custom data
