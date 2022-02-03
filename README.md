# Deploy Redwood on Layer0 with Limelight

Layer0 by Limelight is an Edge Compute Platform with over 130 global points of presence. Limelight operates its own private network with more than 70+ terabits per second of global egress capacity. Data is prefetched and streamed into the browser at a 95% cache hit rate by Layer0 before the user requests it.

<p align="center">
  <img
    src="https://assets-global.website-files.com/5ec129d839c03647b43dbd41/619459e884ec7ae74d923da8_I6iG8tVXinoz29x52oRnHeDYe8WmpuND7AdmwC9-c64qzxJVkN8fpn5Vlogr7W67K-peNtFsLvmBWDWuzlNJ1VnEXM3Iso4ijaf8tXlxd0Mmmk3LrBTLKXUCj_GJASq3WsIbksyJ.jpeg"
    alt="Cache hit rate comparison graphic showing 95 percent cache hit rate for Layer0 versus an 11 percent rate for traditional CDNs"
    width="749"
    height="506"
  >
</p>

In addition to hosting your static assets, Layer0 also includes edge functions with [EdgeJS](https://www.layer0.co/edgejs), a framework agnostic, performant, and declarative JavaScript-based edge configuration language.

## Outline

* [Start Development Server](#start-development-server)
  * [Start Layer0 Development Server](#start-layer0-development-server)
* [Build Project for Deployment](#build-project-for-deployment)
  * [Run Production Simulation of Build](#run-production-simulation-of-build)
* [Deploy Project to Layer0](#deploy-project-to-layer0)
  * [Test Deployed Endpoint](#test-deployed-endpoint)
* [Layer0 Configuration](#layer0-configuration)
  * [Redwood Custom Connector](#redwood-custom-connector)
  * [Layer0 Routing Configuration](#layer0-routing-configuration)

## Start Development Server

```terminal
yarn rw dev
```

Open [localhost:8910](http://localhost:8910) to see the web app.

![home-page-localhost-8910](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ijenahgpkabvms5qfbpo.png)

### Start Layer0 Development Server

Alternatively, you can use [`0 dev`](https://docs.layer0.co/guides/cli#section_dev) instead of `yarn rw dev` to start the Layer0 dev server *and* the Redwood development server. Layer0 is on port 3000 and Redwood is started on 3001 by forwarding the port.

```terminal
yarn 0 dev
```

## Build Project for Deployment

[`0 build`](https://docs.layer0.co/guides/cli#section_build) does a Redwood build of both the `web` and `api` sides as well as a Layer0 build for copying the necessary build assets. Make sure to run this command before starting the development server.

```bash
yarn 0 build
```

### Run Production Simulation of Build

[`0 run -p`](https://docs.layer0.co/guides/cli#section_run) runs the build simulating production mode.

```bash
yarn 0 run -p
```

This starts Redwood's `apiServerHandler` on Layer0's production port and sets the `apiRootPath` to whatever is defined in `redwood.toml`. This means any API request that comes in starts up that server to handle the request.

## Deploy Project to Layer0

[`0 deploy`](https://docs.layer0.co/guides/cli#section_deploy) builds and deploys your site on Layer0.

```bash
yarn 0 deploy --site redwood-layer0
```

```
***** Deployment Complete ******************************************************
*                                                                              *
*  🖥  Layer0 Developer Console:                                                *
*  https://app.layer0.co/anthony-campolo/redwood-layer0/env/default/builds/26  *
*                                                                              *
*  🌎 Website:                                                                 *
*  https://anthony-campolo-redwood-layer0-default.layer0-limelight.link        *
*                                                                              *
********************************************************************************
```

### Test Deployed Endpoint

```bash
curl \
  --request POST \
  --header 'content-type: application/json' \
  --url 'https://anthony-campolo-redwood-layer0-default.layer0-limelight.link/api/graphql' \
  --data '{"query":"{ redwood { version } }"}'
```

```json
{
  "data":{
    "redwood":{
      "version":"0.41.0"
    }
  }
}
```

## Layer0 Configuration

The `layer0.config.js` configuration file specifies the location of the files defining the connector.

### Redwood Custom Connector

This project is configured to use a Layer0 [custom connector](https://docs.layer0.co/guides/connectors).

```js
// layer0.config.js

module.exports = {
  connector: '@layer0/redwood',
}
```

### Layer0 Routing Configuration

The [`routes.js`](https://docs.layer0.co/guides/routing#section_configuration) file in the root of your project is used to define routes for Layer0.

```js
// routes.js

import { Router } from '@layer0/core'
import { redwoodRoutes } from '@layer0/redwood'

export default new Router().use(redwoodRoutes)
```