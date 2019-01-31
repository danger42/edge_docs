{% extends "_base.html" %} {% block title %}Overview of Node.js on Apigee Edge{% endblock %} {% block body %}

You can host your [Node.js](https://nodejs.org/en/about/) APIs on Apigee Edge, where
API proxies can call them as target services. 

Node.js support on Apigee Edge allows you to build customized
backend services for your proxies in Node.js. Essentially, Apigee Edge hosts your Node.js code in
a runtime environment that is scoped to an Apigee organization and environment.
API proxies that are deployed to the same organization and environment can call
HTTP-based APIs exposed by the Node.js application.


## Node.js deployment options

Apigee provides two options for hosting Node.js code: [Hosted Targets](#about_hosted_targets_deployment)
and the [traditional Node.js Edge deployment environment](#about_traditional_nodejs_edge_deployment).

### Deploy to Hosted Targets

[Hosted Targets](/api-platform/hosted-targets/hosted-targets-overview) allows Node.js
applications to run in a native environment that does not depend on any Apigee-specific
run-time technology. It provides you a native node runtime, so you can use your
favorite node packages.

You can debug and test your app locally before deploying it
and be assured that the deployed version will work exactly as it does locally. At
deployment time, you can choose any application runtime version for running your
application in Hosted Targets. For example, you might specifically choose to run a
Node.js app in a v8.10.0 environment or any other version.


The following figure illustrates the basic architecture:

<p><img src="/api-platform/images/nodejs-hosted-targets-overview.png" width="80%" /></p>

The Hosted Targets runtime environment is scoped to an Apigee organization and
environment and can serve as the target for any API proxies that are deployed to
that same organization and environment.

### Deploy to the traditional embedded Node.js environment

<aside class="warning"><p><strong>Deprecated feature:</strong> The traditional Node.js support on
  Apigee Edge Cloud, as described in this topic, is deprecated. For details on the deprecation, see the <a href="/release/deprecated-features">Deprecations
  and retirements page</a>. This feature will be retired in the future.</p>

  <p>If you are on Apigee Cloud, Apigee recommends that
  you use <a href="/api-platform/hosted-targets/hosted-targets-overview">Hosted Targets</a> for new
  Node.js development and deployment to Apigee Edge. For information on migrating existing proxies that use Apigee's traditional
    Node.js deployment approach, see <a href="/api-platform/hosted-targets/hosted-targets-tutorials#migrating-an-existing-nodejs-proxy-to-a-hosted-targets-proxy">Migrating an existing Node.js proxy to a Hosted Targets proxy</a>.</p>
</aside>

The [traditional approach to deploying Node.js to Edge](/api-platform/nodejs/overview-nodejs-apigee-edge)
relies internally on an open-source bridge application called (<a href="https://github.com/apigee/trireme">Trireme</a>)
and a JavaScript interpreter called (<a href="https://en.wikipedia.org/wiki/Rhino_(JavaScript_engine)">Rhino</a>).
These components allow Node.js code to execute directly in the Edge Java runtime
environment.

<p><img src="/api-platform/images/nodejs-edge-overview.png" width="80%"></p>

The traditional, embedded Node.js  runtime environment is scoped to an Apigee organization and
environment and can serve as the target for any API proxies that are deployed to
that same organization and environment. 

With this approach, you can use a supporting
module called [apigee-access](/api-platform/nodejs/using-apigee-access) that lets you access API proxy flow variables, caches,
key value maps, and quotas from within your Node.js application code.


## Choosing a Node.js approach

Apigee recommends that you consider using Hosted Targets. Node.js apps
deployed to Hosted Targets do not depend on any Apigee-specific Node.js run-time
technology. Your Node.js app will run in Hosted Targets exactly as it runs in your
local development environment.

Furthermore, Apigee support for the traditional Node.js Edge deployment is has limitations:

* Only an older version of Node.js (0.10.32) is supported.
* There are subtle differences in behavior between the standard Node.js environment
  and the Trireme/Rhino environment.
* Debugging Node.js apps after they are deployed to Edge is difficult.

Currently, Hosted Targets does not support the use of [apigee-access](/api-platform/nodejs/using-apigee-access) to access resources in the proxy flow context, such as flow variables.

To learn more about traditional Node.js Edge deployment, see
[Traditional Node.js deployment on Edge](/api-platform/nodejs/overview-nodejs-apigee-edge).

## Use cases

Common use cases for Node.js on Edge include:

* Building highly customized standalone HTTP-based APIs and backend services.

* Solving complex and mobile optimization problems with the advantage of a scriptable
  target endpoint.

* Building composite services and mashups.

* Rapidly developing prototypes of new APIs using frameworks like [Express](https://expressjs.com/).

## Next step

To decide which Node.js deployment approach is best for you, read the overviews: 

* [Hosted Targets overview](/api-platform/hosted-targets/hosted-targets-overview). 
* [Traditional Node.js deployment on Edge overview](/api-platform/nodejs/overview-nodejs-apigee-edge).


{% endblock %}
