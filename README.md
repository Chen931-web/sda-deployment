Introduction
---

This is a work in progress. This repository tries to deploy the SDA stack, all in one place.
We had a breaking change, instead of relying on the starter-kit repositories, we now depend on the original SDA repository.

Modifications applied by this repository
---
1. Add the dependencies to SDA and REMS with `git submodule add ...`. The submodules are included and configured in [docker-compose.yml](docker-compose.yml).
2. [SDA](https://github.com/neicnordic/sensitive-data-archive/)
   * Created a [.env](configuration/sensitive-data-archive/.env) that specifies the version of SDA to use
   * Created a [rems-client.yaml](configuration/aai-mock/clients/rems-client.yaml). The whole configuration folder is added to the submodule default configuration with the service `aai-mock-config-init` in [sensitive-data-archive.override.yml](sensitive-data-archive.override.yml).
3. [REMS](https://github.com/CSCfi/rems)
   * Move the initialization of the database in [rems.override.yml](rems.override.yml) in the service `rems-migrate`, instead of the imperative `docker-compose run --rm -e CMD="migrate" app`
   * Created a [config.edn](configuration/rems/config.edn) with `oidc` authentication instead of `fake`.
   * Add `extra_hosts: "localhost:host-gateway"`  for the service `app` so it can reach `lsaai-mock`. 

Diagram
---
[sda-s3-dependency-diagram.html](sda-s3-dependency-diagram.html)

Using the stack
---
1. [Administering REMS](rems/docs/installing-upgrading.md)
   * Login into REMS at http://localhost:3000/ so the user is created in the REMS database. Users are specified in the [aai-mock configuration](sensitive-data-archive/.github/integration/sda/aai-mock/userinfos).
   * Give the owner role to the user. 
   ```
   docker exec sda-deployment-app-1 java -Drems.config=/rems/config/config.edn -jar rems.jar grant-role owner testu@lifescience-ri.eu
   ```
   * Restart REMS, and the user can now access the [administration UI](http://localhost:3000/administration/catalogue-items).

Troubleshooting wiki
---
* Compiling the SDA image
  * Should we run the build-all command?
  * Or should we replace the image parameter from PR${PR_NUMBER} to ${PR_NUMBER} so we can use "v4.0.2" ?

